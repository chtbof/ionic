# 第16課-(1) 加入Chart.js


#### 執行圖示
![GitHub Logo](/images/fig16-01.jpg)



## App部份

#### (1)建立一個App, 名稱為 myApp:
```
ionic start myApp blank --v2
```


#### (2)加載chart.js
```
npm install chart.js --save
```


#### (3.1) home.ts (加入Chart引用)
```javascript
import { Component, ViewChild } from '@angular/core';
import { NavController } from 'ionic-angular';

import { Chart } from 'chart.js';   /* 增加 */

 
@Component({
    selector: 'page-home',
    templateUrl: 'home.html'
})
export class HomePage {
    //-------------------------------------
    // 成員
    //-------------------------------------
    @ViewChild('myCanvas') myCanvas; 
    myChart: any;


    //-------------------------------------
    // 建構元
    //-------------------------------------
    constructor(public navCtrl: NavController) {}
 

    //-------------------------------------
    // 畫面載入後即執行的函式
    //-------------------------------------
    ionViewDidLoad() {
        this.myChart = new Chart(this.myCanvas.nativeElement, {
            type: 'bar',
            data: {
                labels: ["幼年", "青壯年", "老年"],
                datasets: [{
                    label: '分佈百分比(%)',			
                    data: [13.8, 75.98, 10.21],
                    backgroundColor: [
                        'rgba(54, 162, 235, 0.2)',                    
                        'rgba(54, 162, 235, 0.2)',
                        'rgba(54, 162, 235, 0.2)'
                    ],
                    borderColor: [
                        'rgba(54, 162, 235, 1)',
                        'rgba(54, 162, 235, 1)',
                        'rgba(54, 162, 235, 1)'                
                    ],
                    borderWidth: 1
                }]
            },
            options: {
                scales: {
                    yAxes: [{
                        ticks: {
                            beginAtZero:true
                        }
                    }]
                }
            }
        });
    }
    //------------------------------------- 
}
```


#### (3.2) home.html
```html
<ion-header>
  <ion-navbar>
    <ion-title>
      Ionic Blank
    </ion-title>
  </ion-navbar>
</ion-header>

<ion-content>
    <!-- ......................................... -->
    <ion-card>          
        <ion-card-header>
            2015 新北市性別年齡人口分佈
        </ion-card-header>

        <ion-card-content>
            <canvas #myCanvas width="100" height="90"></canvas>
        </ion-card-content>
    </ion-card>
    <!-- ......................................... -->    
</ion-content>
```



#### (5)手機架構:
```
 d:\
  |___ <myApp>                                      
          |___ <src>            
                 |___ <pages>   
                         |___ <home> 
                                |___ home.ts
                                |___ home.html    
                             
```

