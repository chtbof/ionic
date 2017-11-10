# 第10課-(6) GET-寫入-DB-有參數-美食


#### 如在Chrome中執行模擬器, 先安裝Chrome Extension: Allow-Control-Allow_Origin



#### 執行圖示
![GitHub Logo](/images/fig10-06.jpg)



## 網站部份


#### (1) 準備資料庫, 名稱:foodDB
```
下載\foods.sql
```


#### (2) 建立網站
```
express myWeb -ejs
```


#### (3) 增加外掛
```
npm install mysql --save
npm install cors --save
```


#### (4)下載樣板網站, 解壓縮後複製到<myWeb>資料夾中(取代舊檔案):
```
下載\myWeb-10-6.zip
```


#### (5)網站測試使用內容如下:
```
 d:\
  |___ <myWeb> 
          |___ app.js  
          |
          |___ <routes>
          |       |___ <lib> 
          |       |       |___ db.js     
          |       |
          |       |___ index.js
          |       |___ writeFood.js
          |       
          |___ <public>
                  |___ <images>
                          |___ 使用的圖片                
```



## App部份

#### (1)建立一個App, 名稱為 myApp:
```
ionic start myApp blank --v2
```

#### (2)下載樣板程式, 解壓縮後複製到<myApp>資料夾中(取代舊檔案):
```
下載\ionic-測試-10-6.zip
```


#### (3.1) app.module.ts (增加匯入 HttpModule)
```javascript
import { BrowserModule } from '@angular/platform-browser';
import { ErrorHandler, NgModule } from '@angular/core';
import { IonicApp, IonicErrorHandler, IonicModule } from 'ionic-angular';
import { SplashScreen } from '@ionic-native/splash-screen';
import { StatusBar } from '@ionic-native/status-bar';
import { HttpModule } from '@angular/http';  //**增加

import { MyApp } from './app.component';
import { HomePage } from '../pages/home/home';

@NgModule({
  declarations: [
    MyApp,
    HomePage
  ],
  imports: [
    BrowserModule,
    HttpModule,  //**增加
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
import { Http, URLSearchParams } from '@angular/http';
import { AlertController } from 'ionic-angular';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
    //----------------------------------
    // 成員     
    //----------------------------------    
    name:string;
    city:string;
    author:string;

    //----------------------------------
    // 建構元    
    //----------------------------------    
    constructor(public navCtrl: NavController, public http:Http, public alertCtrl: AlertController) {}


    //----------------------------------
    // 寫入資料
    //----------------------------------            
    writeData(){
        // 如果未輸入資料
        if(this.name=="" || this.city=="" || this.author==""){
            this.showNoEntry();
            return;
        }

        // 傳給主機的參數
        let params: URLSearchParams = new URLSearchParams();
        params.set('name', this.name);
        params.set('city', this.city);
        params.set('author', this.author);                

        this.http.get('http://192.168.56.1/writeFood', {search: params})			
            .subscribe(
                (data) => {
                    // 接收主機回傳代碼
                    let rtn=data.json();

                    if(rtn.code==0){
                        // 如果寫入成功       
                        this.showSuccess(); 
                        this.name="";
                        this.city="";
                        this.author="";                       
                    }else{
                        // 如果寫入失敗                     
                        this.showFail();
                    }
                },
                (err) => {this.showAlert();}
            );	
    }


    //----------------------------------
    // 顯示讀取失敗訊息
    //----------------------------------
    showNoEntry() {
        let alert = this.alertCtrl.create({
            title: '請輸入資料!',
            subTitle: '請先輸入資料再上傳.',
            buttons: ['OK']
        });
        alert.present();
    }
    //----------------------------------


    //----------------------------------
    // 顯示讀取失敗訊息
    //----------------------------------
    showAlert() {
        let alert = this.alertCtrl.create({
            title: '資料寫入失敗!',
            subTitle: '請確定網路狀態, 或是主機是否提供服務中.',
            buttons: ['OK']
        });
        alert.present();
    }
    //----------------------------------


    //----------------------------------
    // 顯示寫入成功訊息
    //----------------------------------
    showSuccess() {
        let alert = this.alertCtrl.create({
            title: '寫入成功!',
            subTitle: '資料寫入成功.',
            buttons: ['OK']
        });
        alert.present();
    }


    //----------------------------------
    // 顯示寫入失敗訊息
    //----------------------------------
    showFail() {
        let alert = this.alertCtrl.create({
            title: '寫入失敗!',
            subTitle: '資料寫入失敗.',
            buttons: ['OK']
        });
        alert.present();
    }    
    //----------------------------------    
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

<ion-content>
  <!-- .................................. -->
  <ion-list padding-left padding-right>
    <ion-item>
      <ion-label floating>美食名稱</ion-label>
      <ion-input type="text" [(ngModel)]="name"></ion-input>
    </ion-item>

    <ion-item>
      <ion-label floating>縣市</ion-label>
      <ion-input type="text" [(ngModel)]="city"></ion-input>
    </ion-item> 

    <ion-item>
      <ion-label floating>作者</ion-label>
      <ion-input type="text" [(ngModel)]="author"></ion-input>
    </ion-item>        
  </ion-list>

  <ion-list padding-left padding-right>
    <button ion-button (click)="writeData()">上傳資料</button>
  </ion-list>
  <!-- .................................. -->	
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

