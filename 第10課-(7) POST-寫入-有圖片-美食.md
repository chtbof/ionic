# 第10課-(7) POST-寫入-有圖片-美食


## 有圖片及文字欄位, 上傳寫入資料庫


#### 如在Chrome中執行模擬器, 先安裝Chrome Extension: Allow-Control-Allow_Origin


#### 執行圖示
![GitHub Logo](/images/fig10-07.jpg)



## 資料庫部份

#### (1)建立一個MySQL的foodDB資料庫, 資料表可下載:
```
下載\foods.sql
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
npm install fs --save
npm install cors --save
npm install multer --save
npm install easyimage --save
```

#### (3)安裝imageMagick:
```
下載/ImageMagick-6.7.3-4-Q16-windows-dll.exe
```


#### (4)下載樣板網站, 解壓縮後複製到<myWeb>資料夾中(取代舊檔案):
```
下載\myWeb-10-7.zip
```

#### (5)網站簡易架構
```
 d:\
  |___ <myWeb>           
          |___ <public>
                 |___ app.js
                 |
                 |___ <images> 
                 |       |___ (圖片上傳後的儲存位置)
                 |                 
                 |___ <routes>   
                         |___ <lib>
                         |      |___ db.js (資料庫連線設定)
                         |                          
                         |___ index.js 
                         |___ writeFood.js (寫入資料庫)                          
```





## App部份

#### (1)建立一個App, 名稱為 myApp:
```
ionic start myApp blank --v2
```


#### (2)安裝angular2-image-upload外掛
```
npm install angular2-image-upload --save
```


#### (3.1) app.module.ts (增加匯入 HttpModule及ImageUploadModule)
```javascript
import { BrowserModule } from '@angular/platform-browser';
import { ErrorHandler, NgModule } from '@angular/core';
import { IonicApp, IonicErrorHandler, IonicModule } from 'ionic-angular';
import { SplashScreen } from '@ionic-native/splash-screen';
import { StatusBar } from '@ionic-native/status-bar';

import { MyApp } from './app.component';
import { HomePage } from '../pages/home/home';
import { HttpModule } from '@angular/http';                /* 增加 */
import { ImageUploadModule } from 'angular2-image-upload'; /* 增加 */

@NgModule({
  declarations: [
    MyApp,
    HomePage
  ],
  imports: [
    BrowserModule,
    HttpModule,                  /* 增加 */
    ImageUploadModule.forRoot(), /* 增加 */
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
//----------------------------------------------------
import { Http } from '@angular/http';
import { AlertController } from 'ionic-angular';
//----------------------------------------------------

@Component({
    selector: 'page-home',
    templateUrl: 'home.html'
})
export class HomePage {
    //----------------------------------
    // 成員
    //----------------------------------    
    name:string="";
    city:string="";
    author:string=""; 
    selectedImageFiles:any;

    //----------------------------------
    // 建構元
    //----------------------------------
    constructor(public navCtrl: NavController, 
        public http: Http,
        public alertCtrl: AlertController) {
        this.selectedImageFiles=[];
    }

    //----------------------------------
    // 上傳圖片
    //----------------------------------
    upload() {
        // 如果未輸入資料
        if(this.name=="" || this.city=="" || this.author==""){
            this.showNoEntry();
            return;
        }      

        if (this.selectedImageFiles[0]) {
            // 傳給主機的參數	
            let input = new FormData();

            // 在參數中加入圖片
            for(var i=0; i<this.selectedImageFiles.length; i++){
                input.append("pictures", this.selectedImageFiles[i]);
            }		      

            // 在參數中加入文字
            input.append('name', this.name);
            input.append('city', this.city);
            input.append('author', this.author);
     
            //***改成自己的主機位址***
            this.http.post("http://192.168.56.1/writeFood", input)
                .subscribe(
                    (data) => {
                        //接收主機回傳代碼
                        let rtn=data.json();
                        
                        if(rtn.code==0){
                            //如果寫入成功
                            this.showWriteSuccess();
                            this.name="";
                            this.city="";
                            this.author="";                                 
                        }else if(rtn.code==-1){
                            //如果寫入失敗
                            this.showWriteFail();
                        }                                   
                    },
                    (err) => {
                        //如果連結主機失敗
                        this.showConnectionFail();
                    }
                );	
        }else{
           this.showNoPhoto(); 
        }
    }

    //----------------------------------
    // 加入圖片
    //----------------------------------
    imageUploaded(event){
        this.selectedImageFiles.push(event.file);
    }
	
    //----------------------------------
    // 移除圖片
    //----------------------------------
    imageRemoved(event){
        let index = this.selectedImageFiles.indexOf(event.file);		
        if( index > -1) {
            this.selectedImageFiles.splice(index, 1);
        }		
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
    // 連線失敗
    //----------------------------------
    showConnectionFail() {
        let alert = this.alertCtrl.create({
            title: '連線失敗!',
            subTitle: '請確定網路狀態, 或是主機是否提供服務中.',
            buttons: ['OK']
        });
        alert.present();
    }

    //----------------------------------
    // 尚未選擇照片
    //----------------------------------
    showNoPhoto() {
        let alert = this.alertCtrl.create({
            title: '尚未選擇照片!',
            subTitle: '請先提供一張照片再繼續註冊.',
            buttons: ['OK']
        });
        alert.present();
    }    

    //----------------------------------
    // 寫入失敗
    //----------------------------------
    showWriteFail() {
        let alert = this.alertCtrl.create({
            title: '寫入失敗!',
            subTitle: '資料寫入失敗, 請檢查圖片是否超過2M.',
            buttons: ['OK']
        });
        alert.present();
    }

    //----------------------------------
    // 寫入成功
    //----------------------------------
    showWriteSuccess() {
        let alert = this.alertCtrl.create({
            title: '寫入成功!',
            subTitle: '已成功寫入.',
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

<ion-content padding>
    <!-- ............................. -->
    <ion-list>
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
    <!-- ............................. -->

    <image-upload  
        id="myImageUpload"
        [max]="1"
        [buttonCaption]="'選圖片(1張)'"
        [dropBoxMessage]="''"    
        (onFileUploadFinish)="imageUploaded($event)"
        (onRemove)="imageRemoved($event)"
    ></image-upload>

    <button ion-button (click)="upload()">上傳</button>
    <!-- ............................. -->    
</ion-content>

```

#### (4)手機簡易架構:
```
 d:\
  |___ <myApp> 
          |___ <node_modules>
          |         |___ <angular2-image-upload>  
          |                    |___ <lib>                           
          |                            |___ <image-upload>  
          |                                       |___ image-upload.components.js (可改上傳圖片外掛顯示樣式)          
          |                           
          |___ <src>
                 |___ <app> 
                 |       |___ app.module.ts (增加引用HttpModule及ImageUploadModule)
                 |                 
                 |___ <pages>   
                         |___ <home> 
                                |___ home.html 
                                |___ home.ts     
                                |___ home.scss                                 
```

