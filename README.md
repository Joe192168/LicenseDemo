# LicenseDemo

#### 项目介绍
在基于Spring的项目中使用 `TrueLicense `生成和验证`License证书`（服务器许可）的示例代码

#### 技术依赖：
* `Spring Boot`：项目基础架构
* `TrueLicense `：基于`Java`实现的生成和验证服务器许可的简单框架

#### 环境依赖：
* `JDK8+`

#### 两个子项目说明： ####

- `ServerDemo`：用于**开发者**给客户生成`License证书`的示例代码
- `ClientDemo`：**模拟需要给客户部署的业务项目**

#### ServerDemo项目： ####

首先的利用keytool生成秘钥
#生成命令
keytool -genkeypair -keysize 1024 -validity 3650 -alias "privateKey" -keystore "privateKeys.keystore" -storepass "public_password1234" -keypass "private_password1234" -dname "CN=localhost, OU=localhost, O=localhost, L=SH, ST=SH, C=CN"

#导出命令
keytool -exportcert -alias "privateKey" -keystore "privateKeys.keystore" -storepass "public_password1234" -file "certfile.cer"

#导入命令
keytool -import -alias "publicCert" -file "certfile.cer" -keystore "publicCerts.keystore" -storepass "public_password1234"

对外发布了两个RESTful接口：

（1）获取服务器硬件信息 ：

请求地址：`http://127.0.0.1:7000/license/getServerInfos`

![获取服务器硬件信息](https://www.zifangsky.cn/wp-content/uploads/2018/07/20180710140711.png)

（2）生成证书 ：

请求地址：`http://127.0.0.1:7000/license/generateLicense`

请求时需要在Header中添加一个 **Content-Type** ，其值为：**application/json;charset=UTF-8**。请求参数如下： 

```json
{
	"subject": "license_demo",
	"privateAlias": "privateKey",
	"keyPass": "private_password1234",
	"storePass": "public_password1234",
	"licensePath": "D:/license.lic",
	"privateKeysStorePath": "D:/privateKeys.keystore",
	"issuedTime": "2020-07-10 00:00:01",
	"expiryTime": "2021-12-31 23:59:59",
	"consumerType": "User",
	"consumerAmount": 1,
	"description": "这是证书描述信息",
	"licenseCheckModel": {
		"ipAddress": ["192.168.0.43", "192.168.154.1", "192.168.136.1"],
		"macAddress": ["94-C6-91-BD-BF-93", "00-50-56-C0-00-01", "00-50-56-C0-00-08"],
		"cpuSerial": "BFEBFBFF000906EA",
		"mainBoardSerial": ""
	}
}
```

(4)验证客户端登陆 POST请求
http://localhost:7080/check?username=admin&password=123

![生成证书](https://www.zifangsky.cn/wp-content/uploads/2018/07/20180710141528.png)

#### ClientDemo项目： ####

项目启动时安装证书，通过`cn/zifangsky/license/LicenseCheckListener.java`类实现。用户登录时校验证书的可用性，通过`cn/zifangsky/license/LicenseCheckInterceptor.java`类实现。

#### 特别说明： ####

详细开发思路可以参考我写的这篇文章：[https://www.zifangsky.cn/1277.html](https://www.zifangsky.cn/1277.html)


>>>>>>> 服务器许可证
