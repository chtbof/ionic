# 第02課 完成一個樣板App


#### 假設App將建在D槽, 建立在 myApp 的資料夾中.
```
 d:\
  |___ <myApp> 
          |___ (ionic建立的空白框架)
```


## (1) 建立myApp空白框架

```
d:
cd\
ionic start myApp blank
```
其中myApp是資料夾名稱, blank是空白框架, --v2是第二版, 圖示如下. 詳細請參考Ionic文件. <p>
![GitHub Logo](/images/fig02-01.jpg)



## (2)在瀏覽器上查看執行結果

```
cd myApp
ionic serve -l
```
其中 -l 是 lab, 表示在瀏覽器中出現手機模擬器, 執行圖示如下. <p>
![GitHub Logo](/images/fig02-02.jpg)



## (3)產生apk檔

```
cordova platform add android
cordova build
```
產生的apk檔將存在 D:\myApp\platforms\android\build\outputs\apk 中. <p>
![GitHub Logo](/images/fig02-03.jpg)



## (4)安裝apk欄

將apk檔以有線或無線方式傳給Android手機, 再安裝即可;<br>
也可以上傳到雲端, 如apkinstall(官網: `http://www.apkinstall.com/`)的服務, 上傳apk檔, 產生QR-code, 由手機掃瞄QR-code下載程式.<p>
圖示如下:<p>
![GitHub Logo](/images/fig02-04.jpg)
