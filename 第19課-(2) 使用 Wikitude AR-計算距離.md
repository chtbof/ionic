# 第19課-(2) 使用 Wikitude AR-計算距離


#### 執行圖示
![GitHub Logo](/images/fig19-02.jpg)



#### 參考第19課-(1), 建立一個應用程式, 假設名稱為 myApp
```
修改 myApp 中的2個程式:


<myApp>myAR.zip解壓
  |
  |___ <src>    
         |___ <assets>  
         |       |___ <09_ObtainPoiData_1_FromApplicationModel>
         |               |___ <js>
         |                      |___ fromapplicationmodel.js
         |
         |___ <pages>   
                 |___ <my-ar> 
                        |___ my-ar.ts
```


#### (1)fromapplicationmodel.js
```
// implementation of AR-Experience (aka "World")
var World = {
	// different POI-Marker assets
	markerDrawable_idle: null,
	markerDrawable_selected: null,
	markerDrawable_directionIndicator: null,

	// list of AR.GeoObjects that are currently shown in the scene / World
	markerList: [],

	// The last selected marker
	currentMarker: null,
    
	currentLat:null,
	currentLon:null, 

	/*
		Have a look at the native code to get a better understanding of how data can be injected to JavaScript.
		Besides loading data from assets it is also possible to load data from a database, or to create it in native code. Use the platform common way to create JSON Objects of your data and use native 'architectView.callJavaScript()' to pass them to the ARchitect World's JavaScript.
		'World.loadPoisFromJsonData' is called directly in native code to pass over the poiData JSON, which then creates the AR experience.
	*/
	//==============================
  // 接收目標點座標, 加入World中
	//==============================
	loadPoisFromJsonData: function loadPoisFromJsonDataFn(poiData) {
		World.markerList = [];

		World.markerDrawable_idle = new AR.ImageResource("assets/marker_idle.png");
		World.markerDrawable_selected = new AR.ImageResource("assets/marker_selected.png");
		World.markerDrawable_directionIndicator = new AR.ImageResource("assets/indi.png");

		// loop through POI-information and create an AR.GeoObject (=Marker) per POI
		for (var currentPlaceNr = 0; currentPlaceNr < poiData.length; currentPlaceNr++) {
	       var lat=parseFloat(poiData[currentPlaceNr].latitude);
			   var lon=parseFloat(poiData[currentPlaceNr].longitude);
			   var dist=World.distance(currentLat, currentLon, lat, lon)+"公里";

	       var singlePoi = {
			       "id": poiData[currentPlaceNr].id,
			       "latitude": lat,
			       "longitude": lon,
			       "altitude": parseFloat(poiData[currentPlaceNr].altitude),
			       "title": poiData[currentPlaceNr].name,
			       "description": dist
			   };	

     		 World.markerList.push(new Marker(singlePoi));
	    }

		  World.updateStatusMessage('目前頁面中有 ' + currentPlaceNr + ' 個座標點.');				    		
	},

	// updates status message shown in small "i"-button aligned bottom center
	updateStatusMessage: function updateStatusMessageFn(message, isWarning) {

		var themeToUse = isWarning ? "e" : "c";
		var iconToUse = isWarning ? "alert" : "info";

		$("#status-message").html(message);
		$("#popupInfoButton").buttonMarkup({
			theme: themeToUse
		});
		$("#popupInfoButton").buttonMarkup({
			icon: iconToUse
		});
	},

	// location updates, fired every time you call architectView.setLocation() in native environment
  //==============================
  // 將目前的座標存在成員中
	//==============================
	locationChanged: function locationChangedFn(lat, lon, alt, acc) {		
		  currentLat=lat;
		  currentLon=lon;	
	},

	// fired when user pressed maker in cam
	onMarkerSelected: function onMarkerSelectedFn(marker) {

		// deselect previous marker
		if (World.currentMarker) {
			if (World.currentMarker.poiData.id == marker.poiData.id) {
				return;
			}
			World.currentMarker.setDeselected(World.currentMarker);
		}

		// highlight current one
		marker.setSelected(marker);
		World.currentMarker = marker;
	},

	// screen was clicked but no geo-object was hit
	onScreenClick: function onScreenClickFn() {
		if (World.currentMarker) {
			World.currentMarker.setDeselected(World.currentMarker);
		}
	},

  //==============================
  // 計算兩點之間的距離
	//==============================
	distance: function distanceFN(lon1, lat1, lon2, lat2) {
      var R = 6371; // 地球半徑(公里)

      var dLat = (lat2-lat1)*Math.PI/180;
      var dLon = (lon2-lon1)*Math.PI/180;

      var a = Math.sin(dLat/2) * Math.sin(dLat/2) +
          Math.cos(lat1*Math.PI/180) * Math.cos(lat2*Math.PI/180) *
          Math.sin(dLon/2) * Math.sin(dLon/2);

      var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
      var d = parseInt(R * c * 1000) / 1000; //兩點間距離(公里)

      return d;
  }	
};

/* forward locationChanges to custom function */
AR.context.onLocationChanged = World.locationChanged;

/* forward clicks in empty area to World */
AR.context.onScreenClick = World.onScreenClick;
```



#### (1)my-ar.ts

```
import { Component } from '@angular/core';
import { IonicPage, NavController, NavParams } from 'ionic-angular';

@IonicPage()
@Component({
  selector: 'page-my-ar',
  templateUrl: 'my-ar.html',
})
export class MyAr {
  constructor(public navCtrl: NavController, public navParams: NavParams) {
  }

  ionViewDidLoad() {
    console.log('ionViewDidLoad MyAr');
  }


  //----------------------------------------------------------------------
  ionViewDidEnter() {
      var startupConfiguration: any = {"camera_position": "back"};

      WikitudePlugin.loadARchitectWorld(
          function(success) {
            console.log("ARchitect World loaded successfully.");

            //---------------------------------------------------------------------------------------------            //
            //呼叫(傳參數)給 [www/assets/09_ObtainPoiData_1_FromApplicationModel/fromapplicationmodel.js]的函式
            //---------------------------------------------------------------------------------------------
            WikitudePlugin.callJavaScript('World.architectView.setLocation()');
            WikitudePlugin.callJavaScript('World.loadPoisFromJsonData([{"id": "1","longitude": "121.520927","latitude": "25.035017","description": "國家音樂廳","altitude": "100.0", "name": "音樂廳"}])');
            //---------------------------------------------------------------------------------------------

          },
          function(fail) {
            console.log("Failed to load ARchitect World!");            
          },
          //------------------------------------------------------------------------
          // 引用AR樣版 (官網有多個不同應用型態樣版可下載, 自行選擇應用)
          //------------------------------------------------------------------------          
          "www/assets/09_ObtainPoiData_1_FromApplicationModel/index.html",  
          //------------------------------------------------------------------------ 
          ["geo"],  
          <JSON>startupConfiguration
      );
  } 
  //----------------------------------------------------------------------   
}
```
