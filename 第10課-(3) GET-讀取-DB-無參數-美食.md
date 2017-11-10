# 第10課-(3) GET-讀取-DB-無參數-美食


#### 如在Chrome中執行模擬器, 先安裝Chrome Extension: Allow-Control-Allow_Origin



#### 執行圖示
![GitHub Logo](/images/fig10-03.jpg)


## 資料庫及網站部份
```
參考 第10課-(1) DB-無參數-JSON-美食
```


## App部份

#### (1)建立一個App, 名稱為 myApp:
```
ionic start myApp blank --v2
```

#### (2)下載樣板程式, 解壓縮後複製到<myApp>資料夾中(取代舊檔案):
```
下載\ionic-測試-10-3.zip
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
import { Http } from '@angular/http';
import { AlertController } from 'ionic-angular';

@Component({
    selector: 'page-home',
    templateUrl: 'home.html'
})
export class HomePage {
    //----------------------------------
    // 成員    
    //----------------------------------    
    items:any;


    //----------------------------------
    // 建構元    
    //----------------------------------    
    constructor(public navCtrl: NavController, public http:Http, public alertCtrl: AlertController) {
        this.loadData();	
    }


    //----------------------------------
    // 讀取主機資料
    //----------------------------------            
    loadData(){
        //**網址請自行修改    
        this.http.get('http://192.168.56.1', {})			
            .subscribe(
                (data) => {this.items=data.json();},
                (err) => {this.showAlert();}
            );	
    }


    //----------------------------------
    // 顯示讀取失敗訊息
    //----------------------------------
    showAlert() {
        let alert = this.alertCtrl.create({
            title: '資料取得失敗!',
            subTitle: '請確定網路狀態, 或是主機是否提供服務中.',
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
  <ion-list>
    <ion-item *ngFor="let item of items">
      <ion-thumbnail item-left>
        <img src="{{item.thumbnail}}">
      </ion-thumbnail>
      <h2>{{item.name}}</h2>
      <p>{{item.author}}</p>		
    </ion-item>
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

