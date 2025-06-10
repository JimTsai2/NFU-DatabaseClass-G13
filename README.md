# 虎科大美食評論系統

## 組員
| 學號 | 姓名 | 分工 |
|------|------|------|
| 41143250 | 蔡士雋 | 資料庫系統資料表建立、資料輸入、ppt製作 |
| 41143217 | 阮國霖 | 資料庫系統資料表建立、完整性限制、ppt製作 |
| 41143218 | 周羿廷 | 資料庫系統功能規劃、view規劃設計、word製作 |

## 系統需求介紹
本系統是一款以「在地美食推薦與交流」為核心的應用平台，主要提供兩大功能板塊：美食清單推薦系統與社群互動分享平台。系統旨在協助客人使用者快速找到優質店家、分享個人用餐經驗，也進一步給使用者了解店家資訊，除了可以對店家進行評分簡單評價外，並藉由社群互動促進資訊交流與在地美食文化推廣。

## 應用情境
### 使用者A（新生探索型）
客人A使用者剛來虎尾就讀，對當地店家不熟。中午用餐時間，他透過清單板塊查詢學校附近熱門美食，根據其他客人使用者的評價、照片與店家資訊，快速找到一間適合自己口味的小吃店。
### 使用者B（在地貢獻型）
客人B使用者使用者在虎尾生活一段時間，對幾間私房美食相當推薦。他使用社群板塊發表貼文，分享自己的用餐照片與詳細評價，鼓勵更多人一同體驗。
### 使用者C（行銷商家）
商家使用者C藉著自家的店舖資訊在資料庫當中，其他客人使用者就可以藉此認識並前來做客且分享自己的感想。

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

#### users(使用者)：一個使用者可以有0到多個貼文(0..n個user_posts)，一個使用者可以有1到多個評論(1..n個user_reviews)
- 包含：user_id(使用者id)、user_name(使用者名稱)、email(電子郵件)、phone_number(電話號碼)
#### user_posts(貼文)：一個貼文與一個使用者相關聯(1..1個users)
- 包含：user_id(使用者id)、type(類別)、date(發布日期)、content(內文)
#### user_reviews(評論)：一個評論與一個使用者相關聯(1..1個users)，一則評論與一個商家相關聯(1..1個stores)
- 包含：user_id(使用者id)、reviews_id(評論id)、store_id(商家id)、title(標題)、score_date(評論日期)、content(內文)
#### stores(商家)：一個商家可以有1到多個評論(1..n個user_reviews)
- 包含:store_id(商家id)、store_name(商家名稱)、tel_number(電話號碼)、address(地址)、description(評論)、website(網站)

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

**SQL說明：**
- INT UNSIGNED：使用無符號整數（只能是正整數，範圍為 0 到 4294967295）
- AUTO_INCREMENT：自動產生一個整數，從1開始
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
```sql
INSERT INTO users (user_id, user_name, email, phone_number, type) VALUES
(1, '虎大尾', 'huweibig@nfu.edu.tw', '0900000001', 'Proprietor'),
(2, '虎中尾', 'huweimedium@nfu.edu.tw', '0900000002', 'Customer'),
(3, '虎小尾', 'huweismall@nfu.edu.tw', '0900000003', 'Customer'),
(4, '虎大威', 'hubigwei@nfu.edu.tw', '0900000004', 'Customer'),
(5, '虎中威', 'humediumwei@nfu.edu.tw', '0900000005', 'Customer'),
(6, '虎小威', 'husmallwei@nfu.edu.tw', '0900000006', 'Customer'),
(7, '雲斗南', 'yundounan@nfu.edu.tw', '0900000007', 'Customer'),
(8, '雲斗六', 'yundouliu@nfu.edu.tw', '0900000008', 'Customer'),
(9, '雲西螺', 'yunxiluo@nfu.edu.tw', '0900000009', 'Customer'),
(10, '雲北港', 'yunbeigang@nfu.edu.tw', '0900000010', 'Customer');
```
![image](https://github.com/user-attachments/assets/347cd7bf-c9da-4133-8957-588f73543891)

---

### 商家資料表

```sql
CREATE TABLE stores (
    store_id INT UNSIGNED AUTO_INCREMENT,--每個商店的識別碼
    store_name VARCHAR(100) NOT NULL,--儲存商家的名稱
    tel_number VARCHAR(10) DEFAULT NULL,--儲存商家的電話號碼
    address VARCHAR(100) NOT NULL,--儲存商家的實體地址
    website VARCHAR(100),--儲存商家的網站
    description VARCHAR(1000),--儲存商家的介紹文字
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
| `tel_number`  | VARCHAR(10) | 電話號碼 | 是 | 阿拉伯數字，必須符合電話格式，手機必須為10碼，以"09"開頭，例如(0912345678)，或室內電話必須為9碼，以"05"開頭，例如(051234567)，有唯一性限制 |
| `address`  | VARCHAR(100) | 地址 | 否 | 長度為1-100的字元，必須以"雲林縣虎尾鎮"開頭，例如(雲林縣虎尾鎮88號)，不可為空 |
| `website`  | VARCHAR(100) | 網站 | 是 | 長度為0-100的字元，若不為空，必須以"https://"開頭，例如(https://example.com) |
| `description`  | VARCHAR(1000) | 簡介 | 是 | 長度為0-1000的字元 |

**SQL說明：**
- INT UNSIGNED：使用無符號整數（只能是正整數，範圍為 0 到 4294967295）
- AUTO_INCREMENT：自動產生一個整數，從1開始
- VARCHAR()：可變長度字串，最大長度為()中數值的字元
- NOT NULL：表示該欄位不能為空，必須提供值
- DEFAULT NULL：若未提供值，預設為 NULL（允許空值）
- PRIMARY KEY：主鍵，確保唯一性
- UNIQUE()：為()中的欄位設置唯一約束，確保在表中是唯一的
- CHECK()：為()中的欄位添加檢查約束
- CHAR_LENGTH(): 計算字串字元數
- CONSTRAINT chk_tel_number CHECK (tel_number REGEXP '^09[0-9]{8}$' OR tel_number REGEXP '^05[0-9]{7}$')：為 tel_number 添加名為 chk_tel_number 的檢查約束，驗證電話號碼格式
- CONSTRAINT chk_address CHECK (address LIKE '雲林縣虎尾鎮%')：為 address 添加名為 chk_address 的檢查約束，確保地址以「雲林縣虎尾鎮」開頭
- CONSTRAINT chk_website CHECK (website IS NULL OR website REGEXP '^https://' OR website REGEXP '^http://')：為 website 添加名為 chk_website 的檢查約束，驗證網站 URL 格式
- CHECK (description IS NULL OR CHAR_LENGTH(description) BETWEEN 0 AND 1000)：為 description 添加檢查約束，確保描述文字的長度在 0 到 1000 個字元之間，或為 NULL

**真實資料：**
```sql
INSERT INTO stores (store_id, store_name, tel_number, address, website, description) VALUES
(1, '麥當勞-虎尾新興餐廳', '056314141', '雲林縣虎尾鎮新興路88號1樓', 'https://www.mcdonalds.com/tw/zh-tw.html', '老字號的經典連鎖速食店，以漢堡和薯條聞名。'),
(2, '色鼎燒肉虎尾店', '056361748', '雲林縣虎尾鎮林森路二段125號', 'http://www.color-pot.com.tw/', NULL),
(3, '登旺簡速餐', '056327678', '雲林縣虎尾鎮文化路46號', NULL, NULL),
(4, '藤原-文化店', '0930857200', '雲林縣虎尾鎮文化路50號', NULL, '藤原壽司分店'),
(5, '藤原壽司', '056311452', '雲林縣虎尾鎮林森路一段533號', NULL, '藤原壽司日式炸豬排餐廳'),
(6, '50Pizza-虎尾人氣比薩焗烤美食專賣店', '056330606', '雲林縣虎尾鎮中正路235號', 'https://www.facebook.com/huwei50pizza/', '50Pizza在地經營已有20年，以平價焗烤美食著稱，品項眾多，且內有超過100個座位，非常適合一群朋友聚會，一定能找到自己喜歡吃的東西。'),
(7, '九鼎豆花(虎尾店)', '056328936', '雲林縣虎尾鎮西安街1號', NULL, NULL),
(8, '六扇門時尚湯鍋 虎尾中正店', '056337737', '雲林縣虎尾鎮中正路264號', 'https://www.6owldoor.com/', '六扇門時尚湯鍋擁有數十種湯頭、豐富自助吧、明亮舒適的環境，給每位來用餐的顧客都能以最平實的價格，來享受我們的精緻、我們的用心。'),
(9, '虎尾婆婆的店', NULL, '雲林縣虎尾鎮工專路27號', NULL, NULL),
(10, '古記燒臘店(虎尾)', '056328707', '雲林縣虎尾鎮中正路315號', NULL, NULL);
```
![image](https://github.com/user-attachments/assets/037af402-85e5-48af-8937-355339e39372)

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
    FOREIGN KEY (store_id) REFERENCES stores (store_id) ON UPDATE CASCADE,-- 建立和stores的stores_id索引
    CHECK (CHAR_LENGTH(content) BETWEEN 1 AND 1000),--檢查content的長度介於1-1000
);
```

| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|-------------|----------|----|--------------|
| `post_id`     | INTEGER | 貼文代號 | 否 | 主鍵，自動產生(從1開始遞增)，限制為正整數 (UNSIGNED)，避免負數 |
| `user_id`     | INTEGER | 使用者代號 | 否 | 連接到發送貼文的users，當users的user_id更新，這裡的user_id會跟著更新 |
| `store_id`     | INTEGER | 店家代號 | 否 | 連接到貼文所指的stores，當stores的store_id更新，這裡的store_id會跟著更新 |
| `date`   | DATETIME | 日期 | 否 | 格式為(YYYY-MM-DD HH:MM:SS)，例如(2025/06/15 00:00:00) |
| `content`  | VARCHAR(1000) | 內文 | 否 | 長度為1-1000的文字，不可為空 |
| `picture`  | VARCHAR(255) | 圖片 | 是 | 若不為空，則為檔案的原路徑 |

**SQL說明：**
- INT UNSIGNED：使用無符號整數（只能是正整數，範圍為 0 到 4294967295）
- AUTO_INCREMENT：自動產生一個整數，從1開始
- NOT NULL：表示該欄位不能為空，必須提供值
- DATETIME：儲存日期和時間
- VARCHAR()：可變長度字串，最大長度為()中數值的字元
- PRIMARY KEY：主鍵，確保唯一性
- FOREIGN KEY (user_id) REFERENCES users(user_id) ON UPDATE CASCADE：為 user_id 定義外鍵約束，參考 users 表的 user_id 欄位，確保 user_id 值存在於 users 表中，且當 users 表的 user_id 更新，user_posts 表的 user_id 會自動同步更新，維持參照完整性
- FOREIGN KEY (store_id) REFERENCES stores (store_id) ON UPDATE CASCADE：為 store_id 定義外鍵約束，參考 stores 表的 store_id 欄位，確保 store_id 值存在於 stores 表中，且當 stores 表的 store_id 更新，user_posts 表的 store_id 會自動同步更新，維持參照完整性
- CHECK (CHAR_LENGTH(content) BETWEEN 1 AND 1000)：為 content 添加檢查約束，確保文字內容長度在 1 到 1000 個字元之間
- CHAR_LENGTH(): 計算字串字元數

**真實資料：**
```sql
INSERT INTO user_posts (post_id, user_id, store_id, post_date, content, picture) VALUES
(1, 3, 2, '2025-01-09 19:27:55', '這次前往這家燒肉吃到飽餐廳用餐，整體感受非常棒，絕對值得推薦！從踏入餐廳的那一刻起，就能感受到服務人員的熱情與專業。帶位迅速且有禮，點餐時對於菜單內容解釋詳細，對於我們的疑問也都能耐心解答。最令人印象深刻的是，收盤換網的速度非常勤快，幾乎是我們一烤完就會立即上前詢問是否需要更換，讓整個用餐過程非常順暢，不會因為油煙或烤焦的殘渣影響食慾，服務態度真的沒話說。', 'D:\\Desktop\\ab.png'),
(2, 9, 4, '2025-01-16 10:32:47', '一碗熱騰騰的意麵上桌，香氣撲鼻而來，視覺和味覺都瞬間被挑逗。滑順的意麵吸飽了濃郁湯汁，每一口都是濃香四溢。', 'D:\\Desktop\\3r4teshdfnminb.png'),
(3, 8, 8, '2025-02-01 09:39:13', '第一次來虎尾的六扇門，來吃吃看跟石頭火鍋、色鼎，有什麼不一樣！我今天來這裡吃午餐，發現有二樓，一個女店員服務很好，送菜還分批，前幾個月去西螺的六扇門只有一樓，但我吃起來不差，也不會太鹹。', 'D:\\Desktop\\tvybh6nj7m89kg.png'),
(4, 6, 9, '2025-03-09 10:01:03', '一碗飯的價值，不只是飽足；在雲林虎尾的街頭，有一間默默耕耘多年的小吃店──虎尾婆婆的店。從凌晨四點就開始營業，這裡不只是補充體力的地方，更是許多人心中的「飽與暖」的起點。最美味的技巧，不是刀工或火候，而是藏在內心的大愛所烹調出的味道。這句話，完美詮釋了婆婆的堅持——即使物價飛漲，她多年來依然維持佛心銅板價，只為了讓每一位學生、勞工、在地居民都能吃得飽、吃得暖。婆婆親力親為，備料、煮湯樣樣親手來。店內「自助洗碗」成為特色文化，也讓客人更懂得珍惜這份善意。每一張牆上感謝狀，都是婆婆默默付出的證明。', 'D:\\Desktop\\kmgfccvbn.png'),
(5, 5, 6, '2025-03-29 20:23:00', '過年期間回味一下早期的$50 Pizza，人超級多等候時間真的蠻久的，從小到大，最喜歡的是泡菜牛肉焗烤飯，配酸梅湯真的很對味很平價，算是非精緻料理，但是很懷念的味道，小拼盤小炸物也是對味好吃，超喜歡玉米濃湯加酥皮的真的很好喝，整體偶爾聚餐吃一下真的還不錯，而且廁所跟整體環境都有重新整理過過感覺非常的乾淨，服務人員態度也不錯，老闆態度也不錯！', 'D:\\Desktop\\vdbxgbysh.png'),
(6, 2, 9, '2025-04-06 12:43:24', '沒有親眼所見真的不能想像會真實發生: 1. 東西份量超多價格超低。 2. 有兩個人來吃塞給婆婆一張大的。 3. 有個人自己帶了夾鏈袋要把吃不完的打包回去，婆婆還幫她裝滿。 4. 婆婆還主動要送給客人兩罐辣椒醬，客人堅持不收，最後勉強拿了一罐。 5. 婆婆要把另一罐送我，我婉拒了，我說我比較想要地瓜，原本想要全部一大堆都給我，最後我說我另外用袋子裝就好了，結果還是一大堆。 6. 第一次見有老闆沒事進來巡客人有沒有吃飽，要不要吃青菜的，一大盤只要10元。', NULL),
(7, 4, 5, '2025-04-17 17:45:45', '味噌湯免費，用料實在還有日本海苗可以加，六個豆皮壽司45元，連鎖的爭*根本沒得比，豬排蓋飯這豬排不是蓋的，連孩子都很好咬，炸豆腐挑嘴的女鵝讚不絕口，飽餐一頓溏心蛋讓我們開胃，又加點一個香草雞腿排，女鵝一連吃三塊，要繼續為傳承好味道的年輕人加油跟鼓勵。這除了停車不方便外，我們找不到缺點（可以停在附近收費停車場，一小時30便宜啦）', NULL),
(8, 8, 8, '2025-04-27 20:48:03', '鍋品選擇多樣，自助吧有飲料、小點心、爆米花、飯、王子麵、冰品等可享用，避開用餐高峰期，整體環境會算是蠻舒適的。簡單的平價小火鍋，不用有太高期待，也不用太苛求它。無附停車場，附近靠近科大的校舍周圍可以找到路邊停車位。', NULL);
(9, 5, 7, '2025-05-07 20:52:14', '在這裡吃過綜合豆花跟黑砂糖剉冰綜合豆花沒什麼問題，但是豆花有些配料吃得出來不太新鮮，有放了段時間的感覺。黑砂糖剉冰的冰不衛生也不乾淨，吃的時候能吃到不屬於冰的味道，冰有異味果然當天晚上肚子就非常痛，很確定不是其他飲食造成，持續了一整天拉肚子後狀況才減緩，非常難受。隔天有去看醫生，腸胃炎可能是店內沒冷氣冰保存不佳加上店內剉冰機在常溫下滋生細菌。如果不是鋼鐵胃建議真的不要吃這家店的冰品，會讓你肚子非常難受，豆花的話可以還算普通，至少不會讓我肚子痛。', NULL);
(10, 4, 9, '2025-05-10 12:14:03', '實在非常厲害！ 從60歲做到現在 已90歲囉~ 身體硬朗，講話有智慧又熱心助人~ 來用餐過の旅客們~ 都說 #物超所值  就連牆上の “價目表” 也從沒漲過~ 真の是~ #銅板價 佛心爆表‼️', NULL);
```

![image](https://github.com/user-attachments/assets/ff88285f-3bc4-42d6-90c0-7741b7e22a8b)

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
    FOREIGN KEY (store_id) REFERENCES stores (store_id) ON UPDATE CASCADE,--建立和stores的store_id索引
    CONSTRAINT chk_title CHECK (title REGEXP '^[a-zA-Z0-9\u4e00-\u9fa5]+$'),--檢查title的正確格式
    CHECK (CHAR_LENGTH(content) BETWEEN 1 AND 100),--檢查content的長度介於1-100
    CHECK (score BETWEEN 1 AND 10)--檢查score介於1到10分
);
```

| 欄位名稱 | 資料型別 | 中文說明 | 是否為空值 | 完整性限制 |
|----------|-------------|----------|----|--------------|
| `reviews_id`     | INTEGER | 評論代號 | 否 | 主鍵，自動產生，限制為正整數 (UNSIGNED)，避免負數 |
| `user_id`     | INTEGER | 使用者代號 | 否 | 連接到發送評論的users，當users的user_id更新，這裡的user_id會跟著更新 |
| `store_id`     | INTEGER | 店家代號 | 否 | 連接到被評論的stores，當stores的store_id更新，這裡的store_id會跟著更新 |
| `title`     | VARCHAR(10) | 標題 | 否 | 長度為1-10的文字，不可為空 |
| `score_date`   | DATETIME | 評價日期 | 否 | 格式為(YYYY-MM-DD HH:MM:SS)，例如(2025/06/15 00:00:00) |
| `content`  | VARCHAR(100) | 內文 | 否 | 長度為1-100的文字，不可為空 |
| `score`  | INTEGER | 評分 | 否 | 分數介於1-10分，不可為空 |

**SQL說明：**
- INT UNSIGNED：使用無符號整數（只能是正整數，範圍為 0 到 4294967295）
- AUTO_INCREMENT：自動產生一個整數，從1開始
- NOT NULL：表示該欄位不能為空，必須提供值
- VARCHAR()：可變長度字串，最大長度為()中數值的字元
- DATETIME：儲存日期和時間
- PRIMARY KEY:主鍵，確保唯一性
- FOREIGN KEY (user_id) REFERENCES users(user_id) ON UPDATE CASCADE：為 user_id 定義外鍵約束，參考 users 表的 user_id 欄位，確保 user_id 值存在於 users 表中，且當 users 表的 user_id 更新，user_reviews 表的 user_id 會自動同步更新，維持參照完整性
- FOREIGN KEY (store_id) REFERENCES stores (store_id) ON UPDATE CASCADE：為 store_id 定義外鍵約束，參考 stores 表的 store_id 欄位，確保 store_id 值存在於 stores 表中，且當 stores 表的 store_id 更新，user_reviews 表的 store_id 會自動同步更新，維持參照完整性
- CONSTRAINT chk_title CHECK (title REGEXP '^[a-zA-Z0-9\u4e00-\u9fa5]+$')：為 title 添加名為 chk_title 的檢查約束，確保標題僅包含字母、數字和中文字符
- CHECK (CHAR_LENGTH(content) BETWEEN 1 AND 100)：為 content 添加檢查約束，確保內容長度在 1 到 100 個字元之間
- CHAR_LENGTH(): 計算字串字元數
- CHECK (score BETWEEN 1 AND 10)：為 score 添加檢查約束，確保評分值在 1 到 10 之間

**真實資料：**
```sql
INSERT INTO user_reviews (review_id, user_id, store_id, title, score_date, content, score) VALUES
(1, 2, 2, '品質穩定又新鮮', '2025-02-12 15:03:21', '食材新鮮，出餐速度快，也有機器人送餐滿新奇的！', 10),
(2, 4, 1, '態度不佳', '2025-03-15 13:20:17', '餐點給錯 點餐員工態度不屑 明明是點麥脆雞腿套餐跟雞翅 哪來的吉事堡', 2),
(3, 10, 4, '值得一試', '2025-04-02 17:44:35', '好吃，老闆和老闆娘都很熱情，值得一試的好店家', 10),
(4, 7, 1, '請好好改進', '2025-04-09 23:51:50', '二樓點餐機狀態很不穩定，我走到二樓要用時，兩台點餐機都暫時不能使用，我只能乖乖再去一樓點餐，點完後上樓後，二樓的點餐機又好了，', 4),
(5, 4, 10, '好吃便宜又大碗', '2025-05-01 11:56:32', '菜色固定幾樣不太會變，提供紅茶跟鹹湯，可隨意選擇4樣菜，菜給的很多肉的部分都還不錯吃', 8),
(6, 3, 8, '今日又來這吃晚餐了', '2025-05-11 18:01:26', '今日晚餐來到這來吃，點了個番茄鍋吃，好吃，下次再來', 7),
(7, 7, 5, '豬排蓋飯', '2025-05-29 13:03:25', '豬排肉厚，蛋、洋蔥、醬汁吃起來好下飯，簡單但美味', 9),
(8, 9, 10, '確實如其他食客說的', '2025-06-01 19:15:15', '內用餐盤整個滿出來，配菜種類多樣，但口味稍重稍油…個人重口味食量大，覺得CP值蠻高，整體吃起來真的很爽…清淡飲食者應該不適合！', 6),
(9, 6, 3, 'CP值很高', '2025-06-03 12:54:46', '東西很好吃，尤其是紅槽排骨飯，吃起來味道有點像烤肉，一個便當才75元，吃得很飽', 8),
(10, 5, 7, '內用空間蠻大整體乾淨', '2025-06-04 19:07:47', '鐵支路附近，多種豆花可以選～還有分糖水跟豆漿的豆花，大碗真的超級飽', 6);
```
![image](https://github.com/user-attachments/assets/a4af7b81-7024-4b05-8673-b9a957af93e0)

---

## View SQL
### 查看所有使用者所發布的貼文數量
```sql
CREATE VIEW user_post_summary AS
SELECT 
    u.user_id,
    u.user_name,
    u.email,
    u.phone_number,
    u.type,
    COUNT(p.post_id) AS post_count
FROM users u
LEFT JOIN user_posts p ON u.user_id = p.user_id
GROUP BY u.user_id, u.user_name, u.email, u.phone_number, u.type;
```
![image](https://github.com/user-attachments/assets/6a8afbd5-9b87-40ff-a98d-82a4e45d90f6)


---
### 查看所有店家的總評價平均分數、總評價和總貼文數量
```sql
CREATE VIEW store_activity_summary AS
SELECT 
    s.store_id,
    s.store_name,
    s.address,
    COUNT(DISTINCT p.post_id) AS post_count,
    COUNT(DISTINCT r.review_id) AS review_count,
    AVG(r.score) AS average_score
FROM stores s
LEFT JOIN user_posts p ON s.store_id = p.store_id
LEFT JOIN user_reviews r ON s.store_id = r.store_id
GROUP BY s.store_id, s.store_name, s.address;
```
![image](https://github.com/user-attachments/assets/52b089bd-2c27-4627-a34d-5a3ece3df44f)

---
### 查看所有貼文並附上店家以及貼文的使用者資訊
```sql
CREATE VIEW post_details AS
SELECT 
    p.post_id,
    p.date,
    p.content,
    p.picture,
    u.user_id,
    u.user_name,
    u.type,
    s.store_id,
    s.store_name,
    s.address
FROM user_posts p
JOIN users u ON p.user_id = u.user_id
JOIN stores s ON p.store_id = s.store_id;
```
![image](https://github.com/user-attachments/assets/8e8c74d8-c0d6-4f2d-8276-6a9e37992e36)

---
