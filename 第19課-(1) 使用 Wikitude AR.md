# 第19課-(1) 使用 Wikitude AR


#### 執行圖示
![GitHub Logo](/images/fig19-01.jpg)



#### (在<下載>資料夾中的 myAR.zip, 解壓縮後的結構如下:)
```
myAR.zip解壓
  |
  |___ <src>    
         |___ <app>   
         |       |___ app.component.ts                  
         |       |___ WikitudePlugin.d.ts  
         | 
         |___ <assets>  
         |       |___ (有多個AR應用版型)
         |
         |___ <pages>   
                 |___ <home> 
                 |      |___ home.html 
                 |      |___ home.ts   
                 |       
                 |___ <my-ar> 
                        |___ my-ar.html 
                        |___ my-ar.ts                                
                            
```

# 建立步驟:


#### (1)建立一個App, 假設名稱為 myApp:
```
ionic start myApp blank --v2
```


#### (2)增加一個頁面:
```
cd myApp
ionic g page my-ar
```


#### (3)在<下載>資料夾中下載 myAR.zip, 下載後解壓縮.(內容為樣板程式及Wikitude檔案)


#### (4)複製 [解壓縮的 src/app/WikitudePlugin.d.ts] 到 [自己的 src/app/] 中.


#### (5)修改 [自己的 app.component.ts], 包括:

##### (5-1)在 [解壓縮的 src/app/app.component.ts] 中有2段註解的程式, 複製並貼在 [自己的 app.component.ts] 中.
##### (5-2)在 http://www.wikitude.com/developer/licenses 申請一個免費的skdKey.
##### (5-3)在 [自己的 app.component.ts] 中, 修改sdkKey變數值, 改成自己申請的skdKey, 如下:
```
(sdkKey很長, 類似:)
WikitudePlugin._sdkKey = "adG+1AQa76L/KtWTwxJRtjW88L6fDpjdX+N6fLceZYbJigDIw94PBN4.......................DgEaR6gGFOOBEZ2QFeYZ5pEGHp60="
```


#### (6)複製 [解壓縮的 src/assets/所有程式], 貼到 [自己的 src/assets/] 中.
```
如果需要更多的應用版型, 可在以下網址下載:
https://github.com/Wikitude/wikitude-sdk-samples
```

#### (7)複製 [解壓縮的 src/pages/my-ar/所有程式], 貼到 [自己的 src/pages/my-ar/] 中.
```
在 [自己的/src/pages/my-ar/my-ar.ts] 中, 目標座標固定在國家音樂廳, 有需要請自行修改, 如下:

//---------------------------------------------------------------------------------------------
WikitudePlugin.callJavaScript('World.loadPoisFromJsonData([{"id": "1","longitude": "121.520927","latitude": "25.035017","description": "國家音樂廳","altitude": "100.0", "name": "音樂廳"}])');
//---------------------------------------------------------------------------------------------
```


#### (8)複製 [解壓縮的 src/pages/home/所有程式] 到 [自己的 src/pages/home/] 中.
```
複製的home是一個測試頁面, 其中有一個按鈕, 按下後打開 my-ar 頁面.
```

#### (9)修改 [自己的 config.xml], 其中 android-minSdkVersion 改為至少 19, 如下:
```
<preference name="android-minSdkVersion" value="19"/>
```


#### (10)加入android平台程式:
```
cd myApp
cordova platform add android
```

#### (11)加入Wikitude外掛:
```
ionic plugin add https://github.com/Wikitude/wikitude-cordova-plugin.git
```

#### (12)編譯 (wikitude不能在模擬器上執行, 應該有模擬器錯誤, 不必理會):
```
ionic serve -l -c
```


#### (13)產生apk檔, 完成後發佈到手機上執行:
```
cordova build
```

#### (14)在手機執行前, 請先開啟手機的定位功能.

