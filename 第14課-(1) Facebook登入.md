# 第14課-(1) Facebook登入


#### 參考來源 http://ionicframework.com/docs/native/facebook/

#### 執行圖示 (**只能在手機執行, 在模擬器執行會失敗)
![GitHub Logo](/images/fig14-01.jpg)


##### 準備工作 (1)在https://developers.facebook.com/ 新增應用程式
![GitHub Logo](/images/fig14-01-0.jpg)


##### 準備工作 (2)應用程式編號(不可改), 顯示名稱(即先前新增應用程式之名稱), Google Play套件名稱(可自行更改)
![GitHub Logo](/images/fig14-01-1.jpg)


##### 準備工作 (3)取出個人FB基本資料測試
![GitHub Logo](/images/fig14-01-2.jpg)



#### (1)建立一個App, 名稱為 myApp:
```
ionic start myApp blank --save
```


#### (2)安裝外掛:
```
cd myApp

(a)
cordova plugin add cordova-plugin-facebook4 --variable APP_ID="應用程式編號" --variable APP_NAME="顯示名稱"

(b)
npm install --save @ionic-native/facebook

備註:步驟(a)中的應用程式編號及顯示名稱, 請參考自己在準備工作(2)中的設定.
```


#### (3)下載樣板程式, 解壓縮後複製到<myApp>資料夾中(取代舊檔案):
```
下載\ionic-測試-14-1.zip
```


#### (4.1) app.module.ts (增加引用 Facebook)
```javascript
import { BrowserModule } from '@angular/platform-browser';
import { ErrorHandler, NgModule } from '@angular/core';
import { IonicApp, IonicErrorHandler, IonicModule } from 'ionic-angular';
import { SplashScreen } from '@ionic-native/splash-screen';
import { StatusBar } from '@ionic-native/status-bar';

import { MyApp } from './app.component';
import { HomePage } from '../pages/home/home';
//---------------------------------------------------
import { Facebook } from '@ionic-native/facebook';
//---------------------------------------------------

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
    Facebook,      //增加
    {provide: ErrorHandler, useClass: IonicErrorHandler}
  ]
})
export class AppModule {}
```



#### (4.2) home.html
```html
<ion-header>
  <ion-navbar>
    <ion-title>
      Ionic Blank
    </ion-title>
  </ion-navbar>
</ion-header>

<ion-content padding>
  <button ion-button (click)="logout()">Logout</button>
  <button ion-button (click)="login()">Login</button>

  <p> 從FB取回的資料 </p>
  
  <p>
    id:{{id}}
  </p>

  <p>
    name:{{name}}
  </p>  

</ion-content>
```



#### (4.3) home.ts
```javascript
import { Component } from '@angular/core';
import { NavController } from 'ionic-angular';
import { Facebook, FacebookLoginResponse } from '@ionic-native/facebook';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
  //------------------------------
  // 成員
  //------------------------------
  id:string;
  name:string;  


  //------------------------------
  // 建構元
  //------------------------------
  constructor(public navCtrl: NavController, private fb: Facebook) {}


  //------------------------------
  // 登入
  //------------------------------
  login(){
      this.fb.login(['public_profile'])
          .then((res: FacebookLoginResponse) => {
              this.fb.api('/v2.9/me?fields=id,name,gender', [])
                  .then((profile)=> {
                       this.id=profile.id;
                       this.name=profile.name;
                  })
                  .catch(e=>{                       
                       this.id='id取得失敗';
                       this.name='name取得失敗';
                  });            
          })
          .catch(e => {
              this.id='登入失敗';
              this.name='登入失敗';
          });
  }


  //------------------------------
  // 登出
  //------------------------------
  logout(){
      this.fb.logout()
          .then(()=>{
              this.id='已登出';
              this.name='已登出';
          })
          .catch(e => {
              this.id='尚未登入';
              this.name='尚未登入';
          });
  }  
}
```



#### (5)本測試使用內容如下:
```
 d:\
  |___ <myApp>           
          |___ <src>
                 |___ <app>
                 |       |___ app.module.ts                  
                 |                  
                 |___ <pages>   
                         |___ <home> 
                                |___ home.html 
                                |___ home.ts                                 
```

