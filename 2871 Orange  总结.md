<h1><center>2871 Orange  总结</center></h1>

### 一、V01

#### 1.修改内置语言

```
http://192.168.3.79:8084/#/c/74208/
```

修改点：将想要预置的语言缩写添加到 "PRODUCT_LOCALES := "后面，第一位为默认语言。在**locale_config.xml**中对应添加即可

#### 2.修改客户信息

```
http://192.168.3.79:8084/#/c/74278/
```

修改点：修改**ProjectConfig.mk**中的对应宏控即可。

> 注：model中使用 %space% 代替空格。其他地方用“_”，代码中会做替换。

#### 3.设备在电脑上显示的名字

> 注：Linux和Windows显示的设备名不是同一个宏。

Linux显示的默认为model，2871 orange中修改为product

```
http://192.168.3.79:8084/#/c/74293/
```

windows显示的为sagereal_pc_name。

```
http://192.168.3.79:8084/#/c/74378/
```

#### 4.APN

```
http://192.168.3.79:8084/#/c/74216/
```

根据客户提供的表格添加apn，修改路径为 sagereal/product/VQ287_GM2871_ORANGE/default/apns-conf.xml

```
<apn carrier="Orange IMS APN"
    mcc="208"       //mcc 
    mnc="01"		//mnc
    apn="ims"		//apn的值
    type="ims"		//apn类型
    protocol="IPV4V6"          		//
    roaming_protocol="IPV4V6"
    network_type_bitmask="1|2|3|4|5|6|7|8|9|10|11|12|13|14|15|16|17|18|19|20"
/>
```

```
authtype     
	0：无，1：PAP，2：CHAP，3：PAP或CHAP。
protocol / roaming_protocol
	有效值为“ IP”，“ IPV6”，“ IPV4V6”。
type
	有效值是“default”，“ mms”，“ supl”（由GPS使用），“ dun”（由网络共享，热点使用），“ hipri”，“ fota”，“ ims”，“ cbs”，“ ia”，“紧急”，“ dm”，“ wap”，“ net”，“ cmmail”，“ tethering”（逐步淘汰，请勿使用！），“ rcse”，“ xcap”，“ rcs”，“ *”。
bearer_bitmask (P0分支后被network_type_bitmask替换)
    此字段用于指定可在哪些承载下使用APN。
    如果配置为“ 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18 | 19”，则表示可以使用此apn通过移动数据和wifi均可。
    如果配置为“ 18”，则表示此apn仅可用于wifi。
user_visible
	的值可以是“ true”或“ false”，这是自M1开始支持的值。默认为true。如果配置为false，则apn将被隐藏，而不显示在“设置”的apn列表中。
user_editable
	的值可以是“ true”或“ false”，这是O1以来支持的值。默认为true。如果配置为false，则apn将是只读的，在用户界面上以灰色显示。
```

```
	// 名称
	private String name;
	// apn
	private String apn;
	// 代理
	private String proxy;
	// 端口
	private String port;
	// 用户名
	private String user;
	// 服务器
	private String server;
	// 密码
	private String password;
	// 彩信
	private String mmsc;
	// 彩信代理
	private String mmsproxy;
	// 采集端口
	private String mmsport;
	// 移动国家代码
	private String mcc; 
	// 移动网络代码
	private String mnc;
	// 号码
	private String numeric;
	// apn类型
	private String type;
```

##### 4.1隐藏apn

> alps/vendor/mediatek/proprietary/packages/apps/MtkSettings/src/com/android/settings/ApnSettings.java

```
if("20801".equals(mccmnc)){  
    if("Orange IMS APN".equals(pref.getTitle())&&"ims".equals(pref.getSummary())){
        cursor.moveToNext();
        continue;
    }
}
```

通过mccmnc判断卡，然后判断是不是需要隐藏的类型，如果是则移除显示。

#### 5.添加默认浏览器书签

```
http://192.168.3.79:8084/#/c/74222/
```

在strings.xml中添加书签列表和书签，在mtk_strings.xml中设置书签列表和默认书签。在BrowserSettings.java和BrowserSettings2.java中根据不同mccmnc的值加载不同书签列表。

```
else if(mMccmnc == 20801){
    url = mContext.getResources().getString(R.string.homepage_base_site_navigation5);
    mPrefs.edit().putInt("isFirstBoot",1).apply();
}
```

```
else if(mccmnc == 20801){
    id = R.array.bookmarks5;
    mPrefs.edit().putInt("defaultbookmarts",1).apply();
}

List<Integer> ll = new ArrayList<Integer>();
ll.add(23415);ll.add(26801);ll.add(21401);ll.add(21670);ll.add(20801);ll.add(26003);ll.add(22610);
```

#### 6.添加voicemail

```
http://192.168.3.79:8084/#/c/74227/
```

路径：alps/vendor/mediatek/proprietary/frameworks/base/telephony/etc/voicemail-conf.xml

根据numeric中的mccmnc判断不同的卡，vmnumber为默认号码，vmtag为打出去后显示的标题

```
<!--Redmine 120120 wangxin add voicemail begin-->
<voicemail numeric="20801" carrier="" vmnumber="888" vmtag="Voicemail" />
<voicemail numeric="26003" carrier="" vmnumber="501" vmtag="Voicemail" />
<voicemail numeric="22610" carrier="" vmnumber="555" vmtag="Voicemail" />
<!--Redmine 120120 wangxin add voicemail end-->
```

#### 7.预置邮箱配置

```
http://192.168.3.79:8084/#/c/74232/
```

配置发送接收邮箱，domain的值为@出来的后缀。

```
<provider id="Orange" label="Orange" domain="orange.fr">
    <incoming uri="imap+ssl+://imap.orange.fr:993" username="$email"/>
    <outgoing uri="smtp+ssl+://smtp.orange.fr:465" username="$email"/>
</provider>
```

在AccountSettingsUtils.java中判断mccmnc，加载出配置邮箱的xml文件。

```
if (mccmnc == 20801) {
    xml = context.getResources().getXml(R.xml.providers);
    while ((xmlEventType = xml.next()) != XmlResourceParser.END_DOCUMENT) {
		if (xmlEventType == XmlResourceParser.START_TAG && "provider".equals(xml.getName())) {
            String providerDomain = getXmlAttribute(context, xml, "domain");
            if (!serverName.contains(providerDomain) && !hasWildchars(providerDomain)) {
            	serverName.add(providerDomain);
            }
		}
	}
}
```

#### 8.添加紧急号码

```
http://192.168.3.79:8084/#/c/74213/
```

在alps/vendor/mediatek/proprietary/external/EccList/ecc_list.xml中添加。根据不同mccmnc加载不同紧急号码。

```
<EccEntry Ecc="911" Category="0" Condition="1" Plmn="208 01"/>
<EccEntry Ecc="999" Category="0" Condition="1" Plmn="208 01"/>
<EccEntry Ecc="997" Category="0" Condition="1" Plmn="208 01"/>
<EccEntry Ecc="998" Category="0" Condition="1" Plmn="208 01"/>
<EccEntry Ecc="112" Category="0" Condition="1" Plmn="208 01"/>
<EccEntry Ecc="112" Category="0" Condition="1" Plmn="260 03"/>
```

Condition：
           0:表示在无卡的时候当紧急号码；

​           1:表示始终当紧急号码；

​           2:表示界面上显示成紧急拨号，但实际以普通方式拨出。

Category：

​			国内默认都是'0'，国外根据实际情况选择。 

>参考文件： 
>```
>http://192.168.3.86:8989/wcp/webdoc/view/Pub402880cd5c5da6e7015da59636070e69.html
>```

##### 8.1紧急电话是否保存通话记录

如果有宏控

> allow_emergency_numbers_in_call_log = true
>
> true:不保存
>
> false:保存

没有宏控时

```
http://192.168.3.79:8084/#/c/74440/
```

#### 9.修改开关机动画和壁纸

```
http://192.168.3.79:8084/#/c/74235/
```

更换资源即可

#### 10.修改铃声资源

```
http://192.168.3.79:8084/#/c/74262/
```

更换资源,修改**AllAudio.mk**和**AudioPackageGo.mk**文件

##### 10.1修改默认铃声

```
http://192.168.3.79:8084/#/c/74311/
```

#### 11.移除apk

```
http://192.168.3.79:8084/#/c/74306/
```

有宏控的关宏控，需要宏控的加宏控,最直接LOCAL_OVERRIDES_PACKAGES.

```
#Redmine 115058 AXIOM remove Calculator wangxin begin
ifneq ($(strip $(MP4P_AXIOM_REMOVE_APK)),yes)
  PRODUCT_PACKAGES += ExactCalculator
endif
#Redmine 115058 AXIOM remove Calculator wangxin end
```

```
LOCAL_OVERRIDES_PACKAGES := MtkLauncher3QuickStep Home Launcher2 Launcher3 Launcher3Go MtkLauncher3 MtkLauncher3Go
```
