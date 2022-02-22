# Page 1

开启系统代理之后，Microsoft Store 等 UWP 应用无法联网。

解决方法：

以管理员方式打开 Powershell\
输入 foreach ($n in (get-appxpackage).packagefamilyname) {checknetisolation loopbackexempt -a -n="$n"}\
等待执行完毕
