# 🎮 新南英語大冒險：五年級英語挑戰賽 (iPad Air 5 專用優化版)

專為國小五年級學生與班級分組學習打造的平板單機三人網頁競賽遊戲。結合了「新南國小五年級英語前測測驗」與「康軒 Wonder World 5 第一單元（The Best Drink & Phonics）」之課綱核心，內建完整 100 題優質題庫！

![支援設備](https://img.shields.io/badge/iPad%20Air%205-專用優化-blue)
![零相依套件](https://img.shields.io/badge/免安裝套件-100%25%20Offline%20Ready-success)
![資安檢核](https://img.shields.io/badge/資安檢核-零個資%20%7C%20零%20XSS-brightgreen)

---

## 🚀 線上即時體驗 (Live Demo)

👉 **GitHub Pages 線上暢玩**：[https://star1123-cheng.github.io/xinnan-english-adventure/](https://star1123-cheng.github.io/xinnan-english-adventure/)

在 iPad Air 5 上直接以 Safari 開啟上方連結，點擊「分享」>「加入主畫面」，即可享受如同原生 App 般的無邊框全螢幕體驗！

---

## ✨ 三大核心升級與優化

1. 🏁 **跑道完全不留白（依據原版設計）**：
   - 紅、藍、綠三道跑道緊密相連，移除跑道之間的空白間隙，形成一體成型的賽馬大富翁跑道。
   - 右側垂直黃色旗標 `FINISH` 貫通三條賽道，呈現熱血競速感。

2. 🔢 **格子清晰可數、走幾格一目了然**：
   - 跑道畫分 **15 個清楚的獨立格子（1 ~ 15）**，交替斑馬條紋背景與分隔線。
   - 答對時，棋子會**一格一格跳躍前進**（每前進一格發出清脆跳步聲），學生能與全班一起大聲數「1、2、3 步！」，精確對齊停駐於目標格子中心。

3. 📚 **內建 100 題完整課綱題庫（免手動匯入）**：
   - 參考 `新南國小五年級_英語前測測驗.pdf` 與 `課程指標五碼` 完整設計：
     * **Phonics 母音與自然發音（35 題）**：長短母音對比、ee/ea、i_e/i、ai/ay、o_e/oa/ow、u_e/ue/ui、sh/ch 首音辨識。
     * **Unit 1 飲料與飲食 The Best Drink（35 題）**：hot coffee, hot tea, water, milk tea, apple juice, soda, orange juice；Do you like...? What do you want to drink? 點餐與食物/飲料分類。
     * **前測文法與生活對話（15 題）**：Where is the book? What are you doing? Can you dance? Are you hungry/thirsty? 句子重組。
     * **前測單字與情境分類（15 題）**：職業 Jobs, 天氣 Weather, 動物 Animals, 數字 1-50, 家中空間 Rooms, 心情 Feelings。
   - 絕不語音爆雷劇透答案，全文字純粹閱讀考驗。

4. 🏷️ **專屬深色浮水印**：
   - 左下角常駐 `Created by 祐誠老師` 橢圓膠囊標籤，質感高雅。

---

## 🔒 資安與個資保護說明

- **零個資蒐集（PII Safety）**：遊戲全程不蒐集、不上傳學生真實姓名、學號或身分證字號，僅以 `Player 1`、`Player 2`、`Player 3` 代號運作。
- **DOM 操作零 XSS**：全數使用原生安全之 `textContent` 與受控 DOM 節點，徹底杜絕跨站腳本攻擊隱患。
- **試卷與機敏素材隔離**：本地開發所用的學校內部試卷 PDF 與參考截圖已透過 `.gitignore` 隔離，確保校園資訊安全不外洩。
- **純靜態託管邊界安全**：部署於 GitHub Pages 靜態空間，全面啟用 HTTPS 強制加密傳輸。

---
*Created by 祐誠老師 · 新南英語大冒險*
