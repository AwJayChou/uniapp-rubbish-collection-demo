### 基于umi和百度开放平台制作垃圾分配小程序



#### 效果



![uni](https://github.com/AwJayChou/uniapp-rubbish-collection-demo/tree/master/images/uni.jpg)



#### 配置

 根据网址去申请图像识别功能api  每天限量500不收费



![scretid](https://github.com/AwJayChou/uniapp-rubbish-collection-demo/tree/master/images/secretid.jpg)



#### 技术实现

基于uni-app, 用到组件库垃圾分类接口，下载需要登录dcloud



#### 使用

编译到dev 再用微信小程序打开



#### 实现思路



- 获取access token

```text
https://aip.baidubce.com/oauth/2.0/token?grant_type=client_credentials&client_id=Va5yQRHlA4Fq5eR3LT0vuXV4&client_secret=0rDSjzQ20XUj5itV6WRtznPQSzr5pVw2&
```

- 请求图片识别接口

  https://aip.baidubce.com/rest/2.0/image-classify/v2/advanced_general

- 根据接口返回值调用垃圾分类接口

  