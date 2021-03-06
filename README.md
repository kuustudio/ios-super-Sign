# 环境需求：

    系统环境：Center OS 7.2 * 1
    系统配置：2核4G 500G以上硬盘
    带宽：5M
    域名：已备案域名 * 1
    SSL证书：域名对应的SSL证书 * 1
    阿里云OSS： *1

#### 与其使用别人的签名系统不如自己搭一套。证书安全也有保障，成本也低上很多。

#### 惯例，先放效果图：
![login](https://raw.githubusercontent.com/oopww1992/ios-super-Sign/master/1.jpg)
落地页
![login](https://raw.githubusercontent.com/oopww1992/ios-super-Sign/master/2.jpg)
管理后台

简单流程说明：
![login](https://raw.githubusercontent.com/oopww1992/ios-super-Sign/master/3.jpg)

/////////下面开始干货

#### 1，添加苹果账号。

使用fastlane框架来实现添加苹果账号。

fastlane是整套工具链的基石。

fastlane脚本代码示例：
![login](https://raw.githubusercontent.com/oopww1992/ios-super-Sign/master/4.jpg)


在这里需要实现的逻辑：

添加账号时，需要创建应用，创建证书并保存证书。

因为苹果存在二次验证，这里通过后台弹窗输入验证码。
![login](https://raw.githubusercontent.com/oopww1992/ios-super-Sign/master/5.jpg)


#### 2，获得用户UDID

苹果允许用户通过浏览器安装配置文件，来上传UDID到服务器。服务器需返回301重定向到特定网站。注意，部分设备上需要通过302重定向。要不会存在无法加载的问题。

.mobileConfig 文件实例：
![login](https://raw.githubusercontent.com/oopww1992/ios-super-Sign/master/6.jpg)


#### 3，解析苹果给出的XML文件，并取得对应数据
![login](https://raw.githubusercontent.com/oopww1992/ios-super-Sign/master/7.jpg)


在这个时候，服务端就已经有了用户的udid了。

#### 4，开发者中心更新UDID。

使用fastlane来更新UDID

调用实例代码：
![login](https://raw.githubusercontent.com/oopww1992/ios-super-Sign/master/8.jpg)


更新完成UDID后，从苹果商店下载相关证书，并准备重签名。

#### 5，重签名

看了很多文章，很多都只能运行在mac电脑上。 mac服务器成本昂贵。要支持高并发成本非常高。

在这里，我们使用isign来实现在linux服务器上也能重签名。

可能有人会说他们使用isign在ios13上无法安装，这里需要自己修复一下源码的bug.

目前，实现linux重签名的方式有很多种。isign只是其中一种选择。

实例代码：
![login](https://raw.githubusercontent.com/oopww1992/ios-super-Sign/master/9.jpg)


到此时，我们的重签名流程就已经完成了。

然后将ipa包上传到OSS服务上，并配置itms-service服务来做分发。

itms-service服务代码实例：
![login](https://raw.githubusercontent.com/oopww1992/ios-super-Sign/master/10.jpg)


tip：

另外，如果要通过isign实现企业的签名的话，因为isign的入参是pem文件，在这里，需要将企业证书的P12文件转换为pem文件。实例代码如下：
![login](https://raw.githubusercontent.com/oopww1992/ios-super-Sign/master/11.jpg)

#### 其他一些附加功能：

#### 应用停用与启用控制：

这里需要用到ios的注入，dylib包，举例：控制包的有效期：
![login](https://raw.githubusercontent.com/oopww1992/ios-super-Sign/master/12.jpg)


我们会在包内写入我们用于标识的特殊文件，并在app启动的时候向服务器请求并检查应用的有效期。
通过这个思路能实现 app的推送，应用启动统计，有效期控制，渠道包统计等等一些功能。

以上就是超级签名的全部细节。

#### 我们的系统已经商用并稳定运行半年， 欢迎加我QQ 397294213 共同学习研究。
