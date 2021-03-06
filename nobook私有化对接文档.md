# 私有化部署URL免密登录接口

## 1、接口定义

主要是为第三方用户对接使用 用户登录NOBOOK虚拟实验平台，需要开发者根据用户信息在服务端生成一个免登录url，通过这个url链接，NOBOOK才能知道是哪个用户来访问。 为了确保客户端每次请求到都是最新的免登陆url，客户端每次向服务器发的请求不能是固定的，以避免请求被缓存。 NOBOOK虚拟实验免登录url经过签名，该url地址5分钟失效，请务必在生成地址后立即使用，使用后页面会重定向进入NOBOOK。

同理

开发者服务器端需要开发一个支持重定向的接口实现动态生成免登录url地址，该接口地址配置在客户端，用户通过点击该地址访问应用。

功能说明：
> * 第三方有独立的用户系统
> * 第三方用户在自己平台登录，调用应用接口，直接访问应用
> * 创建应用时需要应用有免密登录权限才能对接
> * 需要根据免密地址需要传递必要的参数请看下方介绍



## 2. 免登录URL生成
### 请求说明
请求方式：get<br>
编码说明：UTF-8 <br>
请求URL：http://xxx.com/login/autologin?uid=111&realname=xxx&appid=appid&tamp=1526542472&scope=1%2C2&sign=c09f38e459b1fb065cd94e7067ce2fc6&return_url=产品地址


### 2.1 参数说明

| 参数        | 是否必须   |  参数类型  | 限制长度 |参数说明|
| --------    | :-----     | :----     | :----   | :----|
| uid    | 必须     | str     | 11   | 用户唯一性标识，对应唯一一个用户且不可变|
| realname     | 必须     | str     | 30   | 用户昵称|
| appid     | 必须     | str     | 255   | 应用的唯一标识|
| tamp     | 必须     | str     | 255    | 1970-01-01开始的时间戳，秒为单位。|
| scope     | 必须     | str     | 20    | 产品授权列表(注意：多个产品用英文逗号隔开),<br>如授权一个NB化学实验产品：<br>scope = 2,<br>如授权NB化学实验产品和NB物理实验产品：<br>scope = 1,2<br>产品：<br>1:NB物理实验 ,<br> 2:NB化学实验 , <br> 3:NB生物实验初中版 , <br> 4:NB生物高中版 , <br> 5:NB小学科学,<br>6:NB物理实验资源版初中版, <br>  7:NB物理实验资源版高中版,  <br> 8:NB化学实验资源版初中版,<br>9:NB化学实验资源版高中版|
| return_url    | 非必须     | str     | 255    | 登录成功后的重定向地址，可以直达到任意页面<br>注意：如未设置return_url参数，则会根据return_pid参数进入相应的产品|
| sign     | 必须     | str     | 255   | MD5签名 md5（appid appkey realname scope tamp uid）|


### return_url 跳转产品页面汇总

| 页面       | 地址   |  说明 |
| --------    | :-----     |  :-----     | 
| 实验平台首页    | http://ip     | 首页|
| 物理实验页面 | http://ip/wuli/ | 物理实验地址|
| 化学实验页面 | http://ip/huaxue/ | 化学实验地址|
| 初中生物实验页面 | http://ip/shengwu/ | 初中生物实验地址|
| 高中生物实验页面 | http://ip/shengwu/gz/ | 高中生物实验地址|
| 小学科学实验页面 | http://ip/xiaoke/ | 小学科学实验地址|


### 代码示例php
```
<?php

//配置参数
$uid='';
$appid='';
$appkey = '';
$tamp = time();
$realname='';
$scope = '';
$sign = md5($appid.$appkey. $realname.$scope.$tamp.$uid);
$return_url='/';


//获取登录Url
function getLoginUrl($tamp, $uid, $realname, $appid, $scope, $sign, $return_url)
{
    $param =  array(
        'tamp'=> $tamp,
        'uid'=>$uid,
        'realname'=>$realname,
        'appid'=> $appid,
        'scope'=> $scope,
        'sign'=> $sign,
        'return_url'=> $return_url,
    );

    $url = 'http://xxx.com/login/autologin?'.http_build_query($param);

    return $url;

}

$getLoginUrl = getLoginUrl($tamp,$uid,$realname,$appid, $scope, $sign, $return_url);

echo $getLoginUrl;

?>

```







