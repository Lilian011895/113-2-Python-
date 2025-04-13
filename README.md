# 113-2 期中 Web Scraping

### 亞洲大學資工系教授專業領域爬蟲

在**colab**上使用Python爬蟲技術，抓取亞洲大學資訊工程學系教授的個人資料與研究專長領域，並整合成結構化資料保存。

## 目錄

- [亞洲大學資工系教授專業領域爬蟲](#亞洲大學資工系教授專業領域爬蟲)
  - [功能特點](#功能特點)
  - [使用技術](#使用技術)
    - [網頁爬蟲技術](#網頁爬蟲技術)
    - [網頁解析](#網頁解析)
    - [資料處理](#資料處理)
  - [使用方法](#使用方法)
  - [爬取對象](#爬取對象)
    
- [程式介紹](#程式介紹)

### 功能特點

- 自動抓取亞洲大學資工系不同教授的資料
- 處理特殊案例與例外情況
- 去除重複資料
- 資料整合與清理
- 將結果保存為易讀的文字檔案

### 使用技術

- **網頁爬蟲技術**：
  - `urllib.request` - 發送HTTP請求
  - User-Agent模擬 - 避免被網站封鎖
- **網頁解析**：
  - `BeautifulSoup4` - HTML解析
- **資料處理**：
  - 正則表達式 (`re`) - 結構化資料提取
  - 字串處理與資料清理
  - 避免資料重複

### 使用方法

1. 安裝：
```bash
pip install beautifulsoup4
```

2. 執行程式：
```bash
python professor_scraper.py
```

3. 查看結果，程式將生成 `professors_expertise_simple.txt` 檔案，包含所有教授的姓名與專長。

### 爬取對象

本爬蟲抓取亞洲大學資工系的以下頁面：
1. 副教授頁面 (`associate_professors_2`)
2. 助理教授頁面 (`assistant_professors_2`)
3. 講師頁面 (`senior_lecturer`)
4. 教授頁面 (`all_professors_1`)

# 程式介紹

### 1. 導入模組
```python
from urllib.request import Request, urlopen
from bs4 import BeautifulSoup
import re
```

`urllib.request`：發送網頁請求。

`BeautifulSoup`：解析 HTML。

`re`：用來做正規表達式比對（文字資料提取用）。

### 2.定義主爬蟲函式
`scrape_professors_expertise(...)`
這個函式會從給定的教授頁面開始爬資料，最多抓 5 頁，流程如下：

**1. 設定 headers（模擬瀏覽器）**
```python
headers = {'User-Agent': 'Mozilla/5.0 ...'}
```
防止網站阻擋爬蟲。

**2.用迴圈抓每一頁資料**
```python
for page in range(1, max_pages + 1):
```
如果是第 1 頁，就直接用原始網址。
如果是第 2 頁以後，網址加上 `?page_no=2` 等參數。

**3.發送請求、解析 HTML**
```python
req = Request(current_url, headers=headers)
html = urlopen(req)
soup = BeautifulSoup(html, 'html.parser')
text = soup.get_text()
```
這裡用來取得整個頁面的純文字。

**4. 用正規表達式提取資料**
```python
pattern = r'姓名[\s\:：]+([\w\(\)\-\,\. ]+)\s*\n+\s*職稱[\s\:：]+([^\n]+)\s*\n+[\s\S]*?研究領域[\s\:：]+([\s\S]*?)(?=\s*Office hour|網站|分機)'
matches = re.findall(pattern, text)
```
很重要的一部分：比對「姓名」、「職稱」和「研究領域」這些內容。

**5.資料清理 & 加入清單**
```python
expertise = expertise.replace(', ', '、').replace('，', '、')
```
把中英文逗號等格式轉成統一的「、」，然後存進 `all_professors` 清單中。

**6.處理特殊案例**
```python
special_cases = [
    {"姓名": "Tadao Murata", "專長": "..."},
    ...
]
```
有些教授網頁可能抓不到，從這裡硬編寫進來（補資料用）。

**7.儲存檔案**
`save_simple_output(...)`
```python
def save_simple_output(professors_data, filename="professors_expertise_simple.txt"):
```
這個函式把抓到的資料儲存成純文字檔，每行輸出一位教授。

**8.主程式**
`main()`
```python
def main():
```
這裡抓了副教授、助理教授、講師、教授的資料，組成總表，再刪除重複的教授（用姓名判斷），最後儲存。


