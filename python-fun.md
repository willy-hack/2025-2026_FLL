# 🐍 fun.py - 核心邏輯與工具函式庫

## 📝 檔案概述

`fun.py` 是本專案的 **後端核心 (Backend Core)**。它不包含任何使用者介面代碼，而是封裝了所有底層功能，供 `main.py` 呼叫。主要職責包括：影像辨識 (AI 推論)、檔案處理、HTML 動態生成以及與外部 API (N8N) 的通訊。

## 📦 依賴模組

此檔案整合了多個強大的 Python 庫：

* **影像處理**: `cv2` (OpenCV), `PIL` (Pillow), `numpy`
* **人工智慧**: `tensorflow` (載入 SavedModel 格式模型)
* **系統與網路**: `os`, `threading`, `requests`, `zipfile`, `base64`

---

## 🏗️ 類別詳解 (Class Breakdown)

此檔案定義了三個主要類別，分別負責不同的功能面向：

### 1. `class Tool` (工具助手)

負責處理檔案路徑、格式轉換與前端顯示所需的 HTML 生成。

* **`ChDir()`**: 強制將工作目錄切換至程式腳本所在位置，解決相對路徑讀取錯誤的問題。
* **`GetStyle(style_path)`**: 讀取外部 `.css` 檔案內容，供 Gradio 介面使用。
* **`CompileImage(img_path)`**:
* 讀取硬碟中的 JPG 圖片。
* 將其轉換為 **Base64 字串**。
* *用途*：讓圖片能直接嵌入 HTML 字串中顯示。


* **`InfomationCard(utensils_code)`**:
* **核心功能**：讀取 `UtensilsData.json` 與 `InfomationCard.html`。
* 將器物資料（名稱、年代、說明）與 Base64 圖片動態填入 HTML 模板中。
* 回傳渲染後的 HTML 字串供前端顯示。



---

### 2. `class Camera` (視覺中樞)

負責攝影機控制、影像擷取與 AI 模型推論。此類別使用 **多執行緒 (Threading)** 設計，確保影像辨識不會卡住使用者介面。

* **初始化 (`__init__`)**:
* 設定攝影機 ID。
* 載入 `UtensilsData.json`。
* 指定 TensorFlow 模型路徑 (`model.savedmodel`)。


* **`_update()` (後台執行緒)**:
* 這是一個無窮迴圈，持續從攝影機讀取畫面。
* **影像預處理**：Resize 至 224x224、正規化 (-1~1)。
* **AI 推論**：將影像送入 TensorFlow 模型。
* **結果解析**：若信心水準 (Confidence) > 0.7，則更新當前辨識到的 `uten['id']`。
* **畫面繪製**：在影像上疊加標籤文字與信心數值。


* **`StreamVideo()`**:
* 這是一個 **生成器 (Generator)**。
* 專門提供給 Gradio 的 `gr.Image(streaming=True)` 使用，持續輸出最新的影像幀。


* **`UtensilsID()`**:
* 獲取目前辨識到的器物 ID，或透過名稱反查 ID。



---

### 3. `class InternetSeletter` (網路通訊員)

負責將收集到的資料發送至雲端自動化平台 (N8N)，並處理回傳的生成結果。

* **`N8nProcess(prompt_word, utensils_url)`**:
* 將提示詞與圖片網址打包成 JSON。
* 發送 **POST 請求** 至 N8N Webhook。
* 接收回傳的 **Zip 壓縮檔**。
* 將 Zip 檔解壓縮至指定的 `save_dir` (即 `./data/`)。


* **`SendOutReduction(...)`**:
* **邏輯整合**：接收來自前端的所有參數（人物、著裝、情境...）。
* **Prompt 工程**：將參數組合成一段完整的中文提示詞 (`prompt_word`)。
* 呼叫 `N8nProcess` 執行發送。
* 回傳下載好的圖片與影片路徑 (`./data/image.png`, `./data/video.mp4`)。



---

## 🔄 與 main.py 的互動流程

1. **初始化**：`main.py` 實例化 `Camera`, `Tool`, `InternetSeletter`。
2. **畫面串流**：前端呼叫 `camera.StreamVideo` 顯示畫面。
3. **辨識**：`Camera` 在後台不斷更新辨識結果。
4. **擷取**：使用者按下按鈕 -> `main.py` 呼叫 `camera.UtensilsID` 獲取 ID -> 呼叫 `tool.InfomationCard` 生成卡片。
5. **生成**：使用者按下生成 -> `main.py` 收集參數 -> 呼叫 `internet.SendOutReduction` -> 下載並顯示結果。