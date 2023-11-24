# 多層Line圖文選單製作方式

### LINE圖文選單是什麼？
LINE圖文選單（LINE Rich Menu）是LINE即時通訊應用程式上的一種功能，允許LINE用戶在聊天界面中以視覺化的方式與LINE官方帳號互動。圖文選單在LINE聊天視窗下方的固定選單，每次用戶不管是因為推播或其他互動打開品牌的LINE官方帳號時，都能看到這個版位。


## STEP 1 先設計好你的LINE圖文選單

```
圖片規格：2500 X 1686
圖片類型：png / jpg

```

## STEP 2 到LINE Developer後台設定 【Provider】

到 <a href ="https://developers.line.biz/console/"> LINE Developer</a>後台設定畫面，點選 Create a new provider -> Create a Messaging API channel 畫面，把資料填一填後，得到一個Channel access token，把他複製起來。

### STEP 2-1
![截圖 2023-11-25 上午12 31 28](https://github.com/yenming/line-rich-menus/assets/7127769/6d1ce6ec-ffb5-48e2-970f-131455ebe84c)

### STEP 2-2
![截圖 2023-11-25 上午12 34 06](https://github.com/yenming/line-rich-menus/assets/7127769/3a92b739-8813-4c8f-add2-04cc93ebbae9)


### STEP 2-3

![截圖 2023-11-25 上午12 36 44](https://github.com/yenming/line-rich-menus/assets/7127769/86f58b54-f8a5-401c-a4f2-afbffbfb86b8)



### STEP 2-4
![截圖 2023-11-25 上午12 36 36](https://github.com/yenming/line-rich-menus/assets/7127769/327e5dbe-2ecb-4bfd-a43c-572eb4af1c0e)





## STEP 3 透過Postman取得richMenuId


### STEP 3-1

    1.上方欄位POST 填入 <https://api.line.me/v2/bot/richmenu>

    2.欄位Authorization 選擇 Bearer 然後填入步驟二的 channel access token。

    3.欄位Header裡面欄位Key填入Content-Type，欄位Value填入 application/json。

    4.欄位Body下選raw，在raw內帶入json程式碼，範例如下。最後按送出會回傳 rich menu id，後面 API 會使用到。



參考SAMPLE 1 - richmenu-a

```
{
  "size": {
    "width": 2500,
    "height": 1686
  },
  "selected": true,
  "name": "richmenu-a",
  "chatBarText": "開啟數位營運術", // 這邊是底下Menu Bar的內容
  "areas": [
    {
      "bounds": {
        "x": 1250,
        "y": 4,
        "width": 1250,
        "height": 145
      },
        // 核心切換
      "action": {
        "type": "richmenuswitch",
        "richMenuAliasId": "richmenu-b",
        "data": "richmenu-changed-to-b"  
      }
    },
    {
      "bounds": {
        "x": 1601,
        "y": 173,
        "width": 899,
        "height": 671
      },
      "action": {
        "type": "message",
        "text": "/預約諮詢"
      }
    },
    {
      "bounds": {
        "x": 58,
        "y": 169,
        "width": 1485,
        "height": 660
      },
      "action": {
        "type": "uri",
        "uri": "https://www.everise.digital/"
      }
    }
  ]
}



```

參考SAMPLE 2 - richmenu-b

```
{
  "size": {
    "width": 2500,
    "height": 1686
  },
  "selected": true,
  "name": "richmenu-b",
  "chatBarText": "萬能店小二",  // 這邊是底下Menu Bar的內容
  "areas": [
    {
      "bounds": {
        "x": 8,
        "y": 12,
        "width": 1246,
        "height": 112
      },
     // 核心切換
      "action": {
        "type": "richmenuswitch",
        "richMenuAliasId": "richmenu-a",
        "data": "richmenu-changed-to-a"
      }
    },
    {
      "bounds": {
        "x": 1625,
        "y": 1011,
        "width": 780,
        "height": 631
      },
      "action": {
        "type": "message",
        "text": "/預約機器人試用"
      }
    },
    {
      "bounds": {
        "x": 1407,
        "y": 247,
        "width": 1048,
        "height": 635
      },
      "action": {
        "type": "uri",
        "uri": "https://www.everise.digital/robot"
      }
    }
  ]
}


```


補充說明：

size: 圖文選單圖片的長寬，必需為以下尺寸: 2500x1686、2500x843、1200x810、1200x405、800x540、800x270

selected: 選單是否預設開啟，我們設定為 true，預設開啟。

name: 選單名稱，用於識別和管理選單，不會顯示給使用者。

chatBarText: 顯示在聊天視窗圖文選單下方的選單文字。

areas: 用於定義按鈕的可點墼區域。

bounds 用於定義座標和大小

x、y: 起始座標

width: 寬度

height: 高度

action 用於定義觸發的動作

type: 動作類型

label: 動作標籤

text: 發送到聊天視窗的文字

data: 透過 webhook 回傳到後端的字串

### STEP 3-3 上傳成功

會顯示：

```
{
    "richMenuId": "richmenu-70b6550f5a2f7eaa6ffddcd70b6c9a05" // 這邊的內容後面會用到
}

```






## STEP 4 上傳圖文選單的圖片

1.首先程式碼建議是分開存哦，尤其新手很容犯錯誤，所以上一步驟的每一個richMenuId都有自己的程式碼，傳圖片也比照辦理。所以我們可以命名檔案叫做image menu-a (然後因為要上傳四張，所以 b c d就分別寫一支)

2.上方欄位一樣是要選POST哦，填入  https://api-data.line.me/v2/bot/richmenu/{richMenuId}/content  ，將上步驟得到的對應 {rich menu id} 帶入網址。所以，看是要要上傳哪張圖文選單，就放他的richMenuId到網址內

3.欄位Authorization 選擇 Bearer 然後一樣填入步驟二的 channel access token

4.欄位Header裡面欄位Key填入Content-Type，欄位Value填入 image/jpeg or image/png，看image檔案是哪一種

欄位Body那邊選擇 binary ，接著選擇對應的圖文選單上傳，最後按送出後，如果回傳狀態碼 200 與 {}， 代表成功！四張都傳上去哦！




## STEP 5 自定義連動 Rich Menu 別名

這個關鍵步驟，我們需要將先前建立圖文選單的別名 Alias 分配給 Rich Menu a, b, c, d，這樣才能互相連動起來～
記得嗎？因為在創建的程式碼中，我們有使用 
"type": "richmenuswitch","richMenuAliasId": "richmenu-b", 
這樣的語法（回去看你跑的程式碼～～有沒有看到呀）...
意思是點選這個區域後LINE圖文選單要switch去另外一個別名叫做richmenu-b的圖文選單，但是要是我們沒有在這步驟，
把定義傳送給LINE，他根本也不會知道誰是richmenu-b，因為我們寫richmenu-a時，
richmenu-b的richMenuId還沒生出來呢，對吧？所以囉，這邊算是一個把該『連結』的，都連起來的關鍵步驟！

一樣記得再開新檔案哦！上方欄位POST 填入 https://api.line.me/v2/bot/richmenu/alias

欄位Authorization 選擇 Bearer 然後一樣填入步驟二你的 channel access token

欄位Header裡面欄位Key填入Content-Type，欄位Value填入 application/json

欄位Body那邊raw帶入json程式碼，範例如下。最後按送出會狀態碼 200 與 {}，代表成功！

四個Alias都要做哦。


```
{
  "richMenuId": "richmenu-70b6550f5a2f7eaa6ffddcd70b6c9a05",
  "richMenuAliasId": "richmenu-b"
}

```

## STEP 6 設定哪一個圖文選單是預設出現

上方欄位POST 填入 https://api.line.me/v2/bot/user/all/richmenu/

欄位Authorization 選擇 Bearer 然後一樣填入步驟二的 channel access token

欄位Header裡面欄位Key填入Content-Type，欄位Value填入 application/json

欄位Body那邊帶入json程式碼，範例如下。最後按送出會狀態碼 200 與 {}，代表成功！

```
{
    "richMenuId": "richmenu-ecac97ec7caea9757c1e81d7f9999e31"
}

```


## STEP  上傳錯要改圖

上方欄位是改成選DELETE哦，填入 https://api.line.me/v2/bot/richmenu/{rich menu id}/，其他看是哪一個圖文選單需要刪除原本的圖，就把上步驟得到的對應 {rich menu id} 帶入網址。欄位Authorization 和欄位Header都和上傳時填一樣

得到狀態碼 200 與 {}， 代表成功刪除，就可以再重新上傳新圖。
