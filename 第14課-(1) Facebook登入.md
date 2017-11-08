# 第14課-(1) Facebook登入


#### 參考來源 http://ionicframework.com/docs/native/facebook/

#### 執行圖示 (**只能在手機執行, 在模擬器執行會失敗)
![GitHub Logo](/images/fig14-01.jpg)


##### 準備工作 (1)在https://developers.facebook.com/ 新增應用程式
![GitHub Logo](/images/fig14-01-0.jpg)


##### 準備工作 (2)應用程式編號(不可改), 顯示名稱(即先前新增應用程式之名稱), Google Play套件名稱(與config.xml的id相同)
![GitHub Logo](/images/fig14-01-1.jpg)

##### 準備工作 (3)在建立應用程式過程中, 將被要求產生開發密鑰雜湊及產生發行密鑰雜湊.
```
**需先下載安裝openssl程式

(1)產生開發密鑰雜湊 
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64

(2)產生發行密鑰雜湊
keytool -exportcert -alias YOUR_RELEASE_KEY_ALIAS -keystore YOUR_RELEASE_KEY_PATH | openssl sha1 -binary | openssl base64

產生的密鑰雜湊, 加入fb應用程式的金鑰雜湊中, 如下:
```
![GitHub Logo](/images/fig14-01-3.jpg)

##### 準備工作 (4)取出個人FB基本資料測試
![GitHub Logo](/images/fig14-01-2.jpg)


## 本測試內容如下:
```
 d:\
  |___ <myApp>  
          |___ config.xml
          |
          |___ <src>
                 |___ <app>
                 |       |___ app.module.ts                  
                 |                  
                 |___ <pages>   
                         |___ <home> 
                                |___ home.html 
                                |___ home.ts                                 
```



### (1)建立一個App, 名稱為 myApp:
```
ionic start myApp blank --save
```


### (2)安裝外掛:
```
cd myApp

(a)
cordova plugin add cordova-plugin-facebook4 --variable APP_ID="應用程式編號" --variable APP_NAME="顯示名稱"

(b)
npm install --save @ionic-native/facebook

備註:步驟(a)中的應用程式編號及顯示名稱, 請參考自己在準備工作(2)中的設定.
```


### (3.1) config.xml(部分, 修改widget id, 內容與fb應用程式中的Google Play套件名稱相同)
```
<?xml version='1.0' encoding='utf-8'?>
<widget id="com.abc.myFBapplication" version="0.0.1" xmlns="http://www.w3.org/ns/widgets" xmlns:cdv="http://cordova.apache.org/ns/1.0">
    <name>myApp</name>   
```

### (3.2) app.module.ts (增加引用 Facebook)
```javascript
import { BrowserModule } from '@angular/platform-browser';
import { ErrorHandler, NgModule } from '@angular/core';
import { IonicApp, IonicErrorHandler, IonicModule } from 'ionic-angular';
import { SplashScreen } from '@ionic-native/splash-screen';
import { StatusBar } from '@ionic-native/status-bar';

import { MyApp } from './app.component';
import { HomePage } from '../pages/home/home';
import { Facebook } from '@ionic-native/facebook';  //**增加

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
    Facebook,      //**增加
    {provide: ErrorHandler, useClass: IonicErrorHandler}
  ]
})
export class AppModule {}
```



### (3.3) home.html
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



### (3.4) home.ts
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


## 測試失敗的解決方法:

執行時發生Invalid Key Hash...的問題, 如下:
![GitHub Logo](/images/fig14-err-1.jpg)

#### 方法(1), 移除手機的facebook app, 或
#### 方法(2), 在fb應用程式中加入手機顯示的Hask Key, 如下:
![GitHub Logo](/images/fig14-err-2.jpg)


