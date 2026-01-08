# 📄 InfomationCard.html - 器物資訊卡 HTML 模板

## 📝 檔案概述

`InfomationCard.html` 是一個 **HTML 模板片段 (Template Fragment)**。它不是一個完整的網頁，而是定義了單一元件——「器物資訊卡」的結構。

此檔案與 `fun.py` 和 `style.css` 緊密配合：

1. **結構**：由本檔案定義 HTML 標籤。
2. **數據**：由 `fun.py` 動態填入變數 (如器物名稱、圖片)。
3. **樣式**：由 `style.css` 控制顏色、字體與排版。

## 🧩 模板語法與變數 (Template Syntax)

此檔案使用了 Python `string.format_map()` 的字串格式化語法。所有被 **波浪括號 `{}**` 包圍的文字都是「變數佔位符」，在程式執行時會被替換為實際內容。

### 變數對照表

| 變數名稱 (Placeholder) | 資料來源 | 說明 | 範例內容 |
| --- | --- | --- | --- |
| `{name}` | `UtensilsData.json` | 器物的名稱，顯示於卡片頂部標題。 | 鼎、青銅劍 |
| `{base64}` | `Tool.CompileImage()` | 經過 Base64 編碼的圖片字串，用於 `src` 屬性。 | `data:image/jpeg;base64,...` |
| `{器物年代}` | `UtensilsData.json` | 該器物的具體年份或時期。 | 商晚期、西周早期 |
| `{器物時代}` | `UtensilsData.json` | 該器物所屬的大時代或朝代。 | 青銅時代、鐵器時代 |
| `{器物說明}` | `UtensilsData.json` | 關於該器物的詳細文字介紹。 | 這是一個用於祭祀的禮器... |

---

## 🏗️ DOM 結構分析

此模板採用了 **Flexbox (推測)** 或 **區塊佈局** 來排列內容，主要分為以下層級：

1. **`.InfoCard` (主容器)**
* **`.InfoHead` (標題區)**：顯示 `{name}`。
* **`.InfoBody` (內容區)**：
* **`.InfoImgDiv` (圖片區)**：包含 `<img>` 標籤，負責顯示器物照片。
* **`.InfoTableDiv` (資訊區)**：
* **表格 (`table`)**：左右兩欄排列，顯示「鑑別年代」與「鑑別時代」。
* **`.InfoIntroduce` (介紹區)**：包含標籤 `<strong>` 與詳細內文 `{器物說明}`。







---

## 🔗 Python 整合方式

在 `fun.py` 中，程式透過以下方式讀取並使用此模板：

```python
# 1. 讀取 HTML 檔案內容
with open('InfomationCard.html', 'r', encoding="utf-8") as Card:
    InfoCard = Card.read()

# 2. 準備資料字典 (包含所有要填入的變數)
Info = {
    "name": "鼎",
    "base64": "data:image/...",
    "器物年代": "商代",
    "器物時代": "青銅器時代",
    "器物說明": "煮食器..."
}

# 3. 將資料填入模板
# format_map 會將 Info 字典中的 key 對應到 HTML 中的 {key}
RenderedHTML = InfoCard.format_map(Info)

```

## 🎨 樣式依賴 (CSS Dependency)

此 HTML 檔案本身不包含任何樣式設定（除了最外層的 `style="..."`），所有的視覺美化都依賴 `style.css` 中的 Class 定義：

* `.InfoCard`: 卡片背景、邊框、陰影。
* `.InfoHead`: 標題字體大小、背景色。
* `.InfoImg`: 圖片的大小限制、圓角。
* `.InfoTable`: 表格的寬度、邊框樣式。

若要修改卡片的顏色或排版，請勿修改此 HTML 檔，而應前往 `style.css` 修改對應的 Class。