---
layout: post
title: iOS8、Xcode6上地图定位不调用代理方法的解决方法(一)
category: iOS
tags: Map
keywords:
description:
---

>IDE:Xcode Version 6.1 (6A1052d),系统:OS X Yosemite 10.10.2 (14C109)

####使用 ***CoreLocation/CoreLocation.h*** 定位时，出现了 ***CLLocationManagerDelegate*** 方法不调用的问题。

主要代码如下:
	
	#import "ViewController.h"
	#import <CoreLocation/CoreLocation.h>

	@interface ViewController ()<CLLocationManagerDelegate>
	@property (nonatomic,strong)CLLocationManager *mgr;
	@end
	
	@implementation ViewController	
	// 懒加载
	- (CLLocationManager *)mgr {
	    
	    if (_mgr == nil) {
	        _mgr = [[CLLocationManager alloc] init];
	        _mgr.delegate = self;
	    }
	    return _mgr;
	}
	
	- (void)viewDidLoad {
	    [super viewDidLoad];	    
	    // 开始定位
	    [self.mgr startUpdatingLocation];	    
	}
	
	#pragma mark - 实现代理方法
	/**
	 *  获取位置
	 *
	 *  @param locations 获取经度、纬度
	 */
	- (void)locationManager:(CLLocationManager *)manager didUpdateLocations:(NSArray *)locations {
	    
	    CLLocation *location = [locations lastObject];
	    CLLocationCoordinate2D coordinate = location.coordinate;
	    NSLog(@"纬度：%f  经度：%f",coordinate.latitude,coordinate.longitude);
	    
	}
	
	/**
	 *  错误信息
	 *
	 *  @param error   位置获取失败时显示的信息
	 */
	- (void)locationManager:(CLLocationManager *)manager didFailWithError:(NSError *)error {	
	    NSLog(@"%@",error);	
	}
	
	- (void)didReceiveMemoryWarning {
	    [super didReceiveMemoryWarning];
	    // Dispose of any resources that can be recreated.
	}
	
	@end
	
方法中，在懒加载处 _mgr.delegate = self ,@interface 处也写明了 <CLLocationManagerDelegate> ,之后写了获取位置的代理方法，locationManager:didUpdateLocations: ，看似没啥错误了，代理方法就是不执行...

************************************************

####解决办法:

在 info.plist 文件中添加 ***NSLocationAlwaysUsageDescription***
***NSLocationWhenInUseUsageDescription*** 这两个字段

![1](/public/img/iOS/map-Location.png)

还有一个操作就是:

在 CoreLocation 前调用 

	requestWhenInUseAuthorization()
或者
	
	requestAlwaysAuthorization()
到这里，运行代码，问题已经解决

附修改后的代码

	#import "ViewController.h"
	#import <CoreLocation/CoreLocation.h>	
	@interface ViewController ()<CLLocationManagerDelegate>
	@property (nonatomic,strong)CLLocationManager *mgr;
	@end
	
	@implementation ViewController	    	
	// 懒加载
	- (CLLocationManager *)mgr {
	    	    
	    if (_mgr == nil) {
	        _mgr = [[CLLocationManager alloc] init];
	        _mgr.delegate = self;
	        [_mgr requestAlwaysAuthorization];
	    }
	    return _mgr;
	}
	
	- (void)viewDidLoad {
	    [super viewDidLoad];	    
	    // 开始定位
	    [self.mgr startUpdatingLocation];	    
	}
	
	#pragma mark - 实现代理方法
	/**
	 *  获取位置
	 *
	 *  @param locations 获取经度、纬度
	 */
	- (void)locationManager:(CLLocationManager *)manager didUpdateLocations:(NSArray *)locations {
	    
	    CLLocation *location = [locations lastObject];
	    CLLocationCoordinate2D coordinate = location.coordinate;
	    NSLog(@"纬度：%f  经度：%f",coordinate.latitude,coordinate.longitude);
	    
	}
	/**
	 *  错误信息
	 *
	 *  @param error   位置获取失败时显示的信息
	 */
	- (void)locationManager:(CLLocationManager *)manager didFailWithError:(NSError *)error {	
	    NSLog(@"%@",error);	
	}
	
	- (void)didReceiveMemoryWarning {
	    [super didReceiveMemoryWarning];
	    // Dispose of any resources that can be recreated.
	}
	
	@end

info.plist 文件前后对比


![2](/public/img/iOS/Map-plistFile-change.png)

viewController.m 文件变动

![3](/public/img/iOS/Map-ViewControllerFile-change.png)

>造成这问题的原因是:iOS8 修改了位置设置里的内容，增加了一套状态(使用中/通常可用)，所以 CLLocationManager 注册后 delegate 就不响应了。

参考博客:
[iOS8下的开发变化](http://www.cocoachina.com/bbs/read.php?tid=217107)

