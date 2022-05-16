<h1><center>汪鑫GMS考核</center></h1>

- #### GMS包

  期望结果：20天以内，则需要更新

  Fail结果：已超过截止日期55天,当前Gms包版本是：11_202104.go

- #### ACSA

  期望结果：项目预置了GoogleMessage，需要预置[ro.com.google.acsa]: [true]

  Fail结果：[ro.com.google.acsa]结果未空

- #### AppLink

  期望结果： 2020-8-24之后，大于等于1G ram的项目都需要配置Youtube Non Go版本;

  ​					2GB项目使用Maps apk，需要在google.xml中添加maps信息.

  Fail结果：google.xml已配置youtube
  				 google.xml中未配置Maps（如果使用GTS8.0R3及以后工具测试会fail）
  				 google.xml中未配置GalleryGo（如果使用GTS8.0R3及以后工具测试会fail）

- #### E+ feature

  期望结果：是E+项目，要有com.google.android.feature.GMSEXPRESS_PLUS_BUILD

​	   Fail结果：没有com.google.android.feature.GMSEXPRESS_PLUS_BUILD

- #### 重复

  期望结果：Google folder和DHS上面的图标不允许重复

  Fail结果：Drive重复，Gallery重复

- #### Google文件夹顺序

  期望结果：2020.9.1号后未获得approve项目，按照此规范执行，老项目不变与mp保持一致 Google文件夹里必须包含以下Google核心应用，强烈建议GMS					应用程序图标以字母顺序排列（具体见项目）1.Assistant Go  2.DUO（如有协议，请按照协议放置） 3. Gallery Go （如有协议，请按照协议放					置） 4. Gmail (full version) 5. Google Go 6.Maps (full version) 7.YouTube （根据GEO出货市场而定） 8.Drive  9.paly movies  10.YTmusic

  Fail结果：1.Assistant Go   2.Contacts  3.Drive  4.Gallery  5.Google Go  6.Gmail  7.play movies  8.Maps  9.YTmusic  

- #### nearby share

  期望结果：所有项目均需直接在下拉状态栏界面存在此菜单，无需编辑,不可出现在第三页

  Fail结果：下拉状态栏不存在此菜单

- #### fingerprint

  期望结果：Mobicel/LEGEND_MAX/LEGEND_MAX:11/RP1A.201005.001/*****:user/release-keys

  Fail结果：  Mobicel/LEGEND/LEGEND_MAX:11/RP1A.201005.001/32445:user/release-keys

  

- #### App pinning

  期望结果：解锁提示语、错误解锁提示语、手机实际解锁模式保持一致

  Fail结果：提示语与规范不一致 

- #### 版本号

  期望结果：Mobicel_LEGEND_MAX_DS_T_V03_20210708_MP

  Fail结果：  Mobicel_LEGEND_MAX_DS_T_V03_20210714_MP

- #### ro.product.name

  期望结果：与客户需求一致，ro.product.name= LEGEND_MAX

  Fail结果：[ro.product.name]: [LEGEND]

- #### MADA

  期望结果：客户的，mobicel

  Fail结果：MADA方为mobicell

- #### 软重启

  期望结果：所有屏幕和动画必须使用黑底白字或白色图像，以最大程度地减少用户干扰，进行软重启时不得发出任何声音或振动（排除usb插入声音）
  				   备注：对应两个命令动画流程

  ​				  1.先出现关机动画，再出现power by android(中间)，再出现客户动画

  ​				  2.先出现power by android(中间)，再出现客户动画

  Fail结果：1.先出现客户动画，再出现power by android(中间)

  ​			 	 2.出现power by android(中间)，未出现客户动画。

- #### screen timeout

  期望结果：设置—显示--屏幕超时，最小允许超时时间为15秒

  Fail结果：最小时间为30秒

- #### GPSU日期

  期望结果： Google Play System Update,是Mainline的版本，mainline资源⼀般⼀季度发布⼀次更新。2021-06-01

  Fail结果：当前Mainline的使用已经超过2个月，请尽快更新。当前Mainline版本为：2021-05-01

- #### OTG

  期望结果：硬件支持OTG，应声明OTG功能

  Fail结果：未声明OTG功能

- #### blue tooth

  期望结果：蓝牙应默认关闭

  Fail结果：蓝牙默认打开

- #### 安全patch

  期望结果：20天以内，则需要更新
  
  Fail结果：已超截止日期出49天

- #### 展锐BASE_PROJECT

  期望结果：2GRam：默认使用：sp7731e、sp9832e;如果需要兼容1G（使用9863a1h10_go_32b）;

  Fail结果：当前机器是：2GRam;使用的BASE_PROJECT是：s9863a1h10_go_32b_2g
  
- #### Assistant

  期望结果：Assistant应放置在默认主屏幕上，且不能有第三方Assistant APP

  Fail结果：Assistant放在了Goolge文件夹中

