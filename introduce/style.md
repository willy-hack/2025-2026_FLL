# 🎨 style.css - 介面美化樣式表

## 📝 檔案概述

`style.css` 是本專案的 **層疊樣式表 (Cascading Style Sheets)**。它負責定義 Gradio 介面中 HTML 元件（特別是「器物資訊卡」）的視覺外觀。

此檔案的設計目標是營造出一種 **「古代、歷史、博物館」** 的氛圍，使用了大量的暖色調（米色、褐色）與紙質感配色，並透過 CSS Flexbox 技術確保版面在不同螢幕上的整齊排列。

## 🎨 視覺風格與配色 (Design System)

本樣式表採用 **大地色系** 為主色調，模擬古書或文物標籤的質感：

| 顏色代碼 | 預覽 | 用途說明 |
| --- | --- | --- |
| **`#f4f1ea`** | 📜 米白/紙色 | 資訊卡背景底色，模擬舊紙張質感。 |
| **`#8b4513`** | 🟤 深褐/鞍褐 | 強調色，用於標題底線、粗體文字與標籤。 |
| **`#d4c5a9`** | 🏜️ 淺褐/卡其 | 邊框顏色、分隔線顏色。 |
| **`#3e3a36`** | ⚫ 深灰黑 | 正文文字顏色，比純黑柔和，易於閱讀。 |

---

## 🏗️ 核心樣式類別詳解 (Class Breakdown)

樣式表依據功能分為三個主要區塊：

### 1. 外部容器定位 (`#info_col`)

這是針對 Gradio 元件 ID 的特定設定，用於強制覆寫 Gradio 的預設樣式。

* **`#info_col`**:
* 使用 `display: flex` 與 `justify-content: center` 確保資訊卡在畫面中**水平垂直置中**。
* 設定固定高度 `400px`，防止卡片因內容過多而撐開版面。
* 使用 `!important` 強制優先權，確保 Gradio 內建樣式不會干擾排版。



### 2. 卡片主體結構

* **`.InfoCard`**:
* 資訊卡的最外層容器。
* 設定了圓角 (`border-radius`) 與內陰影 (`box-shadow`)，增加立體感。
* 字體指定為 **微軟正黑體 (`Microsoft JhengHei`)**，確保中文顯示美觀。


* **`.InfoHead`**:
* 卡片頂部的標題區。
* 底部帶有深褐色實線 (`border-bottom`)，區隔標題與內容。


* **`.InfoBody`**:
* 內容區塊，使用 Flexbox 將畫面分為左右兩欄（左圖、右文）。



### 3. 內容排版細節

* **圖片區 (`.InfoImgDiv`, `.InfoImg`)**:
* 佔據左側 30% 寬度。
* 圖片設定圓角，並自動縮放以適應容器。


* **文字區 (`.InfoTableDiv`)**:
* 佔據右側 70% 寬度。
* 設定 `overflow: auto` 與 `scrollbar-width: none`，當文字過多時可捲動但**隱藏捲軸**，保持介面簡潔。


* **表格樣式 (`.InfoTable`, `.InfoTableTd`)**:
* 用於顯示「鑑別年代」與「鑑別時代」。
* 使用 **虛線 (`dashed`)** 作為分隔線，增添設計細節。


* **介紹文字 (`.InfoIntroduce`)**:
* 設定 `text-align: justify` (左右對齊)，讓長段落文字閱讀起來更整齊。



---

## 🔗 與 Python 的整合

在 `main.py` 中，此檔案透過 `demo.launch()` 的 `css` 參數被載入：

```python
# main.py
if __name__ == "__main__":
    demo.queue(...).launch(
        # 載入此 CSS 檔案
        css=tool.GetStyle(os.path.abspath('./style.css')), 
        debug=True
    )

```

同時，`fun.py` 生成的 HTML 字串中，`class="..."` 屬性即對應此檔案中的定義：

```html
<div class="InfoCard">  <div class="InfoHead">...</div>
    ...
</div>

```

## ⚠️ 特別設計註記

* **`user-select: none;`**:
* 應用於多個區塊，**禁止使用者選取文字或圖片**。這通常用於模擬 App 質感，避免使用者在操作介面時誤將文字反白。


* **`!important`**:
* 在 `#info_col` 中大量使用，這是為了對抗 Gradio 框架本身強制的 CSS 屬性，確保自定義的排版能生效。