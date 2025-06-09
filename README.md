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
### 使用者C（行銷商家）
商家使用者C登記自家的店舖資訊到資料庫當中，詳細的提供自家服務的菜品及店家資訊，其他客人使用者就可以藉此認識並前來做客並分享自己的感想。

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


## ER Diagram
![1](https://github.com/user-attachments/assets/152e2c41-120f-40d0-aad3-b1e651178523)

### 說明：

#### 1.users(使用者)：一個使用者可以有0到多個貼文(0..n個user_posts)，一個使用者可以有1到多個評論(1..n個user_reviews)
-包含：user_id(使用者id)、user_name(使用者名稱)、email(電子郵件)、phone_number(電話號碼)
#### 2.user_posts(貼文)：一個貼文與一個使用者相關聯(1..1個users)
-包含：user_id(使用者id)、type(類別)、date(發布日期)、content(內文)
#### 3.user_reviews(評論)：一個評論與一個使用者相關聯(1..1個users)，一則評論與一個商家相關聯(1..1個stores)
-包含：user_id(使用者id)、reviews_id(評論id)、store_id(商家id)、title(標題)、score_date(評論日期)、content(內文)
#### 4.stores(商家)：一個商家可以有1到多個評論(1..n個user_reviews)
-包含:store_id(商家id)、store_name(商家名稱)、tel_number(電話號碼)、address(地址)、description(評論)、website(網站)

## 資料庫Schema
### 使用者資料表

```sql
CREATE TABLE users (
    user_id INT UNSIGNED AUTO_INCREMENT,--每個使用者的識別碼
    user_name VARCHAR(50) NOT NULL,--儲存使用者的姓名
    email VARCHAR(100) NOT NULL,--儲存使用者的電子郵件
    phone_number VARCHAR(10) NOT NULL,--儲存使用者的電話號碼
    type SET('Customer', 'Proprietor') NOT NULL DEFAULT 'Customer',--標示使用者的類型(客戶或業主)
    PRIMARY KEY (user_id),--確保user_id是唯一的
    UNIQUE (email),--確保email的唯一性
    UNIQUE (phone_number),--確保phone_number的唯一性
    CHECK (CHAR_LENGTH(user_name) BETWEEN 1 AND 50),--檢查user_name的長度介於1-50
    CONSTRAINT chk_email CHECK (email REGEXP '^[a-zA-Z0-9._+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$'),--檢查email的正確輸入
    CONSTRAINT chk_phone_number CHECK (phone_number REGEXP '^09[0-9]{8}$')--檢查電話號碼的正確輸入
);
```

| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|-------------|----------|----|--------------|
| `user_id`     | INTEGER | 使用者代號 | 否 | 主鍵，自動產生，限制為正整數（UNSIGNED），避免負數 |
| `user_name`   | VARCHAR(50) | 使用者姓名 | 否 | 長度為1-50的字元，不可為空 |
| `email`  | VARCHAR(100) | 電子郵件 | 否 | 長度為1-100的字元，必須符合電子郵件格式，例如(41143200@nfu.edu.tw)，不可為空，有唯一性約束 |
| `phone_number`  | VARCHAR(10) | 電話號碼 | 否 | 阿拉伯數字，必須剛好10碼，必須符合電話格式，以"09"開頭，例如(0912345678)，不可為空，有唯一性約束  |
| `type`  | SET | 身分類別 | 否 | 分為客戶身分、老闆身分，預設值設為"客戶身分"，不可為空  |

**說明：**
- AUTO_INCREMENT：自動產生一個整數，從1開始
- INT UNSIGNED：使用無符號整數（只能是正整數，範圍為 0 到 4294967295）
- VARCHAR()：可變長度字串，最大長度為()中數值的字元
- NOT NULL：表示該欄位不能為空，必須提供值
- type：定義一個欄位，用來標示使用者類型
- SET('Customer', 'Proprietor')：限制該欄位只能是 Customer 或 Proprietor 之一
- DEFAULT 'Customer'：如果插入記錄時未指定 type，預設值為 Customer
- PRIMARY KEY：主鍵，確保唯一性
- UNIQUE()：為()中的欄位設置唯一約束，確保在表中是唯一的
- CHECK()：為()中的欄位添加檢查約束
- CONSTRAINT chk_email CHECK (email REGEXP '^[a-zA-Z0-9._+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$')：為 email 欄位添加名為 chk_email 的檢查約束，使用正規表達式（REGEXP）驗證電子郵件格式
- CONSTRAINT chk_phone_number CHECK (phone_number REGEXP '^09[0-9]{8}$')：為 phone_number 欄位添加名為 chk_phone_number 的檢查約束，使用正規表達式驗證電話號碼格式

**真實資料：**

![image](https://github.com/user-attachments/assets/05f1aa47-b4b4-4c1f-9664-4219ba92f8de)

---

### 商家資料表

```sql
CREATE TABLE stores (
    store_id INT UNSIGNED AUTO_INCREMENT,--每個商店的識別碼
    store_name VARCHAR(100) NOT NULL,--儲存商店的名稱
    tel_number VARCHAR(10) DEFAULT NULL,--儲存商店的電話號碼
    address VARCHAR(100) NOT NULL,--儲存商店的實體地址
    website VARCHAR(100),--儲存商店的網站
    description VARCHAR(1000),--儲存商店的介紹文字
    PRIMARY KEY (store_id),--確保每個store_id是唯一的
    UNIQUE(tel_number),--確保tel_number的唯一性
    CHECK (CHAR_LENGTH(store_name) BETWEEN 1 AND 100),--檢查store_name長度介於1到100
    CONSTRAINT chk_tel_number CHECK (`tel_number` REGEXP '^09[0-9]{8}$' OR `tel_number` REGEXP '^05[0-9]{7}$'),--檢查電話正確輸入
    CONSTRAINT chk_address CHECK (address LIKE '雲林縣虎尾鎮%'),--檢查address的正確格式
    CONSTRAINT chk_website CHECK (`website` IS NULL OR `website` REGEXP '^https://' OR `website` REGEXP '^http://'),--檢查website的正確格式
    CHECK (description IS NULL OR CHAR_LENGTH(description) BETWEEN 0 AND 1000)--檢查description的長度介於0-1000個字元
);
```

| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|-------------|----------|----|--------------|
| `store_id`     | INTEGER | 店家代號 | 否 | 主鍵，自動產生(從1開始遞增)，限制為正整數 (UNSIGNED)，避免負數 |
| `store_name`   | VARCHAR(100) | 店家名稱 | 否 | 長度為1-100的字元，不可為空 |
| `tel_number`  | VARCHAR(10) | 電話號碼 | 是 | 阿拉伯數字，手機必須為10碼，以 "09" 開頭，或室內電話必須為9碼，以"05"開頭，有唯一性限制 |
| `address`  | VARCHAR(100) | 地址 | 否 | 長度為1-100的字元，必須以雲林縣虎尾鎮開頭，不可為空 |
| `website`  | VARCHAR(100) | 網站 | 是 | 長度為0-100的字元，若不為空，必須符合有效 URL 格式 |
| `description`  | VARCHAR(1000) | 簡介 | 是 | 長度為0-1000的字元 |

**說明：**
- AUTO_INCREMENT:自動產生一個整數，從1開始
- PRIMARY KEY:主鍵，確保唯一性
- CHECK:檢查約束

---

### 貼文資料表

```sql
CREATE TABLE user_posts (
    post_id INT UNSIGNED AUTO_INCREMENT,--每個貼文的識別碼
    user_id INT NOT NULL,--識別建立貼文的使用者
    store_id INT NOT NULL,--識別貼文所指的店家
    date DATETIME NOT NULL,--儲存貼文的建立日期和時間
    content VARCHAR(1000) NOT NULL,--儲存貼文的文字內容
    picture VARCHAR(255),--儲存與貼文相關的檔案路徑
    PRIMARY KEY (post_id),--確保每個post_id是唯一的識別碼
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON UPDATE CASCADE,-- 建立和users的user_id索引
    FOREIGN KEY (store_id) REFERENCES stores (store_id) ON UPDATE CASCADE;-- 建立和stores的stores_id索引
    CHECK (CHAR_LENGTH(content) BETWEEN 1 AND 1000),--檢查content的長度介於1-1000
);
```

| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|-------------|----------|----|--------------|
| `post_id`     | INTEGER | 貼文代號 | 否 | 主鍵，自動產生(從1開始遞增)，限制為正整數 (UNSIGNED)，避免負數 |
| `user_id`     | INTEGER | 使用者代號 | 否 | 連接到發送貼文的users |
| `store_id`     | INTEGER | 店家代號 | 否 | 連接到貼文所指的店家 |
| `date`   | DATETIME | 日期 | 否 | 格式為(YYYY-MM-DD HH:MM:SS) |
| `content`  | VARCHAR(1000) | 內文 | 否 | 長度為1-1000的文字 |
| `picture`  | VARCHAR(255) | 圖片 | 是 | 若不為空，網址必須符合有效 URL 格式 |

**說明：**
- AUTO_INCREMENT:自動產生一個整數，從1開始
- PRIMARY KEY:主鍵，確保唯一性
- CHECK:檢查約束
- FOREIGN KEY:用來引用另一個表中的PRIMARY KEY，建立並加強表之間的鏈接

---

### 評價資料表

```sql
CREATE TABLE user_reviews (
    review_id INT UNSIGNED AUTO_INCREMENT,--每個評論的識別碼
    user_id INT NOT NULL,--撰寫評論的使用者
    store_id INT NOT NULL,--被評論的店家
    title VARCHAR(10) NOT NULL,--評論的簡短標題
    score_date DATETIME NOT NULL,--記錄提交評論的日期和時間
    content VARCHAR(100) NOT NULL,--儲存評論的文字內容
    score INT NOT NULL,--儲存評論的評分
    PRIMARY KEY (review_id),--確保每個review_id是唯一的
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON UPDATE CASCADE,--建立和users的user_id索引
    CONSTRAINT chk_title CHECK (title REGEXP '^[a-zA-Z0-9\u4e00-\u9fa5]+$'),--檢查title的正確格式 --建立索引，並當users表的user_id被更新時，同步更新
    CONSTRAINT review_chk_store_id FOREIGN KEY (store_id) REFERENCES stores (store_id) ON UPDATE CASCADE  --建立和stores的stores_id索引
    CHECK (CHAR_LENGTH(content) BETWEEN 1 AND 100),--檢查content的長度介於1-100
    CHECK (score BETWEEN 1 AND 10),--檢查score介於1到10分
);
```

| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|-------------|----------|----|--------------|
| `reviews_id`     | INTEGER | 評論代號 | 否 | 主鍵，自動產生，限制為正整數 (UNSIGNED)，避免負數 |
| `user_id`     | INTEGER | 使用者代號 | 否 | 連接到發送評論的users |
| `store_id`     | INTEGER | 店家代號 | 否 | 連接到被評論的店家 |
| `title`     | VARCHAR(10) | 標題 | 否 | 長度為1-10的文字 |
| `score_date`   | DATETIME | 評價日期 | 否 | 格式為(YYYY-MM-DD HH:MM:SS) |
| `content`  | VARCHAR(100) | 內文 | 否 | 長度為1-100的文字 |
| `score`  | INTEGER | 評分 | 否 | 分數介於1-10分 |

**說明：**
- AUTO_INCREMENT:自動產生一個整數，從1開始
- PRIMARY KEY:主鍵，確保唯一性
- CHECK:檢查約束
- FOREIGN KEY:用來引用另一個表中的PRIMARY KEY，建立並加強表之間的鏈接

---

