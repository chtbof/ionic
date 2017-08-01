# 第17課-(6) 使用Firebase推播 
# Express --> Firebase --> 手機(訂閱主題)


## 測試方式

```
先啟動Express主機(不可用虛擬IP):
npm start

再使用瀏覽器連結Express主機:
http://主機位址
```


## Express 架構部份

#### (1)建立一個Express網站, 名稱為 myWeb:
```
express myWeb -ejs
cd myWeb
npm install
```


#### (2)增加以下外掛
```
npm install fcm-push --save
```


## Express 程式部份

#### 網站路徑如下:
```
 d:\
  |___ <myWeb>             
          |___ app.js
          |
          |___ <routes>
          |      |___ notify.js
          |
          |___ <views>
                 |___ index.ejs
                 |___ pushSuccess.ejs    
                 |___ pushFail.ejs                    
                 
```



#### (1)app.js

```javascript
var express = require('express');
var path = require('path');
var favicon = require('serve-favicon');
var logger = require('morgan');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');

var index = require('./routes/index');
var users = require('./routes/users');

//------------------------------------------------------------------
// 增加以下的require
//------------------------------------------------------------------
var notify = require('./routes/notify');
//------------------------------------------------------------------

var app = express();

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'ejs');

// uncomment after placing your favicon in /public
//app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

app.use('/', index);
app.use('/users', users);

//-------------------------------------------------------
// 增加以下的app.use()
//-------------------------------------------------------
app.use('/notify', notify);
//-------------------------------------------------------

// catch 404 and forward to error handler
app.use(function(req, res, next) {
  var err = new Error('Not Found');
  err.status = 404;
  next(err);
});

// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.render('error');
});

module.exports = app;
```



#### (2)notify.js

##### 伺服器金鑰
![GitHub Logo](/images/fig17-4-01.jpg)


##### 程式部分
```javascript
var express = require('express');
var router = express.Router();

//---------------------------------------
// 在command模式中加入外掛, 如下:
// npm install fcm-push --save
//---------------------------------------
var FCM = require('fcm-push');

//---------------------------------------------------------------------------------
// 在 https://console.firebase.google.com/ 中,
// 已建立的Firebase專案中 -> 選 CLOUD MESSAGING -> 內有 "伺服器金鑰"
//---------------------------------------------------------------------------------
var serverKey = 'AAAANR_Y_FQ .... 自己的伺服器金鑰 ....bULPVc9EGOx97pQvb0';
var fcm = new FCM(serverKey);

//--------------------------------------------------------
// 等待post請求
//--------------------------------------------------------
router.post('/', function(req, res, next) {
    // 取得訂閱主題及推播內容
    var topic=req.body.topic;
    var content=req.body.content;	
	
    //=========================================================
    // 執行某些處理傳入資料的作業
    //=========================================================
    //   .
    //   .
    //   .	
	
	
    //=========================================================
    // 傳送通知給訂閱某個主題的連接設備
    //=========================================================
    var message = {
        to: '/topics/' + topic, 
        collapse_key: '001', 
        notification: {
            title: '我的通知',
            body: content
        }
    };	

    fcm.send(message, function(err, response){
        //===========================
        // 如果推播失敗
        //===========================
        if (err) {								
            res.render('pushFail');	
        }

        //===========================
        // 如果推播成功
        //===========================			
        if(response){
            res.render('pushSuccess');	
        }
    });
});

module.exports = router;
```


#### (3)index.ejs

```html
<!DOCTYPE html>
<html>
    <head>
        <title>推播測試</title>
        <link rel='stylesheet' href='/stylesheets/style.css' />
    </head>
    
    <body>
        <h1>推播測試</h1>
        <form  method="post" action="/notify">		
            <p>
                主題<br>
                <input type="text" name="topic" value="marketing"><br>
            </p>
		
            <p>
                內容<br>
                <input type="text" name="content" value="你好">
            </p>
		
            <p>
	        <input type="submit" value="送出">
            </p>	
        </form>
    </body>
</html>
```


#### (4)pushSuccess.ejs

```html
<!DOCTYPE html>
<html>
    <head>
        <title>推播結果</title>
        <link rel='stylesheet' href='/stylesheets/style.css' />
    </head>
    <body>
        <h1>訊息已傳送</h1>    
    </body>
</html>
```



#### (5)pushFail.ejs

```html
<!DOCTYPE html>
<html>
    <head>
        <title>推播結果</title>
        <link rel='stylesheet' href='/stylesheets/style.css' />
    </head>
    <body>
        <h1>訊息傳送失敗</h1>    
    </body>
</html>
```


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



#### (4) 修改config.xml, 其中 com.abc.myapplication 應與第17課-(1)的步驟(5)設定的套件名稱相同.
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
import { ToastController } from 'ionic-angular';

@Component({
    selector: 'page-home',
    templateUrl: 'home.html'
})
export class HomePage {
    //------------------------------
    // 建構元
    //------------------------------
    constructor(public navCtrl: NavController, public fcm: FCM, private toastCtrl: ToastController) {   
        // 訂閱marketing主題的推播
        fcm.subscribeToTopic('marketing')
            .then(()=>{this.presentRegisterSuccess();})
            .catch((err)=>{this.presentRegisterError()}) 
    }


    //------------------------------
    // 顯示訂閱成功
    //------------------------------
    presentRegisterSuccess() {
        let toast = this.toastCtrl.create({
            message: '訂閱成功',
            duration: 2000,
            position: 'bottom'
        });

        toast.present();
    }   


    //------------------------------
    // 顯示訂閱失敗
    //------------------------------
    presentRegisterError() {
        let toast = this.toastCtrl.create({
            message: '訂閱失敗',
            duration: 2000,
            position: 'bottom'
        });

        toast.present();
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

