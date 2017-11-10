# 第09課-(5) Get-傳參數-帳密


#### 如在Chrome中執行模擬器, 先安裝Chrome Extension: Allow-Control-Allow_Origin



#### 執行圖示
![GitHub Logo](/images/fig09-05.jpg)



## 網站部份

#### (1)建立Express網站, 名稱為 myWeb:
```
express myWeb -ejs
cd myWeb
npm install
```


#### (2)追加外一個外掛, cors:
```
npm install cors --save
```


#### (3)下載樣板網站, 解壓縮後複製到<myWeb>資料夾中(取代舊檔案):
```
下載\myWeb-接收文字參數.zip
```


## App部份

#### (1)建立一個App, 名稱為 myApp:
```
ionic start myApp blank --v2
```

#### (2)下載樣板程式, 解壓縮後複製到<myApp>資料夾中(取代舊檔案):
```
下載\ionic-測試-09-5.zip
```


#### (3.1) app.module.ts (增加匯入 HttpModule)
```javascript
import { BrowserModule } from '@angular/platform-browser';
import { ErrorHandler, NgModule } from '@angular/core';
import { IonicApp, IonicErrorHandler, IonicModule } from 'ionic-angular';
import { SplashScreen } from '@ionic-native/splash-screen';
import { StatusBar } from '@ionic-native/status-bar';
import { HttpModule } from '@angular/http';

import { MyApp } from './app.component';
import { HomePage } from '../pages/home/home';

@NgModule({
  declarations: [
    MyApp,
    HomePage	
  ],
  imports: [
    BrowserModule,
    HttpModule,
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
    {provide: ErrorHandler, useClass: IonicErrorHandler}
  ]
})
export class AppModule {}
```

#### (3.2) home.ts
```javascript
import { Component } from '@angular/core';
import { NavController } from 'ionic-angular';
import { Http, URLSearchParams, RequestOptions, Headers } from '@angular/Http';
import { AlertController } from 'ionic-angular';

@Component({
    selector: 'page-home',
    templateUrl: 'home.html'
})
export class HomePage {
    userName:string;
    password:string;
    returnCode:any;

    //----------------------------------------------------------------	
    constructor(public navCtrl: NavController, public http:Http, public alertCtrl: AlertController) {}


    //----------------------------------------------------------------
    sendData(){
        // 傳給主機的參數
        let headers = new Headers({ 'Content-Type': 'application/json' });
        let options = new RequestOptions({ headers: headers });
        let data={'userName':this.userName, 'password':this.password};

        this.http.post('http://140.131.115.72/sendDataTest', data, options)			
            .subscribe(
                (data) => {
                    this.returnCode=data.json();
                    this.showConfirm();
                    this.userName="";
                    this.password="";
                },
                (err) => {this.showAlert();}
            );	
    }


    //----------------------------------------------------------------
    showAlert() {
        let alert = this.alertCtrl.create({
            title: '資料取得失敗!',
            subTitle: '請確定網路狀態, 或是主機是否提供服務中.',
            buttons: ['OK']
        });
        alert.present();
    }

    //----------------------------------------------------------------
    showConfirm() {
        let alert = this.alertCtrl.create({
            title: '資料傳送成功!',
            subTitle: '已將資料傳送給主機.',
            buttons: ['OK']
        });
        alert.present();
    }	
}
```

#### (3.3) home.html
```html
<ion-header>
    <ion-navbar>
        <ion-title>
            Ionic Blank
        </ion-title>
    </ion-navbar>
</ion-header>

<ion-content padding>
    <!-- ............................. -->
    <ion-list>
        <ion-item>
            <ion-label floating>帳號</ion-label>
            <ion-input type="text" [(ngModel)]="userName"></ion-input>
        </ion-item>

        <ion-item>
            <ion-label floating>密碼</ion-label>
            <ion-input type="password" [(ngModel)]="password"></ion-input>
        </ion-item>

        <button ion-button block style="margin-top:50px" (click)="sendData()">登入</button>		
    </ion-list>
    <!-- ............................. -->
</ion-content>
```

#### (4)本測試使用內容如下:
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

