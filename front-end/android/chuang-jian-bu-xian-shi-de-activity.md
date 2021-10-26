# 创建不显示的Activity

在 AndroidManifest.xml 文件中， 添加activity  属性

```
        <activity
            android:name=".Live"
            android:theme="@android:style/Theme.NoDisplay" />
```

此方法无效， 会报  java.lang.IllegalStateException: You need to use a Theme.AppCompat theme 的错误

所以现在无法创建不显示的activity， 曲线救国， 可以创建一个完全透明的activity

```
        <activity
            android:name=".Live"
            android:theme="@android:style/Theme.Translucent.NoTitleBar" />
```
