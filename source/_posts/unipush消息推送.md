---
title: unipush的消息推送
date: 2021-4-21 10:30:55
tags: app
categories: 踩坑经验
typora-root-url: unipush消息推送
---

<meta name="referrer" content="no-referrer"/>

# unipush的消息推送

## 一、创建项目并开通unipush

1. 申请开发者账号

   - 方法一：在https://dev.dcloud.net.cn/app/index?type=0 网址申请DCloud开发者账号
   - 方法二：在Hbuderx左下角点击未登录，然后注册账号

   ![000](000.png)

2. 登录注册的开发者账号，创建h5或者是uniapp项目，进行开发。

3. 配置manifest.json文件添加推送服务，先进行*基础配置*（AppId、应用名称等）。点击*App模块配置*，勾选Push(消息推送)-->uniPush。点击配置跳转如下界面

   ![001](001.png)

   ![002](002.png)

4. 编辑应用信息，如果想单独打包android可以不选中ios，然后点击开通推送服务就开启了。

   注意：这里的android包名要与后面的厂商应用保持一致，应用签名要与华为平台上的签名文件使用同一个(SHA256)

   至此，个推渠道已经配置完毕，app打包之后在线情况可以收到推送消息

## 二、注册手机厂商账号（华为为例）

1. 直接到https://developer.huawei.com/consumer/cn/华为开发者联盟去注册账号，跟着提示一步步走

2. 账号注册完后登录。到管理中心-->上架及推送服务-->我的项目-->新建项目-->添加应用

   ![01](01.png)

   ![02](02.png)

   ![03](03.png)

   ![04](04.png)

3. 新建完成后，进入如下页面

   ![05](05.png)

4. 项目创建完成后需要生成指纹证书文件（Android平台签名证书(.keystore)）

   如果配置了java环境变量就可以直接往下走，如果没有就需要执行cd命令进入keytool.exe所在的目录（java安装目录的bin下）。

   ​        （1）在命令行输入命令   

   ```c
   keytool -genkey -alias testalias -keyalg RSA -keysize 2048 -validity 36500 -keystore test.keystore
   ```

   - testalias是证书别名，可修改为自己想设置的字符，建议使用英文字母和数字

   - test.keystore是证书文件名称，可修改为自己想设置的文件名称，也可以指定完整文件路径

     (2)  然后跟据命令行提示操作生成keystore文件  

   ​                 **testalias是证书别名  ，还有证书文件密码和位置都需要记住打包的时候必须要。**

   ​    （3）查看keystore文件。输入以下命令

   ```c
   keytool -list -v -keystore test.keystore  
   Enter keystore password: //输入密码，回车
   ```

   ​	输出以下格式信息

   ```c
   Keystore type: PKCS12    
   Keystore provider: SUN    
   
   Your keystore contains 1 entry    
   
   Alias name: test    
   Creation date: 2019-10-28    
   Entry type: PrivateKeyEntry    
   Certificate chain length: 1    
   Certificate[1]:    
   Owner: CN=Tester, OU=Test, O=Test, L=HD, ST=BJ, C=CN    
   Issuer: CN=Tester, OU=Test, O=Test, L=HD, ST=BJ, C=CN    
   Serial number: 7dd12840    
   Valid from: Fri Jul 26 20:52:56 CST 2019 until: Sun Jul 02 20:52:56 CST 2119    
   Certificate fingerprints:    
            MD5:  F9:F6:C8:1F:DB:AB:50:14:7D:6F:2C:4F:CE:E6:0A:A5    
            SHA1: BB:AC:E2:2F:97:3B:18:02:E7:D6:69:A3:7A:28:EF:D2:3F:A3:68:E7    
            SHA256: 24:11:7D:E7:36:12:BC:FE:AF:2A:6A:24:BD:04:4F:2E:33:E5:2D:41:96:5F:50:4D:74:17:7F:4F:E2:55:EB:26    
   Signature algorithm name: SHA256withRSA    
   Subject Public Key Algorithm: 2048-bit RSA key    
   Version: 3
   ```

   其中证书指纹信息（Certificate fingerprints）：

   - MD5
     证书的MD5指纹信息（安全码MD5）
   - SHA1
     证书的SHA1指纹信息（安全码SHA1）
   - SHA256
     证书的SHA256指纹信息（安全码SHA245）

5. 将生成的SHA256填入如图标记部分。

   ![06](06.png)

6. 项目信息完成后，开通推送服务

   ![07](07.png)

   ![08](08.png)

7. 将配置文件  agconnect-services.json文件下载保存，unipush配置厂商通道需要使用

   ![09](09.png)

## 三、配置unipush的厂商推送设置

1. 厂商账号以及推送服务开通之后，回到unipush后台配置界面https://dev.dcloud.net.cn/app/index?type=0

   ![10](10.png)

2. 点击自己需要配置的应用进入到unipush页面，点击厂商推送设置，将各个厂商的应用信息填入对应项中，保存。

   ![12](12.png)

3. 将项目进行云打包，使用自有证书，上面生成的证书。填入证书别名，填入证书密码，填入证书路径，选择打正式包，然后点击打包，等待打包完成手机安装即可。

## 四、服务端代码

#### 准备：pom.xml文件

```xml
<dependency>
    <groupId>com.gexin.platform</groupId>
    <artifactId>gexin-rp-sdk-http</artifactId>
    <version>4.1.1.4</version>
</dependency>
 

 <repositories>
    <repository>
        <id>getui-nexus</id>
        <url>http://mvn.gt.getui.com/nexus/content/repositories/releases/</url>
    </repository>
 </repositories>
```

#### 后端Java：

服务端集成时首先需要获取AppId、AppKey、MasterSecret参数，登录[DCloud开发者中心](https://dev.dcloud.net.cn/)，在“Uni Push”下的“应用配置”页面中获取，如下图所示：

```java
import com.gexin.rp.sdk.base.IPushResult;
import com.gexin.rp.sdk.base.impl.AppMessage;
import com.gexin.rp.sdk.base.impl.SingleMessage;
import com.gexin.rp.sdk.base.impl.Target;
import com.gexin.rp.sdk.base.notify.Notify;
import com.gexin.rp.sdk.base.payload.APNPayload;
import com.gexin.rp.sdk.base.uitls.AppConditions;
import com.gexin.rp.sdk.exceptions.RequestException;
import com.gexin.rp.sdk.http.Constants;
import com.gexin.rp.sdk.http.IGtPush;
import com.gexin.rp.sdk.template.AbstractTemplate;
import com.gexin.rp.sdk.template.LinkTemplate;
import com.gexin.rp.sdk.template.NotificationTemplate;
import com.gexin.rp.sdk.template.TransmissionTemplate;
import com.gexin.rp.sdk.template.style.AbstractNotifyStyle;
import com.gexin.rp.sdk.template.style.Style0;
import org.springframework.stereotype.Service;

public class UninAppNews {
    //参照上方链接获取
    private static String appId = "";
    private static String appKey = "";
    private static String masterSecret = "";

    // 如果需要使用HTTPS，直接修改url即可
    //private static String host = "https://api.getui.com/apiex.htm";
    private static String host = "http://api.getui.com/apiex.htm";

    private static String CID = "";//前端获取
    //指定用户推送
    public void sendNews() {
        // 设置后，根据别名推送，会返回每个cid的推送结果
        System.setProperty(Constants.GEXIN_PUSH_SINGLE_ALIAS_DETAIL, "true");
        IGtPush push = new IGtPush(host, appKey, masterSecret);
        NotificationTemplate template = getNotificationTemplate(); 
        SingleMessage message = new SingleMessage();
        message.setOffline(true);
        // 离线有效时间，单位为毫秒
        message.setOfflineExpireTime(24 * 3600 * 1000);
        message.setData(template);
        // 可选，1为wifi，0为不限制网络环境。根据手机处于的网络情况，决定是否下发
        message.setPushNetWorkType(0);
        // 厂商通道下发策略
        message.setStrategyJson("{\"default\":4,\"ios\":4,\"st\":4}");
        Target target = new Target();
        target.setAppId(appId);
        target.setClientId(CID);
        //target.setAlias(Alias);
        IPushResult ret = null;
        try {
            ret = push.pushMessageToSingle(message, target);
        } catch (RequestException e) {
            e.printStackTrace();
            ret = push.pushMessageToSingle(message, target, e.getRequestId());
        }
        if (ret != null) {
            System.out.println(ret.getResponse().toString());
        } else {
            System.out.println("服务器响应异常");
        }
    }
    // 打开应用的首页
    public static NotificationTemplate getNotificationTemplate() {
        NotificationTemplate template = new NotificationTemplate();
        // 设置APPID与APPKEY
        template.setAppId(appId);
        template.setAppkey(appKey);
        //设置展示样式
        Style0 style = new Style0();
        // 设置通知栏标题与内容
        style.setTitle("请输入通知栏标题");
        style.setText("请输入通知栏内容");
        // 配置通知栏图标
        style.setLogo("icon.png"); //配置通知栏图标，需要在客户端开发时嵌入，默认为push.png
        // 配置通知栏网络图标
        style.setLogoUrl("");
        // 配置自定义铃声(文件名，不需要后缀名)，需要在客户端开发时嵌入后缀名为.ogg的铃声文件
        style.setRingName("sound");
        // 角标, 必须大于0, 个推通道下发有效; 此属性目前仅针对华为 EMUI 4.1 及以上设备有效
        style.setBadgeAddNum(1);

        // 设置通知是否响铃，震动，或者可清除
        style.setRing(true);
        style.setVibrate(true);
        style.setClearable(true);
        style.setChannel("通知渠道id");
        style.setChannelName("通知渠道名称");
        style.setChannelLevel(4); //设置通知渠道重要性
        template.setStyle(style);
        template.setTransmissionType(2);  // 透传消息设置，收到消息是否立即启动应用： 1为立即启动，2则广播等待客户端自启动
        template.setTransmissionContent("请输入您要透传的内容");
		// template.setSmsInfo(PushSmsInfo.getSmsInfo()); //短信补量推送

		// template.setDuration("2019-07-09 11:40:00", "2019-07-09 12:24:00");  // 设置定时展示时间，安卓机型可用
        template.setNotifyid(123); // 在消息推送的时候设置notifyid。如果需要覆盖此条消息，则下次使用相同的notifyid发一条新的消息。客户端sdk会根据notifyid进行覆盖。
        template.setAPNInfo(getAPNPayload()); //ios消息推送
        return template;
    }
    //ios样式模板
    private static APNPayload getAPNPayload() {
        APNPayload payload = new APNPayload();
        //在已有数字基础上加1显示，设置为-1时，在已有数字上减1显示，设置为数字时，显示指定数字
        payload.setAutoBadge("+1");
        payload.setContentAvailable(0);
        //ios 12.0 以上可以使用 Dictionary 类型的 sound
        payload.setSound("default");
        payload.setCategory("$由客户端定义");
        payload.addCustomMsg("由客户自定义消息key", "由客户自定义消息value");
        //简单模式APNPayload.SimpleMsg
		// payload.setAlertMsg(new APNPayload.SimpleAlertMsg("给你发消息了"));
        payload.setAlertMsg(getDictionaryAlertMsg());  //字典模式使用APNPayload.DictionaryAlertMsg
        return payload;
    }
    //ios消息体
    private static APNPayload.DictionaryAlertMsg getDictionaryAlertMsg() {
        APNPayload.DictionaryAlertMsg alertMsg = new 					 APNPayload.DictionaryAlertMsg();
        alertMsg.setBody("小何给你发消息了");//ios消息内容
        alertMsg.setActionLocKey("显示关闭和查看两个按钮的消息");
        alertMsg.setLocKey("loc-key1");
        alertMsg.addLocArg("loc-ary1");
//        alertMsg.setLaunchImage("调用已经在应用程序中绑定的图形文件名");
        // iOS8.2以上版本支持
        alertMsg.setTitle("通知标题");//ios消息标题
//        alertMsg.setTitleLocKey("自定义通知标题");
//        alertMsg.addTitleLocArg("自定义通知标题组");
        return alertMsg;
    }
}
```

## 五、前端处理

 代码需要放在app.vue中的`onLaunch`里面

```vue
onLaunch: function() {
	console.log('App Launch')
	//#ifdef APP-PLUS
	let timer = false;
	plus.push.addEventListener("click",(msg)=>{
		clearTimeout(timer);
		timer = setTimeout(()=>{
			console.log(1111,msg);
			if(msg.payload){
				uni.navigateTo({
					url:msg.payload
				})
			}
		},1500)
	},false)
	plus.push.addEventListener("receive",(msg)=>{
		if("LocalMSG" == msg.payload){
		}else{
			if(msg.type=='receive'){
				var options = {cover:false,title:msg.title};
				plus.push.createMessage(msg.content, msg.payload, options ); 
		 	}  
		}
	},false)
	//#endif
}
```

注：华为配置产商渠道可能有延迟，配置完成可能等等再测试

消息推送模板：https://docs.getui.com/getui/server/java/template/?id=doc-title-8

消息推送demo的git：https://github.com/GetuiLaboratory/getui-pushapi-java-demo/tree/master/src/main/java/com/getui/platform/demo

unipush参考：https://blog.csdn.net/weixin_42678675/article/details/115470016

​							https://blog.csdn.net/object_oriented/article/details/106695077

​							https://blog.csdn.net/z668899/article/details/109805595