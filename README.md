# 113-2 期中 Web Scrapying

## 亞洲大學資工系教授專業領域爬蟲

使用Python爬蟲技術，抓取亞洲大學資訊工程學系教授的個人資料與研究專長領域，並整合成結構化資料保存。

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

## 使用方法

1. 安裝：
```bash
pip install beautifulsoup4
```

2. 執行程式：
```bash
python professor_scraper.py
```

3. 查看結果，程式將生成 `professors_expertise_simple.txt` 檔案，包含所有教授的姓名與專長。

## 爬取對象

本爬蟲抓取亞洲大學資工系的以下頁面：
1. 副教授頁面 (`associate_professors_2`)
2. 助理教授頁面 (`assistant_professors_2`)
3. 講師頁面 (`senior_lecturer`)
4. 教授頁面 (`all_professors_1`)


