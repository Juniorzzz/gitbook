# 带软件包参数（包含 Parcelable 类型）的方法

如果您的 AIDL 接口包含接收软件包作为参数（预计包含 Parcelable 类型）的方法，则在尝试从软件包读取之前，请务必通过调用 [`Bundle.setClassLoader(ClassLoader)`](https://developer.android.com/reference/android/os/Bundle?hl=en#setClassLoader%28java.lang.ClassLoader%29) 设置软件包的类加载器。否则，即使您在应用中正确定义 Parcelable 类型，也会遇到 [`ClassNotFoundException`](https://developer.android.com/reference/java/lang/ClassNotFoundException?hl=zh-cn)。例如，

如果您有 `.aidl` 文件：

```text
// IRectInsideBundle.aidl
package com.example.android;

/** Example service interface */
interface IRectInsideBundle {
    /** Rect parcelable is stored in the bundle with key "rect" */
    void saveRect(in Bundle bundle);
}
```

如下方实现所示，在读取 `Rect` 之前，`ClassLoader` 已在 `Bundle` 中完成显式设置

{% tabs %}
{% tab title="KOTLIN" %}
```text
private val binder = object : IRectInsideBundle.Stub() {
    override fun saveRect(bundle: Bundle) {
      bundle.classLoader = classLoader
      val rect = bundle.getParcelable<Rect>("rect")
      process(rect) // Do more with the parcelable
    }
}
```
{% endtab %}

{% tab title="JAVA" %}
```text
private final IRectInsideBundle.Stub binder = new IRectInsideBundle.Stub() {
    public void saveRect(Bundle bundle){
        bundle.setClassLoader(getClass().getClassLoader());
        Rect rect = bundle.getParcelable("rect");
        process(rect); // Do more with the parcelable.
    }
};
```
{% endtab %}
{% endtabs %}

