# 🎮 新南英語大冒險 (Xinnan English Adventure)

這是一款專為國小五年級學生設計的平板單機多人網頁遊戲。結合了「前測評量」與「康軒 Wonder World 5」的進度，讓孩子們透過大富翁賽跑的機制，在歡樂的音效與音樂中複習英文。

## ✨ 遊戲特色
1. **三人同樂**：支援 1-3 人共用一台平板輪流答題。
2. **平板最佳化 (PWA)**：支援加入主畫面全螢幕運行，防誤觸縮放，直式橫式自適應。
3. **沉浸式音效**：內建 Web Audio API，包含輕快的木琴背景音樂、魔法擲骰聲、金幣答對聲與滑稽答錯聲。
4. **不重複抽題**：題目抽完前絕不重複，自動循環洗牌。

## 📱 如何使用與部署

**方法一：直接在平板遊玩 (推薦 iOS/Android)**
1. 將下方完整的 HTML 程式碼複製。
2. 存成一個副檔名為 `.html` 的檔案（例如 `english_game.html`）。
3. 將檔案傳送到平板，使用 Safari 或 Chrome 瀏覽器打開。
4. **(重要)** 點擊瀏覽器的「分享」>「加入主畫面」。從主畫面點開即可享受無邊框的全螢幕 APP 體驗！

**方法二：嵌入 Google Sites (協作平台)**
1. 在 Google Sites 編輯頁面，選擇「嵌入」>「嵌入程式碼」。
2. 將下方 HTML 程式碼完整貼上。
3. 發佈網站。
4. **(重要)** 提醒學生：進入畫面後，先用手指點一下畫面的空白處，再按下「START」按鈕，音樂就會順利播放囉！

---

## 💻 完整遊戲程式碼 (HTML)

請複製以下區塊內的所有程式碼：

```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <!-- 新增：支援加入主畫面變成全螢幕 Web App (iOS/Android) -->
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="mobile-web-app-capable" content="yes">
    <title>新南英語大冒險</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Nunito:wght@400;700;900&family=Noto+Sans+TC:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Nunito', 'Noto Sans TC', sans-serif;
            background-color: #f0f9ff;
            background-image: radial-gradient(#bae6fd 1px, transparent 1px);
            background-size: 20px 20px;
            overflow: hidden; /* 平板版強制隱藏外部捲軸，帶來 App 體驗 */
            touch-action: manipulation; /* Prevent double-tap zoom on tablets */
            -webkit-user-select: none; /* 防止平板上長按選取文字 */
            user-select: none;
        }
        
        .track-bg {
            background: repeating-linear-gradient(
                90deg,
                rgba(255,255,255,0.5),
                rgba(255,255,255,0.5) 40px,
                rgba(200,200,200,0.2) 40px,
                rgba(200,200,200,0.2) 80px
            );
        }

        .character {
            transition: all 0.5s cubic-bezier(0.34, 1.56, 0.64, 1);
            will-change: transform, left; /* 提升平板動畫效能 */
        }

        @keyframes bounce {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-10px); }
        }

        .active-turn {
            animation: bounce 1s infinite;
            box-shadow: 0 0 15px 5px rgba(250, 204, 21, 0.6);
        }

        .modal-overlay {
            background-color: rgba(0, 0, 0, 0.6);
            backdrop-filter: blur(4px);
        }

        /* 骰子動畫 */
        @keyframes rollDice {
            0% { transform: rotate(0deg) scale(1); }
            25% { transform: rotate(90deg) scale(1.2); }
            50% { transform: rotate(180deg) scale(0.8); }
            75% { transform: rotate(270deg) scale(1.1); }
            100% { transform: rotate(360deg) scale(1); }
        }
        .rolling {
            animation: rollDice 0.6s ease-in-out;
        }

        /* 慶祝動畫 */
        @keyframes popIn {
            0% { transform: scale(0); opacity: 0; }
            80% { transform: scale(1.1); opacity: 1; }
            100% { transform: scale(1); opacity: 1; }
        }
        .pop-in {
            animation: popIn 0.5s forwards;
        }
    </style>
</head>
<body class="min-h-[100dvh] w-full flex flex-col items-center justify-center text-gray-800 selection:bg-blue-300">

    <!-- 遊戲首頁 -->
    <div id="start-screen" class="flex flex-col items-center justify-center w-full flex-grow p-4 md:p-8">
        <div class="bg-white p-6 md:p-10 rounded-3xl shadow-2xl border-4 border-blue-400 text-center max-w-xl w-full pop-in overflow-y-auto max-h-full flex flex-col">
            <h1 class="text-4xl md:text-5xl font-black text-blue-600 mb-2">新南英語大冒險</h1>
            <p class="text-xl text-gray-500 mb-8 font-bold">五年級英語挑戰賽</p>
            
            <div class="space-y-4 mb-8 text-left">
                <div>
                    <label class="block text-gray-700 font-bold mb-1">玩家 1 (紅隊)</label>
                    <input type="text" id="p1-name" value="Player 1" class="w-full px-4 py-3 border-2 border-red-300 rounded-xl focus:outline-none focus:border-red-500 text-lg text-red-600 font-bold">
                </div>
                <div>
                    <label class="block text-gray-700 font-bold mb-1">玩家 2 (藍隊)</label>
                    <input type="text" id="p2-name" value="Player 2" class="w-full px-4 py-3 border-2 border-blue-300 rounded-xl focus:outline-none focus:border-blue-500 text-lg text-blue-600 font-bold">
                </div>
                <div>
                    <label class="block text-gray-700 font-bold mb-1">玩家 3 (綠隊)</label>
                    <input type="text" id="p3-name" value="Player 3" class="w-full px-4 py-3 border-2 border-green-300 rounded-xl focus:outline-none focus:border-green-500 text-lg text-green-600 font-bold">
                </div>
            </div>
            
            <button onclick="startGame()" class="w-full bg-gradient-to-r from-yellow-400 to-orange-500 hover:from-yellow-500 hover:to-orange-600 text-white font-black text-2xl md:text-3xl py-4 px-8 rounded-2xl shadow-lg transform transition active:scale-95 mt-auto">
                開始遊戲 START!
            </button>
        </div>
    </div>

    <!-- 遊戲主畫面 -->
    <div id="game-screen" class="hidden flex-col w-full h-[100dvh] max-w-7xl mx-auto p-2 md:p-6 gap-2 md:gap-4">
        
        <!-- 標題與狀態 -->
        <div class="flex justify-between items-center bg-white p-3 md:p-4 rounded-2xl shadow-md border-2 border-blue-200 flex-shrink-0">
            <h2 class="text-xl md:text-2xl font-black text-blue-600 hidden sm:block">新南英語大冒險</h2>
            <div class="text-center flex-grow">
                <span class="text-lg md:text-xl font-bold text-gray-600">現在輪到：</span>
                <span id="current-turn-display" class="text-2xl md:text-3xl font-black text-red-500 ml-1 md:ml-2">Player 1</span>
            </div>
            <button id="music-toggle" onclick="toggleMusic()" class="ml-2 bg-yellow-400 hover:bg-yellow-500 text-white w-10 h-10 md:w-12 md:h-12 rounded-full shadow-md text-xl md:text-2xl flex items-center justify-center transition-colors">
                🎵
            </button>
        </div>

        <!-- 賽道區 (針對平板直式/橫式優化為彈性伸縮) -->
        <div class="flex-grow flex flex-col justify-evenly bg-white p-2 md:p-6 rounded-3xl shadow-xl border-4 border-gray-200 relative overflow-hidden min-h-0 my-2">
            <!-- 終點線標示 -->
            <div class="absolute right-4 md:right-8 top-0 bottom-0 w-8 md:w-12 bg-yellow-300 border-l-4 border-dashed border-gray-800 z-0 flex items-center justify-center">
                <span style="writing-mode: vertical-rl;" class="font-black text-xl md:text-3xl text-gray-800 tracking-widest shadow-sm">FINISH</span>
            </div>

            <!-- Player 1 Track -->
            <div class="relative w-full flex-1 max-h-24 min-h-[3.5rem] bg-red-50 rounded-xl border-2 border-red-200 track-bg flex items-center z-10 px-2 md:px-4 my-1 md:my-2">
                <div id="p1-avatar" class="character w-10 h-10 sm:w-14 sm:h-14 md:w-16 md:h-16 bg-red-500 rounded-full border-4 border-white shadow-md flex items-center justify-center text-white font-bold text-base md:text-2xl z-20 absolute left-2 active-turn" style="left: 2%;">P1</div>
                <div class="absolute right-12 md:right-16 text-red-300 font-bold text-lg md:text-xl hidden sm:block">Goal</div>
            </div>

            <!-- Player 2 Track -->
            <div class="relative w-full flex-1 max-h-24 min-h-[3.5rem] bg-blue-50 rounded-xl border-2 border-blue-200 track-bg flex items-center z-10 px-2 md:px-4 my-1 md:my-2">
                <div id="p2-avatar" class="character w-10 h-10 sm:w-14 sm:h-14 md:w-16 md:h-16 bg-blue-500 rounded-full border-4 border-white shadow-md flex items-center justify-center text-white font-bold text-base md:text-2xl z-20 absolute left-2" style="left: 2%;">P2</div>
                <div class="absolute right-12 md:right-16 text-blue-300 font-bold text-lg md:text-xl hidden sm:block">Goal</div>
            </div>

            <!-- Player 3 Track -->
            <div class="relative w-full flex-1 max-h-24 min-h-[3.5rem] bg-green-50 rounded-xl border-2 border-green-200 track-bg flex items-center z-10 px-2 md:px-4 my-1 md:my-2">
                <div id="p3-avatar" class="character w-10 h-10 sm:w-14 sm:h-14 md:w-16 md:h-16 bg-green-500 rounded-full border-4 border-white shadow-md flex items-center justify-center text-white font-bold text-base md:text-2xl z-20 absolute left-2" style="left: 2%;">P3</div>
                <div class="absolute right-12 md:right-16 text-green-300 font-bold text-lg md:text-xl hidden sm:block">Goal</div>
            </div>
        </div>

        <!-- 互動控制區 -->
        <div class="bg-white p-3 md:p-4 rounded-2xl shadow-md border-2 border-gray-200 flex justify-center items-center gap-4 md:gap-6 flex-shrink-0">
            <div id="dice-display" class="w-16 h-16 md:w-20 md:h-20 bg-gray-100 rounded-2xl border-4 border-gray-300 flex items-center justify-center text-3xl md:text-4xl font-black text-gray-700 shadow-inner">
                ?
            </div>
            <button id="roll-btn" onclick="rollDiceAndPlay()" class="bg-indigo-500 hover:bg-indigo-600 text-white font-black text-xl md:text-2xl py-3 md:py-4 px-6 md:px-10 rounded-2xl shadow-lg transform transition active:scale-95">
                擲骰子抽題! (Roll)
            </button>
        </div>
    </div>
    
    <!-- 題目彈出視窗 -->
    <div id="question-modal" class="hidden fixed inset-0 modal-overlay z-50 flex items-center justify-center p-4">
        <div class="bg-white rounded-3xl shadow-2xl border-4 md:border-8 border-indigo-400 w-full max-w-3xl overflow-hidden pop-in flex flex-col max-h-[90dvh]">
            <div class="bg-indigo-500 text-white p-3 md:p-4 text-center flex-shrink-0">
                <h3 class="text-xl md:text-2xl font-bold flex items-center justify-center gap-2">
                    <span id="question-player-name" class="text-yellow-300 font-black">Player</span> 的挑戰！
                    <span class="bg-white text-indigo-600 px-3 py-1 rounded-full text-sm md:text-base whitespace-nowrap">前進 <span id="pending-steps">0</span> 步</span>
                </h3>
            </div>
            
            <div class="p-4 md:p-8 overflow-y-auto overscroll-contain flex-1 min-h-0">
                <p id="question-text" class="text-xl md:text-3xl font-bold text-gray-800 mb-6 md:mb-8 text-center leading-relaxed select-text">
                    Here is the question?
                </p>
                
                <div id="options-container" class="grid grid-cols-1 md:grid-cols-2 gap-3 md:gap-4">
                    <!-- 按鈕由 JS 生成 -->
                </div>
            </div>
        </div>
    </div>

    <!-- 答題結果回饋視窗 -->
    <div id="feedback-modal" class="hidden fixed inset-0 modal-overlay z-[60] flex items-center justify-center p-4">
        <div id="feedback-box" class="bg-white p-6 md:p-8 rounded-3xl shadow-2xl text-center transform transition-transform pop-in border-4 w-full max-w-sm">
            <div id="feedback-icon" class="text-6xl md:text-7xl mb-4">✅</div>
            <h2 id="feedback-title" class="text-3xl md:text-4xl font-black mb-2">答對了！</h2>
            <p id="feedback-msg" class="text-lg md:text-xl text-gray-600 font-bold mb-6">前進 N 步</p>
            <button onclick="closeFeedback()" class="bg-gray-800 hover:bg-gray-700 text-white font-bold text-lg md:text-xl py-3 px-8 rounded-xl w-full">
                繼續 (Continue)
            </button>
        </div>
    </div>

    <!-- 遊戲結束視窗 -->
    <div id="winner-modal" class="hidden fixed inset-0 modal-overlay z-[70] flex items-center justify-center p-4">
        <div class="bg-gradient-to-b from-yellow-300 to-yellow-500 p-8 md:p-10 rounded-3xl shadow-2xl text-center border-4 md:border-8 border-white pop-in max-w-md w-full">
            <div class="text-7xl md:text-8xl mb-4">🏆</div>
            <h2 class="text-4xl md:text-5xl font-black text-white drop-shadow-md mb-2">WINNER!</h2>
            <h3 id="winner-name" class="text-3xl md:text-4xl font-bold text-gray-800 mb-8">Player Name</h3>
            <button onclick="location.reload()" class="bg-white text-yellow-600 font-black text-xl md:text-2xl py-4 px-8 rounded-2xl shadow-lg hover:bg-gray-100 transform transition active:scale-95 w-full">
                再玩一次 (Play Again)
            </button>
        </div>
    </div>

    <script>
        // --- 音效與背景音樂設定 (針對 iPad 終極強化版) ---
        let audioCtx = null;
        let isMusicPlaying = false;
        let bgmInterval;
        let beatCount = 0;
        let audioUnlocked = false;

        function initAndUnlockAudio() {
            try {
                if (!audioCtx) {
                    const AudioContext = window.AudioContext || window.webkitAudioContext;
                    if (AudioContext) audioCtx = new AudioContext();
                }
                if (!audioCtx) return false;

                if (audioCtx.state === 'suspended') {
                    audioCtx.resume();
                }

                if (!audioUnlocked) {
                    const buffer = audioCtx.createBuffer(1, 1, 22050);
                    const source = audioCtx.createBufferSource();
                    source.buffer = buffer;
                    source.connect(audioCtx.destination);
                    if (source.start) source.start(0);
                    else if (source.noteOn) source.noteOn(0);
                    audioUnlocked = true;
                }
                return true;
            } catch (e) {
                console.warn("音效初始化失敗:", e);
                return false;
            }
        }

        const globalUnlock = () => {
            if (audioUnlocked) return;
            initAndUnlockAudio();
            if (audioCtx && audioCtx.state === 'running') {
                document.removeEventListener('touchstart', globalUnlock);
                document.removeEventListener('click', globalUnlock);
            }
        };
        document.addEventListener('touchstart', globalUnlock, { once: true });
        document.addEventListener('click', globalUnlock, { once: true });

        function playTone(freq, type, duration, vol = 0.1, startTime = 0) {
            if (!audioCtx || audioCtx.state !== 'running') return;
            try {
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                
                osc.type = type; 
                osc.frequency.setValueAtTime(freq, audioCtx.currentTime + startTime);
                
                gain.gain.setValueAtTime(0, audioCtx.currentTime + startTime);
                gain.gain.linearRampToValueAtTime(vol, audioCtx.currentTime + startTime + 0.015); 
                gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + startTime + duration); 
                
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                
                osc.start(audioCtx.currentTime + startTime);
                osc.stop(audioCtx.currentTime + startTime + duration);
            } catch (e) {}
        }

        function toggleMusic() {
            initAndUnlockAudio(); 
            const btn = document.getElementById('music-toggle');
            
            if (!audioCtx) {
                btn.innerText = "🔇";
                btn.classList.replace("bg-yellow-400", "bg-gray-400");
                return;
            }

            if (isMusicPlaying) {
                clearInterval(bgmInterval);
                isMusicPlaying = false;
                btn.innerText = "🔇";
                btn.classList.replace("bg-yellow-400", "bg-gray-400");
                btn.classList.replace("hover:bg-yellow-500", "hover:bg-gray-500");
            } else {
                isMusicPlaying = true;
                btn.innerText = "🎵";
                btn.classList.replace("bg-gray-400", "bg-yellow-400");
                btn.classList.replace("hover:bg-gray-500", "hover:bg-yellow-500");
                
                const sequence = [
                    { note: 261.63, vol: 0.15 }, { note: 392.00, vol: 0.1 }, 
                    { note: 329.63, vol: 0.12 }, { note: 392.00, vol: 0.1 },
                    { note: 349.23, vol: 0.15 }, { note: 523.25, vol: 0.1 }, 
                    { note: 440.00, vol: 0.12 }, { note: 523.25, vol: 0.1 },
                    { note: 392.00, vol: 0.15 }, { note: 587.33, vol: 0.1 }, 
                    { note: 493.88, vol: 0.12 }, { note: 587.33, vol: 0.1 },
                    { note: 523.25, vol: 0.20 }, { note: null, vol: 0 },     
                    { note: 392.00, vol: 0.15 }, { note: null, vol: 0 }
                ];
                
                beatCount = 0;
                bgmInterval = setInterval(() => {
                    try {
                        if (!audioCtx || audioCtx.state !== 'running') return;
                        
                        const step = sequence[beatCount % sequence.length];
                        if (step.note) {
                            playTone(step.note, 'sine', 0.3, step.vol);
                            playTone(step.note, 'triangle', 0.2, step.vol * 0.4); 
                        }
                        beatCount++;
                    } catch (e) {}
                }, 180); 
            }
        }

        function playSFX(type) {
            if (!audioCtx || audioCtx.state !== 'running') return;
            const now = audioCtx.currentTime;
            
            try {
                if (type === 'roll') {
                    [523.25, 659.25, 783.99, 1046.50].forEach((freq, i) => {
                        playTone(freq, 'sine', 0.15, 0.1, i * 0.05);
                    });
                } else if (type === 'correct') {
                    playTone(783.99, 'sine', 0.15, 0.15, 0);     
                    playTone(1046.50, 'sine', 0.4, 0.2, 0.15);   
                } else if (type === 'wrong') {
                    const osc = audioCtx.createOscillator();
                    const gain = audioCtx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(349.23, now); 
                    osc.frequency.exponentialRampToValueAtTime(174.61, now + 0.5); 
                    
                    gain.gain.setValueAtTime(0, now);
                    gain.gain.linearRampToValueAtTime(0.2, now + 0.05);
                    gain.gain.exponentialRampToValueAtTime(0.01, now + 0.5);
                    
                    osc.connect(gain);
                    gain.connect(audioCtx.destination);
                    osc.start(now);
                    osc.stop(now + 0.5);
                } else if (type === 'win') {
                    playTone(523.25, 'triangle', 0.15, 0.2, 0);
                    playTone(659.25, 'triangle', 0.15, 0.2, 0.15);
                    playTone(783.99, 'triangle', 0.15, 0.2, 0.3);
                    playTone(1046.50, 'triangle', 0.6, 0.3, 0.45);
                    playTone(783.99, 'triangle', 0.15, 0.2, 0.6);
                    playTone(1046.50, 'triangle', 0.8, 0.3, 0.75);
                }
            } catch(e) {}
        }

        // --- 題庫 (前測考卷 + 康軒 Wonder World 5 擴充) ---
        const questionBank = [
            { q: "___ is the book? B: It's on the desk.", options: ["What", "Where", "Who"], ans: 1 },
            { q: "I ___ swim, but I can't dance.", options: ["can", "am", "can't"], ans: 0 },
            { q: "Are you a student? B: Yes, I ___", options: ["is", "am", "are"], ans: 1 },
            { q: "Are you ___? B: Yes, I am. I want some noodles.", options: ["hungry", "thirsty", "sad"], ans: 0 },
            { q: "___ are you doing? B: I am reading.", options: ["Where", "What time", "What"], ans: 2 },
            { q: "請問 'doctor' 屬於哪一類？", options: ["動物 (Animals)", "職業 (Jobs)", "天氣 (Weather)"], ans: 1 },
            { q: "If the dog is sleeping under the table. 請問：The dog is playing a ball.", options: ["True (正確)", "False (錯誤)"], ans: 1 },
            { q: "A: Do you like water? B: Yes, I ____.", options: ["am", "do", "like"], ans: 1 },
            { q: "A: ____ you like milk tea? B: No, I don't.", options: ["Are", "Do", "Does"], ans: 1 },
            { q: "What do you want to drink? I want some ____.", options: ["hot coffee", "bread", "pizza"], ans: 0 },
            { q: "請問 '蘋果汁' 的英文是？", options: ["orange juice", "apple juice", "water"], ans: 1 },
            { q: "I am thirsty (口渴). I want to drink ____.", options: ["cake", "water", "book"], ans: 1 },
            { q: "It's cold (好冷)! I want some ____.", options: ["hot coffee", "soda", "ice cream"], ans: 0 },
            { q: "請問 'orange juice' 是飲料還是食物？", options: ["飲料 (Drink)", "食物 (Food)"], ans: 0 },
            { q: "請問 'pizza' 是飲料還是食物？", options: ["飲料 (Drink)", "食物 (Food)"], ans: 1 },
            { q: "哪一個單字的母音發音與 'tea' 相同？", options: ["bee", "bed", "big"], ans: 0 },
            { q: "哪一個單字的母音發音與 'bike' (i_e) 相同？", options: ["kite", "bit", "kick"], ans: 0 },
            { q: "哪一個單字包含 /o/ 的長母音 (如 rose)？", options: ["boat (oa)", "box (o)", "boy (oy)"], ans: 0 },
            { q: "哪一個單字的母音發音與 'suit' (ui) 相同？", options: ["blue", "but", "bus"], ans: 0 },
            { q: "看到 'ow' 結尾的單字，發音通常與下列何者相同？", options: ["snow (o)", "now (au)", "how (au)"], ans: 0 },
            { q: "請問 'rain' (ai) 的母音發音與下列何者相同？", options: ["train", "ran", "run"], ans: 0 },
            { q: "請問 'he' 的母音發音與下列何者相同？", options: ["hen", "Pete", "hat"], ans: 1 }
        ];

        // --- 遊戲狀態變數 ---
        const totalSteps = 15; 
        let players = [
            { id: 'p1', name: 'Player 1', pos: 0, color: 'text-red-500', bg: 'bg-red-500', border: 'border-red-500' },
            { id: 'p2', name: 'Player 2', pos: 0, color: 'text-blue-500', bg: 'bg-blue-500', border: 'border-blue-500' },
            { id: 'p3', name: 'Player 3', pos: 0, color: 'text-green-500', bg: 'bg-green-500', border: 'border-green-500' }
        ];
        let currentPlayerIndex = 0;
        let pendingDiceValue = 0;
        let currentQuestion = null;
        let availableQuestions = [...questionBank];

        // --- 核心功能函數 ---
        function startGame() {
            try {
                const isAudioReady = initAndUnlockAudio();
                if (isAudioReady && !isMusicPlaying) {
                    toggleMusic();
                }
            } catch (e) {
                console.log("初始化音效失敗", e);
            }

            players[0].name = document.getElementById('p1-name').value || 'Player 1';
            players[1].name = document.getElementById('p2-name').value || 'Player 2';
            players[2].name = document.getElementById('p3-name').value || 'Player 3';

            document.getElementById('p1-avatar').innerText = players[0].name.substring(0, 2);
            document.getElementById('p2-avatar').innerText = players[1].name.substring(0, 2);
            document.getElementById('p3-avatar').innerText = players[2].name.substring(0, 2);

            document.getElementById('start-screen').style.display = 'none';
            document.getElementById('game-screen').style.display = 'flex';

            updateTurnDisplay();
        }

        function updateTurnDisplay() {
            const p = players[currentPlayerIndex];
            const display = document.getElementById('current-turn-display');
            display.innerText = p.name;
            display.className = `text-3xl font-black ml-2 ${p.color}`;

            players.forEach((player, index) => {
                const avatar = document.getElementById(`${player.id}-avatar`);
                if (index === currentPlayerIndex) {
                    avatar.classList.add('active-turn');
                } else {
                    avatar.classList.remove('active-turn');
                }
            });

            document.getElementById('dice-display').innerText = '?';
            document.getElementById('roll-btn').disabled = false;
            document.getElementById('roll-btn').classList.remove('opacity-50', 'cursor-not-allowed');
        }

        function rollDiceAndPlay() {
            const btn = document.getElementById('roll-btn');
            const diceDisplay = document.getElementById('dice-display');
            
            btn.disabled = true;
            btn.classList.add('opacity-50', 'cursor-not-allowed');
            
            diceDisplay.classList.add('rolling');
            playSFX('roll'); 
            
            let count = 0;
            const rollInterval = setInterval(() => {
                diceDisplay.innerText = Math.floor(Math.random() * 3) + 1;
                count++;
                if (count > 10) {
                    clearInterval(rollInterval);
                    diceDisplay.classList.remove('rolling');
                    pendingDiceValue = Math.floor(Math.random() * 3) + 1; 
                    diceDisplay.innerText = pendingDiceValue;
                    
                    setTimeout(showQuestion, 800);
                }
            }, 50);
        }

        function showQuestion() {
            const p = players[currentPlayerIndex];
            
            if (availableQuestions.length === 0) {
                availableQuestions = [...questionBank];
            }
            const qIndex = Math.floor(Math.random() * availableQuestions.length);
            currentQuestion = availableQuestions.splice(qIndex, 1)[0];

            document.getElementById('question-player-name').innerText = p.name;
            document.getElementById('pending-steps').innerText = pendingDiceValue;
            document.getElementById('question-text').innerText = currentQuestion.q;
            
            const optionsContainer = document.getElementById('options-container');
            optionsContainer.innerHTML = '';
            
            currentQuestion.options.forEach((opt, idx) => {
                const btn = document.createElement('button');
                btn.className = "w-full text-left p-3 md:p-4 rounded-xl border-2 border-gray-200 hover:border-indigo-500 hover:bg-indigo-50 text-lg md:text-xl font-bold text-gray-700 transition-colors shadow-sm active:bg-indigo-100";
                btn.innerText = `${String.fromCharCode(65 + idx)}. ${opt}`;
                btn.onclick = () => checkAnswer(idx);
                optionsContainer.appendChild(btn);
            });

            document.getElementById('question-modal').classList.remove('hidden');
        }

        function checkAnswer(selectedIndex) {
            document.getElementById('question-modal').classList.add('hidden');
            const isCorrect = (selectedIndex === currentQuestion.ans);
            
            const feedbackModal = document.getElementById('feedback-modal');
            const feedbackBox = document.getElementById('feedback-box');
            const feedbackIcon = document.getElementById('feedback-icon');
            const feedbackTitle = document.getElementById('feedback-title');
            const feedbackMsg = document.getElementById('feedback-msg');

            feedbackModal.classList.remove('hidden');

            if (isCorrect) {
                playSFX('correct'); 
                feedbackBox.className = "bg-white p-8 rounded-3xl shadow-2xl text-center transform transition-transform pop-in border-4 border-green-500";
                feedbackIcon.innerText = "🌟";
                feedbackTitle.innerText = "答對了！ Awesome!";
                feedbackTitle.className = "text-4xl font-black mb-2 text-green-600";
                feedbackMsg.innerText = `準備前進 ${pendingDiceValue} 步！`;
                
                movePlayer(currentPlayerIndex, pendingDiceValue);
            } else {
                playSFX('wrong'); 
                feedbackBox.className = "bg-white p-8 rounded-3xl shadow-2xl text-center transform transition-transform pop-in border-4 border-red-500";
                feedbackIcon.innerText = "Oops!";
                feedbackTitle.innerText = "答錯囉！ Try Again";
                feedbackTitle.className = "text-4xl font-black mb-2 text-red-600";
                const correctText = currentQuestion.options[currentQuestion.ans];
                feedbackMsg.innerHTML = `不要灰心！留在原地。<br><span class="text-sm mt-2 block text-gray-500">正確答案是: ${correctText}</span>`;
            }
        }

        function movePlayer(playerIdx, steps) {
            const p = players[playerIdx];
            p.pos += steps;
            if (p.pos > totalSteps) p.pos = totalSteps;

            const avatar = document.getElementById(`${p.id}-avatar`);
            
            const percentage = 2 + (p.pos / totalSteps) * 86; 
            avatar.style.left = `${percentage}%`;
        }

        function closeFeedback() {
            document.getElementById('feedback-modal').classList.add('hidden');
            
            if (players[currentPlayerIndex].pos >= totalSteps) {
                showWinner(players[currentPlayerIndex]);
                return;
            }

            currentPlayerIndex = (currentPlayerIndex + 1) % players.length;
            updateTurnDisplay();
        }

        function showWinner(player) {
            playSFX('win'); 
            document.getElementById('winner-modal').classList.remove('hidden');
            document.getElementById('winner-name').innerText = player.name;
        }

    </script>
</body>
</html>
```