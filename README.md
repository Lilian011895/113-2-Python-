# 113-2 期中 Web Scraping

### 亞洲大學資工系教授專業領域爬蟲

在**colab**上使用Python爬蟲技術，抓取亞洲大學資訊工程學系教授的個人資料與研究專長領域，並整合成結構化資料保存。

## 目錄

- [亞洲大學資工系教授專業領域爬蟲](#亞洲大學資工系教授專業領域爬蟲)
  - [功能特點](#功能特點)
  - [使用](#使用)
    - [網頁爬蟲技術](#網頁爬蟲技術)
    - [網頁解析](#網頁解析)
    - [資料處理](#資料處理)
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

副教授：https://csie.asia.edu.tw/zh_tw/associate_professors_2

助理教授：https://csie.asia.edu.tw/zh_tw/assistant_professors_2

講師：https://csie.asia.edu.tw/zh_tw/senior_lecturer

教授：https://csie.asia.edu.tw/zh_tw/all_professors_1


2.爬取完成後，會將所有教授的資料（姓名和專長）保存到當前目錄下的professors_expertise_simple.txt檔案中

3.如果在Colab中執行，程式會自動提供檔案下載選項

### 使用方法

1.  執行程式：
```bash
python professor_scraper.py
```

2. 查看結果，程式將生成 `professors_expertise_simple.txt` 檔案，包含所有教授的姓名與專長。

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

### 2.爬蟲函數 `scrape_professors_expertise`
```python
def scrape_professors_expertise(base_url="https://csie.asia.edu.tw/zh_tw/associate_professors_2", max_pages=5, add_special_cases=False):
```
這個函數的功能：

  1.接收基礎URL和最大頁數參數

  2.設定User-Agent標頭以模擬瀏覽器請求

  3.逐頁爬取教授資料

  4.使用正規表示式從網頁文字中提取教授姓名和專長

  5.如果 add_special_cases=True，還會加入手動輸入的特殊案例

  6.返回包含所有教授資料的列表

### 3.簡單輸出函數
```python
def save_simple_output(professors_data, filename="professors_expertise_simple.txt"):
```
這個函數將爬取的教授資料保存為簡單的文字檔案，每行包含一位教授的姓名和專長。


### 4.檔案保存函數
```python
pythondef save_to_file(data_list, filename="output.txt"):
```
一個通用的檔案保存函數，用於將列表資料寫入檔案。
    
### 5.主程式
`main()`
```python
def main():
```
這裡抓了副教授、助理教授、講師、教授的資料，組成總表，再刪除重複的教授（用姓名判斷），最後儲存。


