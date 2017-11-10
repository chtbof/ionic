# 第18課-(1) 使用Storage


#### 執行圖示
![GitHub Logo](/images/fig18-01.jpg)



#### (1)建立一個App, 名稱為 myApp:
```
ionic start myApp blank --v2
```


#### (2)增加一個頁面, 名稱為detail
```
ionic g page detail
```


#### (3.1) 修改app.module.ts
```javascript
import { BrowserModule } from '@angular/platform-browser';
import { ErrorHandler, NgModule } from '@angular/core';
import { IonicApp, IonicErrorHandler, IonicModule } from 'ionic-angular';
import { SplashScreen } from '@ionic-native/splash-screen';
import { StatusBar } from '@ionic-native/status-bar';

import { MyApp } from './app.component';
import { HomePage } from '../pages/home/home';

//---------------------
// 增加
//---------------------
import { IonicStorageModule } from '@ionic/storage';

@NgModule({
  declarations: [
    MyApp,
    HomePage
  ],
  imports: [
    BrowserModule,
    IonicModule.forRoot(MyApp),
    IonicStorageModule.forRoot()  /*增加*/
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


#### (4.1) 修改home.html
```html
<ion-header>
  <ion-navbar>
    <ion-title>
      主頁面
    </ion-title>
  </ion-navbar>
</ion-header>

<ion-content padding>
  寫入 Storage 變數.

  <ion-list>
    <ion-item>
      <ion-label>stuNo</ion-label>
      <ion-input type="text" [(ngModel)]="stuNo"></ion-input>
    </ion-item>

    <ion-item>
      <ion-label>name</ion-label>
      <ion-input type="text" [(ngModel)]="name"></ion-input>
    </ion-item>
  </ion-list>

  <p>
    <button ion-button (click)='writeStorage()'>寫入Storage</button>
  </p>

  <p>
    <button ion-button (click)='goNext()'>跳至次頁</button>
  </p>
</ion-content>
```



#### (4.2) 修改home.ts
```javascript
import { Component } from '@angular/core';
import { NavController } from 'ionic-angular';

//---------------------
// 增加引用
//---------------------
import { Storage } from '@ionic/storage';
import { ToastController } from 'ionic-angular';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
  //---------------------
  // 成員
  //---------------------  
  stuNo:string;
  name:string;

  //---------------------
  // 建構元
  //---------------------
  constructor(public navCtrl: NavController, private storage: Storage, private toastCtrl: ToastController) {
  }

  //---------------------
  // 寫入Storage的函式
  //---------------------
  writeStorage(){
      this.storage.set('stuNo', this.stuNo);
      this.storage.set('name', this.name);

      let toast = this.toastCtrl.create({
        message: '已寫入Storage',
        duration: 3000,
        position: 'bottom'
      });

      toast.present();
  }

  //---------------------
  // 跳至次頁的函式
  //---------------------
  goNext(){
    this.navCtrl.push('Detail');
  }

}
```


#### (5.2) 修改detail.html
```html
<ion-header>

  <ion-navbar>
    <ion-title>次頁面</ion-title>
  </ion-navbar>

</ion-header>


<ion-content padding>
  取出storage變數
  <p>
    stuNo: {{stuNo}}
  </p>
  
  <p>
    name: {{name}}
  </p>  
</ion-content>
```

#### (5.2) 修改detail.ts
```javascript
import { Component } from '@angular/core';
import { IonicPage, NavController, NavParams } from 'ionic-angular';

//---------------------
// 增加引用
//---------------------
import { Storage } from '@ionic/storage';

@IonicPage()
@Component({
  selector: 'page-detail',
  templateUrl: 'detail.html',
})
export class Detail {
  //---------------------
  // 成員
  //--------------------- 
  stuNo:string;
  name:string;

  //---------------------
  // 建構元
  //---------------------  
  constructor(public navCtrl: NavController, public navParams: NavParams, private storage: Storage) {
	// 取出Storage內容  
	storage.get('stuNo').then((val) => {
		this.stuNo=val;
	});
	
	storage.get('name').then((val) => {
		this.name=val;
	});	
  }
}
```


#### 本測試使用內容如下:
```
 d:\
  |___ <myApp> 
          |___ <app>      
          |      |___ app.module.ts
          |       
          |___ <src>      
                 |___ <pages>   
                         |___ <home> 
                         |      |___ home.html 
                         |      |___ home.ts   
                         |       
                         |___ <detail> 
                                |___ detail.html 
                                |___ detail.ts                                
                            
```

