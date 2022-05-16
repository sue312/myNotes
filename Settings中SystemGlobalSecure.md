<h1><center>Settings中System/Global/Secure</center></h1>

## 一、 概述

常用于判断是否是首次打开，Settings中不论是Global、System还是Secure的数据都由键值对组成的。这些属性以Settings为入口，通过SettingsProvider来进行的。在M(Android5.0)版本之前，SettingsProvider中系统设置是存储在settings.db数据库中；但是在L(Android6.0)之后，SettingsProvider中系统设置改为由xml存储在data分区。

## 二、Settings的三种分类

1. **Global**：所有的偏好设置对系统的所有用户公开，第三方APP有读没有写的权限；
2. **System**：包含各种各样的用户偏好系统设置，第三方APP有读没有写的权限；
3. **Secure**：安全性的用户偏好系统设置，第三方APP有读没有写的权限。

## 三、在Settings中自定义一个值

alps/frameworks/base/core/java/android/provider/Settings.java中有三个内部类：Global、Secure、System。

把你要添加的值添加进对应的内部类中。要加@hide。

```
/**
* Whether the current user has been confirmed the disclaimer of vowifi(0 = false, 1 = true)
* @hide
*/
public static final String WFC_CONFIRM_COMPLETE = "wfc_confirm_complete";
```

## 四、常用方法

```
Settings.Secure.putInt(getContentResolver(), Settings.Secure.WFC_CONFIRM_COMPLETE, 1);
```

> 设置WFC_CONFIRM_COMPLETE的值为1

```
Settings.Secure.getInt(context.getContentResolver(), Settings.Secure.WFC_CONFIRM_COMPLETE, 0);
```

> 获取WFC_CONFIRM_COMPLETE的值，如果还未定义则为0

```
adb shell settings list secure
```

> 查看所有Settings.Secure的值，secure可换为System和Global

```
Orange_Hapi_52:/ # cd data/system/users/0                                     
Orange_Hapi_52:/data/system/users/0 # ls -l
total 63
-rw------- 1 system system   340 2020-01-01 00:00 appwidgets.xml
-rw-rw---- 1 system system 12606 2020-01-01 00:53 package-restrictions.xml
drwxrwx--x 2 system system  3488 2020-01-01 00:00 registered_services
-rw------- 1 system system 14338 2020-01-01 00:00 runtime-permissions.xml
-rw------- 1 system system  9611 2020-01-01 00:01 settings_global.xml
-rw------- 1 system system  6615 2020-01-01 00:02 settings_secure.xml
-rw------- 1 system system  5949 2020-01-01 00:53 settings_system.xml
-rw------- 1 system system   176 2020-01-01 00:00 wallpaper_info.xml
```

