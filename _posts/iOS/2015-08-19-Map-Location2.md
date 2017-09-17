---
layout: post
title: iOS8、Xcode6上地图定位不调用代理方法的解决方法(二)
category: iOS
tags: Map
keywords:
description:
---

>开发环境：

IDE：Xcode Version 6.1 (6A1052d)   

iOS Simulator：iPhone 5/5S/6

### 改之前的代码如下：

	//  ViewController.m

	//  mapKit-2

	//

	//  Created by zhanglongqing on 15/7/30.

	//  Copyright (c) 2015年 klone. All rights reserved.

	//

	# import "ViewController.h"	
	# import <MapKit/MapKit.h>
	
	@interface ViewController ()<MKMapViewDelegate>
	
	@property (weak, nonatomic) IBOutlet MKMapView *mapView;
	
	@end
	
	@implementation ViewController
	
	- (void)viewDidLoad {
	  
	    [super viewDidLoad];	  
	    self.mapView.delegate = self;	  
	    self.mapView.userTrackingMode = MKUserTrackingModeFollow;  
	    self.mapView.showsUserLocation = YES;
	  
	  }
	
	// 获取用户位置	
	- (void)mapView:(MKMapView *)mapView didUpdateUserLocation:(MKUserLocation *)userLocation {
	  
	    NSLog(@"获取用户位置");
	
	}
	
	// 地图加载失败时的 error 信息
	- (void)mapViewDidFailLoadingMap:(MKMapView *)mapView withError:(NSError *)error {
	  
	    NSLog(@"%@",error);
	
	}
	
	- (void)didReceiveMemoryWarning {
	  
	    [super didReceiveMemoryWarning];
	  
	    // Dispose of any resources that can be recreated.
	  
	  }
	
	@end



>之前定位的时候遇到了 **CLLocationManagerDelegate** 的问题，那个是不报错的情况下单纯的不调用代理方法而已。不过，这次的错误 Diao，不但不调用 MKMapViewDelegate 方法，它还报错，提示信息如下：

"***Trying to start MapKit location updates without prompting for location authorization. Must call -[CLLocationManager requestWhenInUseAuthorization] or -[CLLocationManager requestAlwaysAuthorization] first.***"

有提示解决就容易多了，可以参照[之前一篇](http://klone1127.github.io/2015/07/28/Map-Location.html)的方法在 info.plist 文件中添加了 **NSLocationAlwaysUsageDescription** **NSLocationWhenInUseUsageDescription** ，然后调用 ***[self.locationManager requestAlwaysAuthorization]*** 或者 ***[self.locationManager requestWhenInUseAuthorization]*** ，也可以对当前 iOS 平台进行一下判断 

	if ([CLLocationManager locationServicesEnabled]) {
			方法体;
		if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 8.0) {
		
	    [self.locationManager requestWhenInUseAuthorization]; 
	    
	    }
	}

### 修改后的 viewdidLoad 

	- (void)viewDidLoad {
	    [super viewDidLoad];
	    if ([CLLocationManager locationServicesEnabled]) {
	    
	    self.locationManager = [[CLLocationManager alloc] init];
	    self.locationManager.delegate = self;
	    
	    self.mapView.delegate = self;
	    self.mapView.userTrackingMode = MKUserTrackingModeFollow;
	    
	    if ([[[UIDevice currentDevice] systemVersion]floatValue] >= 8.0) {
	        [self.locationManager requestWhenInUseAuthorization]; 
	        }
	    }
	}	

或者

	- (void)viewDidLoad {

	    [super viewDidLoad];	    

	    self.locationManager = [[CLLocationManager alloc] init];
	    [self.locationManager requestAlwaysAuthorization];
	    self.locationManager.delegate = self;
	    
	    self.mapView.delegate = self;
	    self.mapView.userTrackingMode = MKUserTrackingModeFollow;
	    self.mapView.showsUserLocation = YES;
	    [self.locationManager startUpdatingLocation];
	        
	}

这样基本上就解决问题了。。。
