# 第17課-(2) 使用Firebase推播


## App部份

#### (1)建立一個App, 名稱為 myApp:
```
ionic start myApp blank --v2
```


#### (2)增加以下外掛
```
cordova plugin add cordova-plugin-fcm
npm install --save @ionic-native/fcm
```


#### (3)加入第17課-(1)中建立的google-services.json, 加入路徑如下:
```
 d:\
  |___ <myApp>   
          |___ google-services.json
          |___ config.xml          
          |           
          |___ <src>                 
                 |___ <app>             
                 |      |___ app.module.ts
                 |      
                 |___ <pages>   
                        |___ <home> 
                                |___ home.ts 
```



#### (4) 修改config.xml, 其中 com.abc.myapplication 應與第17課-(1)的第5步驟設定套件名稱相同.
```
<widget id="com.abc.myapplication" .....>
```



#### (5) 修改config.xml, 其中關於Splash的偏好設定改成以下:
```
  <preference name="SplashMaintainAspectRatio" value="true"/>
  <preference name="FadeSplashScreenDuration" value="300"/>
  <preference name="SplashScreenDelay" value="2000"/>
  <preference name="FadeSplashScreenDuration" value="1000"/>
  <preference name="SplashScreen" value="screen"/>
  <preference name="ShowSplashScreenSpinner" value="true"/>
  <preference name="AutoHideSplashScreen" value="false"/>
```



#### (6) 修改home.ts, 加入FCM, 如下:
```javascript
import { Component } from '@angular/core';
import { NavController } from 'ionic-angular';

//------------------------
// 增加引用
//------------------------
import { FCM } from '@ionic-native/fcm';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
  //------------------------
  // 建構元
  //------------------------
  constructor(public navCtrl: NavController, private fcm: FCM) {        
  }
}
```



#### (7) 修改app.module.ts, 加入FCM, 如下:
```javascript
import { BrowserModule } from '@angular/platform-browser';
import { ErrorHandler, NgModule } from '@angular/core';
import { IonicApp, IonicErrorHandler, IonicModule } from 'ionic-angular';
import { SplashScreen } from '@ionic-native/splash-screen';
import { StatusBar } from '@ionic-native/status-bar';

import { MyApp } from './app.component';
import { HomePage } from '../pages/home/home';

//------------------------
// 增加引用
//------------------------
import { FCM } from '@ionic-native/fcm';

@NgModule({
  declarations: [
    MyApp,
    HomePage
  ],
  imports: [
    BrowserModule,
    IonicModule.forRoot(MyApp)
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    MyApp,
    HomePage
  ],
  providers: [
    StatusBar,
    SplashScreen,
    FCM,     /** 增加 **/
    {provide: ErrorHandler, useClass: IonicErrorHandler}
  ]
})
export class AppModule {}
```


## 測試推播

#### (1)進入 console.firebase.google.com, 選擇在第17課-(1)中建立的專案, 開始使用Notifications功能
![GitHub Logo](/images/fig17-2-01.jpg)


#### (2)選擇新增訊息
![GitHub Logo](/images/fig17-2-02.jpg)


#### (3)加入訊息文字, 選擇應用程式, 即可推播訊息
![GitHub Logo](/images/fig17-2-03.jpg)
