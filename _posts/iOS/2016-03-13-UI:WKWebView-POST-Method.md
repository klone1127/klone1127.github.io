---
layout: post
title: UI/WKWebview 的 POST 请求
category: iOS
tagsd: iOS
keywords:
description:
---

> 首先说下我遇到的问题：在项目中加载的是 HTML5 ，用户退出时发送的请求是 DELETE 请求，服务器使用的是 Ruby ，Ruby 本身并不能实现 DELETE 方法([具体看这里](http://railscasts.com/episodes/77-destroy-without-javascript))，大概来说它就是 POST 方法，所以我在 UI/WKWebvie 中使用 POST 方法就可以了，其实 UI/WKWebview 默认 HTTP 请求是 GET 请求，所以这里可以改用 POST 方法。


代码：

	+ (void)HTTPPOSTMethodWithRequest:(NSURLRequest *)request URL:(NSString *)urlString blockWithReq:(requestBlock)requestBlock {
	    NSMutableURLRequest *req = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:urlString]];
	    if ([request.HTTPMethod isEqualToString:@"POST"]) {
	        req.HTTPBody = request.HTTPBody;
	        req.HTTPMethod = request.HTTPMethod;
	    }
	    requestBlock(req);
	}


核心代码：

	    NSMutableURLRequest *req = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:urlString]];
	    if ([request.HTTPMethod isEqualToString:@"POST"]) {
	        req.HTTPBody = request.HTTPBody;
	        req.HTTPMethod = request.HTTPMethod;
	    }
	    
这样加载的页面发送的就是 POST 请求了，我的问题就是这样解决的。
