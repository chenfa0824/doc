## **一、参考资料**

**DTO 数据传输对象 将需要传输的数据封装成为一个对象**

https://baike.baidu.com/item/DTO/16016821?fr=aladdin

**web与wap**

https://zhidao.baidu.com/question/166939647.html

**github的api文档**

https://developer.github.com/apps/building-oauth-apps/

**Github 授权登录教程与如何设计第三方授权登录的用户表**

https://www.jianshu.com/p/03581c464aa3

**Java 实现 Github 第三方授权登陆**

https://www.jianshu.com/p/5f1b268216d0

**基于Springboot Security开发的Github第三方登录Demo实例**

https://www.cnblogs.com/liuxianan/p/java-qq-weibo-login.html

sql语句地址

https://pan.baidu.com/share/init?surl=WcH637cny39y4fEY0_rCAg

密码：7sbf



## **二、Github第三方授权登录**

1、创建应用，获取Client ID与Client Secret

**首先需要创建一个oauth app，获取Client ID与Client Secret**

[**https://github.com/settings/developers**](https://github.com/settings/developers)

**参考文档**

https://developer.github.com/apps/

create oauth app

https://developer.github.com/apps/building-oauth-apps/creating-an-oauth-app/

Client ID f3a986944a5aff6abe06

Client secrets 9108d33a1ecd47b985f43fa8e93bd6d128fc8679

![img](C:\Users\96549\AppData\Local\YNote\data\weixinobU7VjtQ_XlL9JHU2XTyRoO75HLo\09cf2b28f9db4268934f236cee2906b4\clipboard.png)

应用信息：图标、应用名称、描述、回调地址等等

![img](C:\Users\96549\AppData\Local\YNote\data\weixinobU7VjtQ_XlL9JHU2XTyRoO75HLo\052e3efb13654e8aac43cae6997ee057\clipboard.png)

其中 Application name 会在请求用户授权的时候显示, Authorization callback URL填写的地址会在用户授权成功后跳转，这里填写的信息之后都可以再次修改

**为项目创建一个access token**

![img](C:\Users\96549\AppData\Local\YNote\data\weixinobU7VjtQ_XlL9JHU2XTyRoO75HLo\83237fa54dfb41b1b2a65229a167f648\截图.png)

#### 2、官方文档授权流程

**根据官方文档说明，Web应用认证流程分为以下三步：**

**（1）用户被第三方客户端(也就是我们的应用)重定向至Github官网请求用户授权。**

**（2）用户被Github带上短暂有效的code重定向回第三方客户端（ 根据Authorization callback URL 指定的地址。**

**（3）第三方客户端 可以带着通过code向Github换取的用户access_token访问Github API 得到用户的资源。**

**redirect-code-accesstoken**

**官方参考文档**

https://developer.github.com/apps/building-oauth-apps/authorizing-oauth-apps/

![img](C:\Users\96549\AppData\Local\YNote\data\weixinobU7VjtQ_XlL9JHU2XTyRoO75HLo\24dff7a0cb1f4ec09f726111638ca972\image.png)

**4、Github授权登录的时序图**

![img](C:\Users\96549\AppData\Local\YNote\data\weixinobU7VjtQ_XlL9JHU2XTyRoO75HLo\41ad1970ab8c4a47be812f174e748119\image.png)

#### 3、授权登录功能实现

[**https://developer.github.com/apps/building-oauth-apps/authorizing-oauth-apps/**](https://developer.github.com/apps/building-oauth-apps/authorizing-oauth-apps/)

**（1）写一个基本的登录页面，点击登录按钮跳到github的授权登录页面**

**（2）调用authorize接口，回调返回code**

**（3）调用access_token接口，携带（2）中参数code，返回access_token**

**（4）调用github的user api接口，携带（3）中参数access_token，返回user信息**

**测试手动生成一个access_token**

https://github.com/settings/tokens

测试url接口地址 get方法，参数是access_token

https://api.github.com/user?access_token=4070acc26145839b0eeb7b013aaf8d11fa72613d

json与java类对象之间的对应关系

GithubUser githubUser = JSON.*parseObject*(json, GithubUser.class);

String json=JSON.*toJSONString*(githubUser);

1、获取code，授权之后会跳转到回调地址

接口url 

https://github.com/login/oauth/authorize?client_id=f3a986944a5aff6abe06&scopes=user

get请求

在授权成功之后，就会带上code跳转至callback URL

[http://localhost:8888/fafa.html?code=21f82602133f1a4ee2c4](https://link.jianshu.com/?t=http%3A%2F%2Flocalhost%3A8080%2Fgithub.html%3Fcode%3D21f82602133f1a4ee2c4)

2、通过code换取access_token

接口https://github.com/login/oauth/access_token

post请求

通过java调用restTemplate去Github服务器请求Github 用户的access_token

使用restTemplate，也可以使用okhttp框架等

3、调用api

得到access_token后我们就可以愉快的去访问Github API啦，如果出现权限不够的情况，那么就请回到第一步去根据需要调整scope啦

顺便介绍一下在在Github API请求中带token的几种方法

1. Headers 设置请求头

在Http Headers中设置

| Key           | Value                 |
| ------------- | --------------------- |
| Authorization | token ${access_token} |

1. URL Params

在访问的URL中带上参数access_token=${access_token}

例如:[https://api.github.com/user?access_token=$](https://link.jianshu.com?t=https%3A%2F%2Fapi.github.com%2Fuser%3Faccess_token%3D%24){access_token}

![img](C:\Users\96549\AppData\Local\YNote\data\weixinobU7VjtQ_XlL9JHU2XTyRoO75HLo\566aeedc49d6410794bd2ffde250341f\3084cec4e4864429e78b1f9a416f77a.png)

http://localhost:8888/callback?code=3b8cbc58082e0f0128a3

![img](C:\Users\96549\AppData\Local\YNote\data\weixinobU7VjtQ_XlL9JHU2XTyRoO75HLo\b47ba8c4180041d1a7deed179ed99b7a\clipboard.png)

[https://api.github.com/user?access_token=](https://link.jianshu.com?t=https%3A%2F%2Fapi.github.com%2Fuser%3Faccess_token%3D%24)06391a84ff4accdf92b134adf9fbe876e43e8e9e

![img](C:\Users\96549\AppData\Local\YNote\data\weixinobU7VjtQ_XlL9JHU2XTyRoO75HLo\8400d1fac1124f8ab7616a29c16ca3f8\clipboard.png)

类似用户系统这边的登录也是这样

接口文档中建议我们在授权登录时传入一个加密的数据防止被攻击，我们传入了UUID，最后重定向到授权页面

加入群聊347478005

添加QQ好友

<a href="tencent://message/?uin=757453794&Site=&Menu=yes" 

target="_blank" title="点击添加服主好友">服主QQ:757453794</a>

uin=后直接跟QQ号即可。

添加QQ群

<a href="https://jq.qq.com/?_wv=1027&k=51ukmBo" 

target="_blank" title="点击加入玩家交流群">QQ群:618967676</a>

QQ群的参数来源：在群的主页有个群应用——>群分享——> 由此获得群链接。