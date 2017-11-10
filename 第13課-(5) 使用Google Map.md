# 第13課-(5) 使用Google Map


#### 參考來源 https://www.christianengvall.se/google-map-marker-infowindow/


#### 執行圖示
![GitHub Logo](/images/fig13-05.jpg)


## 測試內容如下:
```
 d:\
  |___ <myApp>           
          |___ <src>
                 | 
                 |___ index.html 
                 |                  
                 |___ <pages>   
                         |___ <home> 
                                |___ home.html 
                                |___ home.ts   
                                |___ home.scss                                
```


### (1)建立一個App, 名稱為 myApp:
```
ionic start myApp blank --v2
```


### (2.1) index.html (增加引用 Google Map API)
```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
<head>
  <meta charset="UTF-8">
  <title>Ionic App</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <meta name="format-detection" content="telephone=no">
  <meta name="msapplication-tap-highlight" content="no">

  <link rel="icon" type="image/x-icon" href="assets/icon/favicon.ico">
  <link rel="manifest" href="manifest.json">
  <meta name="theme-color" content="#4e8ef7">

  <!-- ~~~~~~~~~~~~~~~~~~~~~~ -->
  <!-- 增加引用 Google Map API -->
  <!-- ~~~~~~~~~~~~~~~~~~~~~~ -->  
  <script src="http://maps.google.com/maps/api/js"></script>

  <!-- cordova.js required for cordova apps -->
  <script src="cordova.js"></script>

  <link href="build/main.css" rel="stylesheet">

</head>
<body>
  <!-- Ionic's root component and where the app will load -->
  <ion-app></ion-app>

  <!-- The polyfills js is generated during the build process -->
  <script src="build/polyfills.js"></script>

  <!-- ~~~~~~~~~~~~~~~~~~~~~~ -->
  <!-- 增加引用                -->
  <!-- ~~~~~~~~~~~~~~~~~~~~~~ -->  
  <script src="build/vendor.js"></script>

  <!-- The bundle js is generated during the build process -->
  <script src="build/main.js"></script>

</body>
</html>
```



### (2.2) home.html
```html
<ion-header>
  <ion-navbar>
    <ion-title>
      Ionic Blank
    </ion-title>
  </ion-navbar>
</ion-header>

<ion-content>
  <!-- ........................... -->
  <ion-content>
    <div #mapContainer id="map"></div>
  </ion-content>
  <!-- ........................... --> 
</ion-content>
```



### (2.3) home.ts
```javascript
import { Component, ViewChild, ElementRef } from '@angular/core';
import { NavController } from 'ionic-angular';
import { ToastController } from 'ionic-angular';

declare var google;

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {
  //---------------------------------------------------
  @ViewChild('mapContainer') mapContainer: ElementRef;
  map: any;

  //假設目前位置
  currentPosition={
      'lat':25.0424,
      'lng':121.524
  }


  //圖釘位置及名稱
  centers:any=[
    {
      'id':'001',
      'lat':25.042375,
      'lng':121.525383,
      'name':'北商大'
    },
    {
      'id':'002',      
      'lat':25.043081,
      'lng':121.523756,
      'name':'成功高中'
    }
  ];


  //--------------------------------------------------- 
  // 建構元
  //--------------------------------------------------- 
  constructor(public navCtrl: NavController, public toastCtrl: ToastController) {}
  
  
  //---------------------------------------------------  
  // 畫面完成後執行
  //---------------------------------------------------  
  ionViewWillEnter() {
    this.displayGoogleMap();
    this.addMarkersToMap();
  }


  //--------------------------------------------------- 
  // 顯示Google地圖(以目前位置為中心)
  //---------------------------------------------------   
  displayGoogleMap() {
    let latLng = new google.maps.LatLng(this.currentPosition.lat, this.currentPosition.lng);

    let mapOptions = {
      center: latLng,
      disableDefaultUI: true,
      zoom: 17,
      mapTypeId: google.maps.MapTypeId.ROADMAP
    }

    this.map = new google.maps.Map(this.mapContainer.nativeElement, mapOptions);
  }


  //--------------------------------------------------- 
  // 加入圖釘
  //---------------------------------------------------  
  addMarkersToMap() {
    for(var center of this.centers){
      var position = new google.maps.LatLng(center.lat, center.lng);
      var myMarker = new google.maps.Marker({id:center.id, position:position, title:center.name});
    
      myMarker.setMap(this.map);
      this.addInfoWindowToMarker(myMarker);  
    }  
  }

 //--------------------------------------------------- 
  // 加入訊息視窗
  //---------------------------------------------------  
  addInfoWindowToMarker(marker) {
    var infoWindowContent = 
        '<div id="content" style="background:#ffaaaa;"><h2 id="firstHeading" class="firstHeading" style=";padding-left:4px;padding-right:4px;padding-top:6px">' + 
        marker.title + 
        '</h2>' +         
        '<button id="myBtn" style="height:30px;background:#550000;color:#fff;width:100%;">前往</button></div>';

    var infoWindow = new google.maps.InfoWindow({
      content: infoWindowContent
    });

    marker.addListener('click', () => {
      infoWindow.open(this.map, marker);
    });
    
    google.maps.event.addListenerOnce(infoWindow, 'domready', () => {
        //如果myBtn被點擊, 將marker.it及marker.title作為參數傳給自定函數
        document.getElementById('myBtn').addEventListener('click', () => {                    
            this.presentToast(marker.id, marker.title);
        });
    }); 
  }

  //---------------------------------------------------  
  // 自訂函數, 用來接收使用者點擊的infoWindow輸入
  //---------------------------------------------------  
  presentToast(id, title) {
    let msg="參數:" + id + "," + title;

    let toast = this.toastCtrl.create({
      message: msg,
      duration: 3000
    });
    toast.present();
  }
  //---------------------------------------------------    
    
}
```



### (2.4) home.scss
```javascript
page-home {
  .scroll-content {
    height: 100%
  }

  #map {
    width: 100%;
    height: 100%;
  }
}
```

