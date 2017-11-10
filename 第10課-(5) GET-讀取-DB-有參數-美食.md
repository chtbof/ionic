# 第10課-(5) GET-讀取-DB-有參數-美食


#### 如在Chrome中執行模擬器, 先安裝Chrome Extension: Allow-Control-Allow_Origin



#### 執行圖示
![GitHub Logo](/images/fig10-05.jpg)


## 資料庫及網站部份
```
參考 第10課-(2) DB-有參數-JSON-美食
```


## App部份

#### (1)建立一個App, 名稱為 myApp:
```
ionic start myApp blank --v2
```

#### (2)下載樣板程式, 解壓縮後複製到<myApp>資料夾中(取代舊檔案):
```
下載\ionic-測試-10-5.zip
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
    items:any;
    city:string="台北";

    //----------------------------------
    // 建構元    
    //----------------------------------    
    constructor(public navCtrl: NavController, public http:Http, public alertCtrl: AlertController) {
        this.loadData(this.city);	
    }


    //----------------------------------
    // 讀取主機資料
    //----------------------------------            
    loadData(city){
        // 傳給主機的參數
        let params: URLSearchParams = new URLSearchParams();
        params.set('city', city);

        this.http.get('http://192.168.56.1', {search: params})			
            .subscribe(
                (data) => {
                    this.items=data.json();

                    if(this.items.length==0){
                        this.showNotFound();
                        return;
                    }
                },
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


    //----------------------------------
    // 顯示無符合資料訊息
    //----------------------------------
    showNotFound() {
        let alert = this.alertCtrl.create({
            title: '無符合資料!',
            subTitle: '目前資料庫無符合條件的資料.',
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
  <div padding>
    <ion-segment>
      <ion-segment-button (click)="loadData('台北')">
        台北
      </ion-segment-button>
      <ion-segment-button (click)="loadData('台南')">
        台南
      </ion-segment-button>
      <ion-segment-button (click)="loadData('彰化')">
        彰化
      </ion-segment-button>
      <ion-segment-button (click)="loadData('宜蘭')">
        宜蘭
      </ion-segment-button>                      
    </ion-segment>
  </div>
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

