---
tags: Zenbo, ATQ
---

# 機器人問卷系統設計


## 1. Structure

![](https://i.imgur.com/TxzLVZO.jpg)


## 2. Script Design
### **Build Script**
1. 創建csv檔(逗點分隔)
(e.g. memory.csv)
2. 一個csv檔為一份腳本，一份腳本裡面可以有多個腳本題目。
3. 一個腳本題目包含ROOT和BRANCH

5. 題目ID不可重複(見Function Tags各標籤限制)

### **Function Tags**
|IS_ASK|KEYWORD|ID|IS_ROOT|TAG|SPEAK_TEXT|Next_TAG|IS_LIMIT|VOLUME|SPEED|PITCH|ACTION_TYPE|TIMELIMIT|SPEC_PRIORITY|LOOP_MAX|AFTER_LOOP_MAX|MATCH_TYPE|IF_NONE_MATCH|QNAME|
| --------| -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |-------- |-------- |-------- |-------- |-------- |-------- |-------- |-------- |-------- |-------- |
|<default>:0|<str>;<str>;<str>|<str>|<str>|<str>|<str>|<str>|<str>|<str>|<str>|<str>|<str>|<str>|<str>|<str>|<str>|<str>|<str>|<str>|
:::info
:label:**IS_ASK**
:::
1. Default='0'，做為紀錄狀態使用。

:::info
:label:**KEYWORD**
:::
1. 可用 ";"，增加不同的keyword。 
e.g. <str1>;<str2>;...

2. 配合MATCH_TYPE使用。
**[Fuzzy Match情況]**
若<str1><str2>任一關鍵字之詞向量能與input之詞向量，相關度達閾值，且相較其他分支相似度最高，則可讓該分支做為下一次回答。
**[Exact Match情況]**
若input含有<str1><str2>任一關鍵字，則可讓該分支做為下一次回答。

:::info
:label:**ID**
:::
1. 代表每一份
:::info
:label:**IS_ROOT**
:::
:::info
:label:**TAG**
:::
:::info
:label:**SPEAK_TEXT**
:::
:::info
:label:**Next_TAG**
:::
:::info
:label:**IS_LIMIT**
:::
:::info
:label:**VOLUME**
:::
:::info
:label:**SPEED**
:::
:::info
:label:**PITCH**
:::
:::info
:label:**ACTION_TYPE**
:::
:::info
:label:**TIMELIMIT**
:::
:::info
:label:**SPEC_PRIORITY**
:::
:::info
:label:**LOOP_MAX**
:::
:::info
:label:**AFTER_LOOP_MAX**
:::
:::info
:label:**MATCH_TYPE**
:::
:::info
:label:**IF_NONE_MATCH**
:::
:::info
:label:**QNAME**
:::
聊天Output會和Keywords進行比對。
[用法]
1. 可用"**;**"，增加不同的keyword
2. 可以寫整個句子，但記得測試效果。

#### **Question_Root:**
題號。若是是「對話題組」，無論root或分支請都設為相同的題號。

e.g.1
|Keywords|  Question_Root  | Question_Branch| Tag| SaySentence| Next_Tag| Priority|
| -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|這樣你清楚了嗎?|1|1|0|這樣你清楚了嗎?|1|0|
|好;知道了;了解|1|0|1| 我清楚了     | end|0 |
|不;我不清楚|1|0|1| 好的我再說一次blabla，這樣你清楚了嗎?|1 |0 |
|也許我沒說清楚|1|0|1| 也許我沒說清楚，我再說一次blabla，這樣你清楚了嗎?|1 |0 |

#### **Question_Branch:** 
若「對話」是該「對話題組」的第一題，請設為1
若「對話」是該「對話題組」的分支或是非第一題，請設為0

#### **Tag:**
若「對話」是該「對話題組」的第一題，請設為0。餘者則可以依照自己想要的順敘編號。
e.g.1 有很多列都具有**標籤1**，程式將會依照關鍵字判斷Input屬於哪一個意圖。

#### **SaySentence:**
核對到項目後，機器人將講的話。

#### **Next_Tag:**
下一次對話的標籤。
若為數字，則會跳至該數字標籤判斷。
若為end，則會結束「對話題組」的狀態，繼續展開閒聊模式。

#### **Priority:**
題目之間具有先後關係。
e.g.
比方說A,B兩個題目分別在QA問卷和QB問卷，且A要先問過才能問B:
QB_mem.csv裡面，B問題的priority欄位設**1**
QA_mem.csv裡面，A問題即將結束的分支中的Next_Tag欄位設 **end;QB;<B_Question_Root>**

---
### Example

### 1. 只講單句話，講完便結束。
|Keywords|  Question_Root  | Question_Branch| Tag| SaySentence| Next_Tag| Priority|
| -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|外向;熱情|1|1|0|一般來說，請問你是個外向的熱情的人嗎？|end|0|

### 2. 進入對話題組，雖機器人有問對方問題，但無論對方回答什麼，都講我們想講的。
|Keywords|  Question_Root  | Question_Branch| Tag| SaySentence| Next_Tag| Priority|
| -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|看法;機器人;想知道|1|1|0|你覺得我是個機靈可愛的機器人嗎?|1|0||這樣啊，我知道了我會幫你跟我的工程師說|1|0|
|這樣啊，我知道了我會幫你跟我的工程師說|1|0|1|這樣啊，我知道了我會幫你跟我的工程師說|end|0|

### 3. 進入對話題組，依照對方的意圖，判斷機器人該說什麼。
舉例以「有了解」/「沒有了解」兩種意圖舉例。
**注意:** 為了避免機器人兩種意圖都沒有理解，需設第三行「提醒使用者該回答什麼」以及「回到判斷中」。
(當然也可以先暫時繞出去，但無法使用end繞到自然聊天系統再繞回這題來)

|Keywords|  Question_Root  | Question_Branch| Tag| SaySentence| Next_Tag| Priority|
| -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|這樣你清楚了嗎?|1|1|0|這樣你清楚了嗎?|1|0|
|好;知道了;了解|1|0|1| 我清楚了     | end|0 |
|不;我不清楚|1|0|1| 好的我再說一次blabla，這樣你清楚了嗎?|1 |0 |
|也許我沒說清楚|1|0|1| 也許我沒說清楚，我再說一次blabla，這樣你清楚了嗎?請告訴我清楚或是不清楚|1 |0 |

### 4. A,B兩個題目分別在QA問卷和QB問卷，且A要先問過才能問B:

1.**A 在QA_mem.csv中，為該問卷第四題組**
A問題即將結束的分支中的Next_Tag欄位設 **end;QB;<B_Question_Root>**
2.**B 在QB_mem.csv中，為該問卷第五題組**
B問題的priority欄位設**1**


**[QA_mem.csv]**

|Keywords|  Question_Root  | Question_Branch| Tag| SaySentence| Next_Tag| Priority|
| -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|請問你早餐吃了嗎?|4|1|0|請問你早餐吃了嗎?|1|0|
|吃了;好飽|4|0|1| 哈哈哈哈，我也很飽，我吃了車輪餅     | 2|0 |
|還沒;不清楚|4|0|1| 這樣啊，我有秘密美食景點喔|2 |0 |
|None|4|0|2| 我知道最近寧夏夜市新開一家車輪餅店很好吃喔|end;QB;5 |0 |

**[QB_mem.csv]**

|Keywords|  Question_Root  | Question_Branch| Tag| SaySentence| Next_Tag| Priority|
| -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|夜市;吃東西;出去玩|5|1|0|還記得我之前提到寧夏夜市嗎?|1|1|
|好;知道;了解|5|0|1| 哈哈哈哈，要一起去嗎?   | end|0 |
|不;我不清楚|5|0|1| 這樣啊，我知道最近寧夏夜市新開一家車輪餅店很好吃喔|end |0 |

---
## 資料格式

**1.目前程式DB**
```

self.DB = {
  '<問卷名稱>': {
    'mem': [
      [0, <問題>, Id, Id_branch, tag, reply, next_tags, volume , speed, pitch, slmode],
      [0, <問題>, Id, Id_branch, tag, reply, next_tags, volume , speed, pitch, slmode]
    ],
    's2v': [ 
      [<題組一:第一題VEC>],
      [<題組二:第一題VEC>],
      ...],
    'mem_branch':[
      [0, <問題>, Id, Id_branch, tag, reply, next_tags, volume , speed, pitch, slmode],
      [0, <問題>, Id, Id_branch, tag, reply, next_tags, volume , speed, pitch, slmode]
    ],
    
    #每個題組最多可以設計編號1-99的標籤
    #每個標籤是<list>
    '1':[                               #題組一                         
      [],                               #標籤0                 
      [                                 #標籤1
        [                               #標籤1;「是」的選項
          [[<vec1>],[<vec2>],[<vec3>]], #標籤1，每個關鍵字的vec
          [<ans1>,<ans2>] ,             #標籤1，可能的回覆
          <next_tags> ,                 #標籤1，下一個標籤
          <volume> ,                    #標籤1，音量
          <speed> ,                     #標籤1，說話速度
          <pitch>,                      #標籤1，說話音高
          <slmode>                      #標籤1，說話模式:說/說後聽
        ],
        [                               #標籤1;「否」的選項
          [[<vec1>],[<vec2>],[<vec3>]], #標籤1，每個關鍵字的vec
          [<ans1>,<ans2>] ,             #標籤1，可能的回覆
          <next_tags> ,                 #標籤1，下一個標籤
          <volume> ,                    #標籤1，音量
          <speed> ,                     #標籤1，說話速度
          <pitch>,                      #標籤1，說話音高
          <slmode>                      #標籤1，說話模式:說/說後聽
        ],
        [                               #標籤1;「都不是上面兩概念」的選項
          [[<vec1>],[<vec2>],[<vec3>]], #標籤1，每個關鍵字的vec
          [<ans1>,<ans2>] ,             #標籤1，可能的回覆
          <next_tags> ,                 #標籤1，下一個標籤
          <volume> ,                    #標籤1，音量
          <speed> ,                     #標籤1，說話速度
          <pitch>,                      #標籤1，說話音高
          <slmode>                      #標籤1，說話模式:說/說後聽
        ]
      ],
      [],[],[],...,                     #標籤2-9，可能都沒有
      [                                 #標籤10
        [                               #標籤10，唯一的選項
          [[]], #標籤10，每個關鍵字的vec
          [<ans1>,<ans2>] ,             #標籤10，可能的回覆
          <next_tags> ,                 #標籤10，下一個標籤
          <volume> ,                    #標籤10，音量
          <speed> ,                     #標籤10，說話速度
          <pitch>,                      #標籤10，說話音高
          <slmode>                      #標籤10，說話模式:說/說後聽
        ]
      ],
      [],[],[],...[]                    #標籤11-99
    ],
    
    '2':[],                             #題組二
}
```
---
## **要處理的問題**
- [x] 新增：自我介紹
>- [x] 新增：機器人講話時看向其他人 -> 等Edwinn
>- [x] 需要加速，寄Sample方便Edwinn修改
> ``` 
>jsonObject = {"system_info":{
>                 "version_info":"ver1.0"
>                },
>               "event_vad_status":{
>                 "vad_status":"FinishCSR",
>                 "sound_level":0,
>                 "doa":2
>                }
>               }
>```
> ```@Override
>public void onVoiceDetect(JSONObject jsonObject) {
>         try{
>             JSONObject event = >jsonObject.getJSONObject("event_vad_status");
>             String vadStatus = event.getString("vad_status");
>             if (vadStatus.equals("FinishCSR")){
>                 float doaresult = event.getInt("doa");
>                 robotAPI.utility.lookAtUser(doaresult);
>             }
>         } catch (Exception e){
>             Log.d(TAG, e.toString());
>         }
>}

1. 聊天紀錄/資料怎麼存？
- [x] 讓檔案新增第一欄狀態列
- [x] 修改mem_init讀入方式
- [x] vec_init需要成為獨立動作，若有新問卷才特別啟用；否則平常只會讀<問卷名差>_vec.csv
- [x] vec_init存成json
- [x] 新增load_vec
- [x] 修正init_db：直接執行ChatQSystem.py可以重新算出VEC,此外則load personal vec file
- [x]  檢查點1:
 > - [x] 有複數標籤的選項，選項中儘管只有一句話也要加";"
 > 檔案需要修改，可能交給家齊
 > (會卡到後面開發，已修正)
2. 呼喚上一個使用者的狀態
> DB要能回復成上一個使用者的狀態
> ->是否問過、priority是否解除。
> 依照受試者ID
- [x] 新增test/formal目錄
- [x] 預想架構
:file_folder: <font color="black">zenboEXP</font>
\- → :file_folder: <font color="orange">dataFormal</font> #放置正式資料
\- — — →:file_folder: <font color="white">subject_001</font> 
\- — — — — →:file_folder: <font color="white">video</font>
\- — — — — →:file_folder: <font color="white">question</font>
\- — — →:file_folder: <font color="white">subject_002</font>
\- → :file_folder: <font color="orange">dataTest</font> #放置測試資料
\- — — →:file_folder: <font color="white">test_001</font> 
\- — — — — →:file_folder: <font color="white">video</font>
\- — — — — →:file_folder: <font color="white">question</font>
\- — — →:file_folder: <font color="white">test_002</font>
- [x] 新增ObjectId 和提取方式 
- [x] 自動檢查目錄/創建目錄
- [x] 系統重新開機後timestamp重新計時問題
> 設立時間標籤
> 每句對話都要有
> 聊天紀錄
> 

