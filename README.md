# 113-2 期中 Web Scraping

### 亞洲大學資工系教授專業領域爬蟲

在**colab**上使用Python爬蟲技術，此程式能夠從亞洲大學資訊工程學系網站抓取教師資料，包括教師類型（專任/兼任）、姓名和研究領域，最後自動下載為文字檔案。

## 目錄

- [亞洲大學資工系教授專業領域爬蟲](#亞洲大學資工系教授專業領域爬蟲)
  - [功能特點](#功能特點)
  - [使用](#使用)
  - [使用方法](#使用方法)    
- [程式介紹](#程式介紹)
  
### 功能特點

- 自動抓取亞洲大學資工系不同教授的資料
- 處理特殊案例與例外情況
- 去除重複資料
- 資料整合與清理
- 將結果保存為易讀的文字檔案

### 使用

1.程式會自動爬取以下頁面的教授資料：

https://csie.asia.edu.tw/zh_tw/TeacherIntroduction/Full_time_faculty

https://csie.asia.edu.tw/zh_tw/TeacherIntroduction/Full_time_professor

https://csie.asia.edu.tw/zh_tw/TeacherIntroduction/Full_time_Associate_Professor

https://csie.asia.edu.tw/zh_tw/TeacherIntroduction/Full_time_Assistant_Professor

https://csie.asia.edu.tw/zh_tw/TeacherIntroduction/Full_time_Lecturer

https://csie.asia.edu.tw/zh_tw/TeacherIntroduction/Part_time_faculty

2.發送 HTTP 請求獲取網頁內容

3.解析 HTML 結構尋找教師資料

4.清理並標準化擷取的資料

5.對資料進行去重和排序

6.顯示結果並觸發文件下載，保存到當前目錄下的asia_university_csie_teachers.txt檔案中

### 使用方法

1.  執行程式：
```bash
期中.ipynb
```

2. 查看結果，程式將生成 `asia_university_csie_teachers.txt` 檔案，包含所有教授的姓名與專長。

# 程式介紹

### 1. 導入模組
```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import re
from google.colab import files
```
`request`：發送網頁請求。

`BeautifulSoup`：解析 HTML。

`pandas`：用於資料整理和操作

`re`：用來做正規表達式比對（文字資料提取用）。

`google.colab.files`：提供Colab環境中的檔案下載功能

### 2.設定目標URL列表
```python
urls = [
    "https://csie.asia.edu.tw/zh_tw/TeacherIntroduction/Full_time_faculty",
    "https://csie.asia.edu.tw/zh_tw/TeacherIntroduction/Full_time_professor",
    "https://csie.asia.edu.tw/zh_tw/TeacherIntroduction/Full_time_Associate_Professor",
    "https://csie.asia.edu.tw/zh_tw/TeacherIntroduction/Full_time_Assistant_Professor",
    "https://csie.asia.edu.tw/zh_tw/TeacherIntroduction/Full_time_Lecturer",
    "https://csie.asia.edu.tw/zh_tw/TeacherIntroduction/Part_time_faculty"
]
```
這個列表包含六個不同網頁的URL，每個URL對應不同類型的教師資料。

### 3.網頁請求標頭設定
```python
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8',
    'Accept-Language': 'zh-TW,zh;q=0.9,en-US;q=0.8,en;q=0.7',
}
```
這個函數將爬取的教授資料保存為簡單的文字檔案，每行包含一位教授的姓名和專長。

### 4.主要爬蟲迴圈
程式使用for迴圈處理每個URL：
```python
for url in urls:
    # 根據URL確定教師類型
    if "Part_time" in url:
        teacher_type = "兼任教師"
    else:
        # 進一步區分專任教師的職級
        ...
```
根據URL中的關鍵字判斷教師類型，例如是專任教授還是兼任教師等。
    
### 5.網頁請求與解析
```python
try:
    response = requests.get(url, headers=headers, timeout=30)
    response.encoding = 'utf-8'
    
    if response.status_code != 200:
        continue
        
    soup = BeautifulSoup(response.text, 'lxml')
    main_content = soup.select_one('#main-content') or soup.select_one('main') or soup
```
發送HTTP請求獲取網頁內容，並使用BeautifulSoup解析HTML結構。透過選擇器找到主要內容區域。

### 6.資料擷取策略
程式有四種不同的資料擷取策略，按順序嘗試：

**策略一：從表格中提取資料**
```python
tables = main_content.find_all('table')
for table in tables:
    # 處理表格結構
```
尋找網頁中的表格元素，從表格中提取教師姓名和研究領域。

**策略二：從文本中搜尋特定模式**
```python
if not [t for t in all_teachers if t["類型"] == teacher_type]:
    page_text = soup.get_text()
    name_matches = re.finditer(r'姓\s*名[：:]\s*([\u4e00-\u9fa5]{2,4})', page_text)
```
如果表格分析沒有找到資料，則從整個頁面文本中搜尋符合「姓名：XXX」模式的內容。

**策略三：分析可能的教師卡片元素**
```python
if not [t for t in all_teachers if t["類型"] == teacher_type]:
    cards = main_content.select('.teacher-card, .profile, .faculty-container > div, .listBS > div')
```
尋找可能包含教師資訊的卡片式元素。

**策略四：解析所有段落**
```python
if not [t for t in all_teachers if t["類型"] == teacher_type]:
    paragraphs = main_content.find_all(['p', 'div', 'li'])
```
最後嘗試分析所有段落元素，找出可能的教師姓名和研究領域。

### 7.資料整理與去重
```python
unique_teachers = []
seen_names = set()

for teacher in all_teachers:
    if teacher["姓名"] not in seen_names:
        seen_names.add(teacher["姓名"])
        unique_teachers.append(teacher)
```
使用集合紀錄已見過的教師姓名，確保最終結果不含重複資料。

### 8.結果輸出與下載
```python
if all_teachers:
    df = pd.DataFrame(all_teachers)
    df = df.sort_values(by=['類型', '姓名'])
    
    # 輸出到螢幕
    for _, row in df.iterrows():
        print(f"{row['類型']} - {row['姓名']} - {row['研究領域']}")
    
    # 儲存為檔案並觸發下載
    file_name = 'asia_university_csie_teachers.txt'
    with open(file_name, 'w', encoding='utf-8') as f:
        for _, row in df.iterrows():
            f.write(f"{row['類型']} - {row['姓名']} - {row['研究領域']}\n")
    
    # 使用 Google Colab 的 files.download 函數觸發檔案下載
    files.download(file_name)
```
將結果轉換為pandas DataFrame，按類型和姓名排序，同時在螢幕上顯示結果並儲存為文字檔案。最後使用Colab特有的files.download函數觸發檔案下載到本地電腦。



