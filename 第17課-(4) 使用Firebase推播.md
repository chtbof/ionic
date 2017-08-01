# 第17課-(4) 使用Firebase推播
# Express --> Firebase --> 手機(個人)



## 資料庫部份

#### (1)建立一個myDB資料庫, 內有register及unregister資料表.
```

SET FOREIGN_KEY_CHECKS=0;
-- ----------------------------
-- Table structure for `register`
-- ----------------------------
DROP TABLE IF EXISTS `register`;
CREATE TABLE `register` (
  `serNo` int(11) NOT NULL AUTO_INCREMENT,
  `token` text COLLATE utf8_unicode_ci,
  `timestamp` datetime DEFAULT NULL,
  PRIMARY KEY (`serNo`)
) ENGINE=InnoDB AUTO_INCREMENT=13 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;

-- ----------------------------
-- Records of register
-- ----------------------------

-- ----------------------------
-- Table structure for `unregister`
-- ----------------------------
DROP TABLE IF EXISTS `unregister`;
CREATE TABLE `unregister` (
  `serNo` int(11) NOT NULL AUTO_INCREMENT,
  `token` text COLLATE utf8_unicode_ci,
  `timestamp` datetime DEFAULT NULL,
  PRIMARY KEY (`serNo`)
) ENGINE=InnoDB AUTO_INCREMENT=13 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;

-- ----------------------------
-- Records of unregister
-- ----------------------------

```



## Express部份

#### (1)建立一個Express網站, 名稱為 myWeb:
```
express myWeb -ejs
cd myWeb
npm install
```


#### (2)增加以下外掛
```
npm install fcm-push --save
npm install mysql --save
```


#### (3)db.js
```javascript
var mysql = require('mysql');

//------------------------
// 建立資料庫連線池
//------------------------
var pool  = mysql.createPool({
    user: 'root',
    password: 'mysql',
    host: '127.0.0.1',
    database: 'mydb', 
    waitForConnections : true, 
    connectionLimit : 10       
});

//----------------------------
// 引用此模組時將匯出pool物件
//----------------------------
module.exports = pool;
```



#### (4)index.js

##### 伺服器金鑰
![GitHub Logo](/images/fig17-4-01.jpg)


##### 程式
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
var serverKey = '*****填入自己的伺服器金鑰******';
var fcm = new FCM(serverKey);


//------------------
// 載入資料庫連結
//------------------
var pool = require('./lib/db.js');


//--------------------------------------------------------
// 等待get請求
//--------------------------------------------------------
router.get('/', function(req, res, next) {	
    // 取得工作代碼及內容
    var code=req.param('code');
    var content=req.param('content');
	

    //===============================
    // 工作代碼為1時: 
    // 儲存手機傳來的token
    //===============================
    if(code==1){
        // 取得使用者傳來的參數		
        var token=content;
        var timestamp=new Date();		
		
        // 建立一個新資料物件
        var newData={
            token:token,
            timestamp:timestamp
        }	
		
        // 將Token寫入資料庫
        pool.query('INSERT INTO register SET ?', newData, function(err, rows, fields) {
            if (err){
                console.log('新增失敗');
            }else{
                console.log('新增成功');
            }
        });
		
        // 回傳訊息給連接設備
        data=[{'code':'0'}];	
        res.json(data);
    }
		
	
    //=========================================================
    // 工作代碼為2時: 	
    // 傳送通知給所有的連接設備
    // 傳送範例 http://主機位址?code=2;content=你好
    //=========================================================
    if(code==2){
        // 取得使用者傳來的參數	
        var msg=content;		
		
        // 取得資料庫中的Token
        pool.query('SELECT * FROM register', function (error, results, fields) {
            if (error){
                console.log('資料取出失敗');
                    return;
            }else{				
                for (var i=0; i<results.length; i++){						
                    var message = {
                        to: results[i].token, 
                        collapse_key: '001', 
                        notification: {
                            title: '我的通知',
                            body: msg
                        }
                    };	

                    sendNotification(message);
                }       
            }
        })

        res.render('index', { title: '通知已傳送'});
    }
	
	
    //===============================
    // 推播函式
    //===============================	
    function sendNotification(message) {
        var msg=message;

        fcm.send(msg, function(err, response){
            //===========================
            // 如果推播失敗
            //===========================
            if (err) {								
                console.log('--------------------------');
                console.log('通知傳送失敗:'+msg.to);
							
                //建立一個新資料物件
                var timestamp=new Date();				
				
                var newData={
                    token:msg.to,
                    timestamp:timestamp
                }	

                // 寫入無效Token資料表
                pool.query('INSERT INTO unregister SET ?', newData, function(err, rows, fields) {
                    if (err) {
                        throw err;
                    }
                });
            }

            //===========================
            // 如果推播成功
            //===========================			
            if(response){
                console.log('--------------------------');
                console.log('通知傳送成功:'+message.to);
            }
        });
    }
    //===============================	
});

module.exports = router;
```

#### (5)網站路徑如下:
```
 d:\
  |___ <myWeb>   
          |___ <routes>
                 |___ index.js
                 |
                 |___ <lib>             
                        |___ db.js
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
import { Http, URLSearchParams } from '@angular/http';
import { ToastController } from 'ionic-angular';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
    data:any;

    //------------------------------
    // 建構元
    //------------------------------
    constructor(public navCtrl: NavController, public fcm: FCM, public http:Http, private toastCtrl: ToastController) {        
        this.fcm.getToken().then(token=>{
            let toast = this.toastCtrl.create({
              message: '取得Token:'+token,
              duration: 2000,
              position: 'bottom'
            });
            toast.present();

            this.registerToken(token);
        })   
    }



    //------------------------------
    // 向主機註冊自己的token
    //------------------------------
    registerToken(token){
        // 傳給主機的參數
        let params: URLSearchParams = new URLSearchParams();
        params.set('code', '1');
        params.set('content', token);

        // 改為自己的主機位址
        this.http.get('http://???.???.???.???', {search: params})			
            .subscribe(
                (data) => {
                  this.data=data.json();
                  this.presentRegisterSuccess();
                },
                (err) => {this.presentRegisterError();}
            );	
    }  


    //------------------------------
    // 顯示註冊成功
    //------------------------------
    presentRegisterSuccess() {
        let toast = this.toastCtrl.create({
            message: '已向主機註冊',
            duration: 1000,
            position: 'bottom'
        });

        toast.present();
    }   


    //------------------------------
    // 顯示註冊失敗
    //------------------------------
    presentRegisterError() {
        let toast = this.toastCtrl.create({
            message: '主機註冊失敗',
            duration: 1000,
            position: 'bottom'
        });

        toast.present();
    }    
    //------------------------------
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
import { HttpModule } from '@angular/http';

@NgModule({
  declarations: [
    MyApp,
    HomePage
  ],
  imports: [
    BrowserModule,    
    HttpModule, /** 增加 **/
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
