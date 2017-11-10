# 第11課-(1) POST-寫入資料庫-帳密


#### 如在Chrome中執行模擬器, 先安裝Chrome Extension: Allow-Control-Allow_Origin



#### 執行圖示
![GitHub Logo](/images/fig11-01.jpg)


## 資料庫, 名稱: userdb
```
下載/userdb.sql
```



## 網站部份

#### (1)建立Express網站, 名稱為 myWeb:
```
express myWeb -ejs
cd myWeb
npm install
```


#### (2)追加外掛:
```
npm install mysql --save
npm install cors --save
```


#### (3)下載樣板網站, 解壓縮後複製到<myWeb>資料夾中(取代舊檔案):
```
下載\myWeb-註冊-資料庫.zip
```

#### (4)index.js
```
var express = require('express');
var router = express.Router();
var mysql = require('mysql');
//------------------
// 載入資料庫連結
//------------------
var pool = require('./lib/db.js');

//------------------
// 回應POST請求
//------------------
router.post('/', function(req, res, next) {
    //接收傳來的參數
    var username=req.body.username;
    var password=req.body.password;
	
    //產生一個物件
    var newData={
        username:username,
        password:password
    }

    //寫入資料庫
    pool.query('INSERT INTO user SET ?', newData, function(err, rows, fields) {
        if (err){
            rtn={'code':-1};
            res.json(rtn);     //回傳失敗代號
        }else{
            rtn={'code':0};
            res.json(rtn);     //回傳成功代號
        }
    });
});

module.exports = router;
```


## App部份

#### (1)建立一個App, 名稱為 myApp:
```
ionic start myApp blank --v2
```

#### (2)下載樣板程式, 解壓縮後複製到<myApp>資料夾中(取代舊檔案):
```
下載\ionic-測試-11-1.zip
```


#### (3.1) app.module.ts (增加匯入 HttpModule)
```javascript
import { BrowserModule } from '@angular/platform-browser';
import { ErrorHandler, NgModule } from '@angular/core';
import { IonicApp, IonicErrorHandler, IonicModule } from 'ionic-angular';
import { SplashScreen } from '@ionic-native/splash-screen';
import { StatusBar } from '@ionic-native/status-bar';
import { HttpModule } from '@angular/Http';

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

    //..........................................
    constructor(public navCtrl: NavController, public http:Http, public alertCtrl: AlertController) {}


    //..........................................
    register(){
        // 改為自己的主機位址
        let url='105stu.ntub.edu.tw';
	
        // 傳給主機的參數
        let headers = new Headers({ 'Content-Type': 'application/json' });
        let options = new RequestOptions({ headers: headers });
        let data={'username':this.userName, 'password':this.password};
   
        // 向主機發出POST請求
        this.http.post(url, data, options)			
            .subscribe(
                (data) => {
                    let ret=data.json();     //接收主機回傳代碼

                    if(ret.code==0){         //如果成功註冊
                        this.showSuccess();
                        this.userName='';
                        this.password='';
                        return;
                    }else{                   //如果註冊失敗 
                        this.showFail();
                        return;
                    }
                },
                (err) => {this.showAlert();}
			      );	
    }


    //..........................................
    showAlert() {
        let alert = this.alertCtrl.create({
            title: '連線失敗!',
            subTitle: '請確定網路狀態, 或是主機是否提供服務中.',
            buttons: ['OK']
        });
        alert.present();
    }

    //..........................................
    showSuccess() {
        let alert = this.alertCtrl.create({
            title: '註冊成功!',
            subTitle: '帳號/密碼已成功註冊',
            buttons: ['OK']
        });
        alert.present();
    }	

    //----------------------------------------------------------------
    showFail() {
        let alert = this.alertCtrl.create({
            title: '註冊失敗!',
            subTitle: '帳號/密碼註冊失敗',
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
    <!-- ............................ -->
    <ion-list>
        <ion-item>
            <ion-label floating>帳號</ion-label> 
            <ion-input type="text" [(ngModel)]="userName"></ion-input>
        </ion-item>

        <ion-item>
            <ion-label floating>密碼</ion-label>
            <ion-input type="password" [(ngModel)]="password"></ion-input>
        </ion-item>

        <button ion-button block style="margin-top:50px" (click)="register()">註冊</button>		
    </ion-list>
    <!-- ............................ -->    
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

