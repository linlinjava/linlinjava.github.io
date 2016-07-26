---
layout: page
title: Android Tips
category: 
description: Android Tips
---

# WiFi Adb

采用无线Adb调试方式, 无需USB连接.

1. Eclipse的timeout需要设置较大值, 否则安装应用失败

```
Window -> Preferences -> Android -> DDMS -> ADB Connection Timeout (ms)
```

http://stackoverflow.com/questions/4775603/android-error-failed-to-install-apk-on-device-timeout

# 反编译

1. apktool

```
./apktool d xxx.apk
```

2. dex2jar

```
unzip xxx.apk
mv classes.dex {$dex2jar_dir}
./d2j-dex2jar.sh classes.dex
```

3. jd-gui
