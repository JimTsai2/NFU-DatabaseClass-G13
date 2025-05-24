# 虎科大美食評論系統

## 組員
| 學號 | 姓名 | 分工 |
|------|------|------|
| 41143250 | 蔡士雋 | 資料庫架構設計、客戶端設計 |
| 41143217 | 阮國霖 | 資料庫架構設計 |
| 41143218 | 周羿廷 | 資料庫系統規劃 |

## 系統需求介紹
本系統是一款以「在地美食推薦與交流」為核心的應用平台，主要提供兩大功能板塊：美食清單推薦系統與社群互動分享平台。系統旨在協助客人使用者快速找到優質店家、分享個人用餐經驗，商家使用者也憑藉本系統建立自家店家資訊，並藉由社群互動促進資訊交流與在地美食文化推廣。 

## 應用情境
### 使用者A（新生探索型）
客人A使用者剛來虎尾就讀，對當地店家不熟。中午用餐時間，他透過清單板塊查詢學校附近熱門美食，根據其他客人使用者的評價、照片與店家資訊，快速找到一間適合自己口味的小吃店。
### 使用者B（在地貢獻型）
客人B使用者使用者在虎尾生活一段時間，對幾間私房美食相當推薦。他使用社群板塊發表貼文，分享自己的用餐照片與詳細評價，鼓勵更多人一同體驗。
### 使用者C（互動參與型）
客人Ｃ使用者在瀏覽客人B使用者的貼文後對內容產生共鳴，便在底下留言表示自己也去過這家店，進而引發其他使用者的共鳴與交流。
### 使用者D（行銷商家）
商家使用者D登記自家的店舖資訊到資料庫當中，詳細的提供自家服務的菜品及店家資訊，其他客人使用者就可以藉此認識並前來做客並分享自己的感想。

## 使用案例
1. 客人使用者  
- 尋找美食商家  
- 對餐廳評價  
- 分享美食商家資訊  
2. 商家使用者  
- 提供上傳商家資訊  
- 對分享美食商家資訊參與交流  
3. 管理驗證人員  
- 審查不當內容  
- 查核驗證商家資訊  


### ER Diagram
![1469b19e-acd2-4a77-9855-509e4c7c253d](https://github.com/user-attachments/assets/2d087caa-00eb-4e6b-ab37-de635206e3d2)

 
## 資料庫Schema
### 使用者資料表

```sql
CREATE TABLE user (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    user_name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    phone_number INT NOT NULL,
    type SET NOT NULL
);
```

| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|-------------|----------|----|--------------|
| `user_id`     | INTEGER | 使用者代號 | 否 | 主鍵，自動產生 |
| `user_name`   | VARCHAR(100) | 使用者姓名 | 否 | 長度為1-50的字元 |
| `email`  | VARCHAR(100 | 電子郵件 | 否 | 長度為1-100的字元 |
| `phone_number`  | INTEGER | 電話號碼 | 否 | 阿拉伯數字，必須剛好10碼  |
| `type`  | SET | 身分類別 | 否 | 分為客戶身分、老闆身分  |
---

### 商家資料表

```sql
CREATE TABLE store (
    store_id INT PRIMARY KEY AUTO_INCREMENT,
    store_name VARCHAR(100) NOT NULL,
    tel_number INT NOT NULL,
    address VARCHAR(100) NOT NULL,
    website VARCHAR(100),
    description VARCHAR(1000)
);
```

| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|-------------|----------|----|--------------|
| `store_id`     | INTEGER | 店家代號 | 否 | 主鍵，自動產生(從1開始遞增) |
| `store_name`   | VARCHAR(100) | 店家名稱 | 否 | 長度為1-100的字元 |
| `tel_number`  | INTEGER | 電話號碼 | 否 | 阿拉伯數字，必須剛好10碼 |
| `address`  | VARCHAR(100) | 地址 | 否 | 長度為0-100的字元 |
| `website`  | VARCHAR(100) | 網站 | 是 | 長度為0-100的字元 |
| `description`  | VARCHAR(1000) | 簡介 | 是 | 長度為0-1000的字元 |
---

### 貼文資料表

```sql
CREATE TABLE post (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    date DATETIME NOT NULL,
    content VARCHAR(1000) NOT NULL,
    picture
);
```

| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|-------------|----------|----|--------------|
| `user_id`     | INTEGER | 使用者代號 | 否 | 主鍵，自動產生(從1開始遞增) |
| `date`   | DATETIME | 日期 | 否 | YYYY-MM-DD HH:MM:SS |
| `content`  | VARCHAR(1000) | 內文 | 否 | 長度為0-1000的文字 |
| `picture`  | VARCHAR(100) | | 是 |  |
---

### 評價資料表

```sql
CREATE TABLE score (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(10) NOT NULL,
    score_date DATETIME NOT NULL,
    content VARCHAR(1000) NOT NULL,
    score INTEGER NOT NULL
);
```

| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|-------------|----------|----|--------------|
| `user_id`     | INTEGER | 使用者代號 | 否 | 主鍵，自動產生(從1開始遞增) |
| `title`     | VARCHAR(10) | 標題 | 否 | 長度為1-10的文字 |
| `score_date`   | DATETIME | 評價日期 | 否 | YYYY-MM-DD HH:MM:SS |
| `content`  | VARCHAR(20) | 內文 | 否 | 長度為0-20的文字 |
| `score`  | INTEGER | 評分 | 否 | 分數介於1-10分 |
---
## SQL 
(empty)


