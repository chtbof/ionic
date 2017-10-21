# 第19課-(1) 使用 Wikitude AR


#### 執行圖示
![GitHub Logo](/images/fig19-01.jpg)


### 1. 快速建立

#### (1)建立一個App, 名稱為 myApp:
```
ionic start myApp blank --save
```

#### (2)在<下載>資料夾中, 下載 myAR.zip, 解壓縮後, 將解壓的src中[所有檔案], 複製並取代 myApp/src 中的程式.


#### (3)修改 myApp/config.xml, 其中 android-minSdkVersion 改為至少 19, 如下:
```
<preference name="android-minSdkVersion" value="19"/>
```

#### (4)加入android平台程式:
```
cd myApp
cordova platform add android
```

#### (5)加入Wikitude外掛:
```
ionic plugin add https://github.com/Wikitude/wikitude-cordova-plugin.git
```

#### (6)編譯並在模擬器上執行 (wikitude不能在模擬器上執行, 應該有錯):
```
ionic serve -l -c
```


#### (7)產生apk檔, 完成後發佈到手機上執行:
```
cordova build
```


### 2. 一步一步建立

#### myAR.zip解壓後:
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

#### (1)建立一個App, 名稱為 myApp:
```
ionic start myApp blank --save
```

#### (2)增加一個頁面:
```
cd myApp
ionic g page my-ar
```

#### (3)複製 myAR.zip解壓之src/app中的 WikitudePlugin.d.ts 到 myApp中的相對位址


#### (4)依照 myAR.zip解壓之src/app中的 app.component.ts 說明, 修改 myApp中的相對的 app.component.ts 檔案
```
加入了兩段程式, 並在第2段程式中貼入自己的skdKey.
(在 http://www.wikitude.com/developer/licenses 申請一個免費的skdKey)

.
.
//-------------------------------------------------------------------------------------
// 在 http://www.wikitude.com/developer/licenses 申請一個免費的skdKey, 並貼入以下:
//-------------------------------------------------------------------------------------      
WikitudePlugin._sdkKey = ".....貼入自己的skdKey....."
.
.
```

#### (5)複製 myAR.zip解壓之src/assets中的 [所有程式] 到 myApp中的相對位址
```
如果需要更多的應用版型, 可在以下網址下載:
https://github.com/Wikitude/wikitude-sdk-samples
```

#### (6)複製 myAR.zip解壓之src/pages/my-ar中的 [所有程式] 到 myApp中的相對位址
```
在 my-ar.ts程式中, 有傳送的座標參數, 可自行修改, 如下:

.
.
//---------------------------------------------------------------------------------------------
//傳參數給 [www/assets/09_ObtainPoiData_1_FromApplicationModel/fromapplicationmodel.js]的函式
//---------------------------------------------------------------------------------------------
WikitudePlugin.callJavaScript('World.loadPoisFromJsonData([{"id": "1","longitude": "121.520927","latitude": "25.035017","description": "國家音樂廳","altitude": "100.0", "name": "音樂廳"}])');
//---------------------------------------------------------------------------------------------
.
.
```

#### (7)複製 myAR.zip解壓之src/pages/home中的 [所有程式] 到 myApp中的相對位址


#### (8)修改<myApp>中的 config.xml, 其中 android-minSdkVersion 改為至少 19, 如下:
```
<preference name="android-minSdkVersion" value="19"/>
```


#### (9)加入android平台程式:
```
cd myApp
cordova platform add android
```

#### (10)加入Wikitude外掛:
```
ionic plugin add https://github.com/Wikitude/wikitude-cordova-plugin.git
```

#### (11)編譯並在模擬器上執行 (wikitude不能在模擬器上執行, 應該有錯):
```
ionic serve -l -c
```


#### (11)產生apk檔, 完成後發佈到手機上執行:
```
cordova build
```
