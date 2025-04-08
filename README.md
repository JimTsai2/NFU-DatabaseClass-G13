# 虎科大美食評論系統
## 系統需求說明
提供兩大板塊功能，分別為清單和社群  
清單板塊推薦使用者附近幾個熱門的美食店家，提供使用者做為參考，並且允許使用者在前往消費過後對店家進行評價，可上傳評論以及自己所給予的分數（１～１０分）  
社群版塊則類似社交平台讓使用者主動分享發布推薦的店家貼文，可以發布照片還有文字和大家分享自己的心得，使用者可以留言回覆  

## 應用情境與使用案例
Ａ同學剛來虎尾讀書，到了中午用餐時間不清楚哪些店家不錯或有什麼特色或是提供哪些類型的餐點，因此他可以使用本系統提供的服務快速掌握周邊美食店家的相關細節，快速根據自己喜好選擇合適的店家用餐  
Ｂ同學在虎尾已經就讀一段時間，想提供美食相關資訊給大家分享，可以使用本系統分享自己在這邊的店家用餐的心得感想，C使用者在底下留言對於貼文發表看法  

## 完整性限制
### 唯一性限制：
一個使用者只能擁有一個帳號(Email跟電話不得重複出現在不同的使用者當中)  
餐廳名稱資訊不得重複(分店因地址不同不在此限)  
一則貼文或評價只能針對一個店家  

### 資料完整性：
每個評價必須附帶評分，不得單獨發送文字評論  
評分必須介於1至10分之間  
使用者必須要有Email和電話驗證才能發表評價、貼文跟留言  

## ER Diagram
![image](https://github.com/user-attachments/assets/18a3fcf6-a974-4cc9-9ac2-dfc94e028385)

## 組員
41143250 蔡士雋  
41143218 周羿廷  
41143217 阮國霖  
