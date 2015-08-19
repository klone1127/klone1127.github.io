---
layout: post
title: 地图地址属性
category: 技术
tags: iOS
keywords:
description:
---

>在反地理编码时时常要获取经纬度对应的地址名称,这时我们就要用到一些 address 属性

官方 API 写的是：

	// address dictionary properties
	@property (nonatomic, readonly, copy) NSString *name; // eg. Apple Inc.
	@property (nonatomic, readonly, copy) NSString *thoroughfare; // street address, eg. 1 Infinite Loop
	@property (nonatomic, readonly, copy) NSString *subThoroughfare; // eg. 1
	@property (nonatomic, readonly, copy) NSString *locality; // city, eg. Cupertino
	@property (nonatomic, readonly, copy) NSString *subLocality; // neighborhood, common name, eg. Mission District
	@property (nonatomic, readonly, copy) NSString *administrativeArea; // state, eg. CA
	@property (nonatomic, readonly, copy) NSString *subAdministrativeArea; // county, eg. Santa Clara
	@property (nonatomic, readonly, copy) NSString *postalCode; // zip code, eg. 95014
	@property (nonatomic, readonly, copy) NSString *ISOcountryCode; // eg. US
	@property (nonatomic, readonly, copy) NSString *country; // eg. United States
	@property (nonatomic, readonly, copy) NSString *inlandWater; // eg. Lake Tahoe
	@property (nonatomic, readonly, copy) NSString *ocean; // eg. Pacific Ocean
	@property (nonatomic, readonly, copy) NSArray *areasOfInterest; // eg. Golden Gate Park
	
翻译过来大概就是：
	
		name     	            :   地名
		thoroughfare            :   街道
		subThoroughfare         :   街道相关信息，例如门牌等
		locality                :   城市
		subLocality             :   城市相关信息，例如标志性建筑
		administrativeArea      :   直辖市
		subAdministrativeArea   :   其他行政区域信息
		postalCode              :   邮编
		ISOcountryCode          :   国家编码
		country;                :   国家
		inlandWater             :   水源、湖泊
		ocean;                  :   海洋
		areasOfInterest         :   关联的或利益相关的地标
		
		