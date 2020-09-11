# 定义 AIDL 接口

您必须在 `.aidl` 文件中使用 Java 编程语言的语法定义 AIDL 接口，然后将其保存至应用的源代码（在 `src/` 目录中）内，这类应用会托管服务或与服务进行绑定。

在构建每个包含 `.aidl` 文件的应用时，Android SDK 工具会生成基于该 `.aidl` 文件的 [`IBinder`](https://developer.android.com/reference/android/os/IBinder?hl=zh-cn) 接口，并将其保存到项目的 `gen/` 目录中。服务必须视情况实现 [`IBinder`](https://developer.android.com/reference/android/os/IBinder?hl=zh-cn) 接口。然后，客户端应用便可绑定到该服务，并调用 [`IBinder`](https://developer.android.com/reference/android/os/IBinder?hl=zh-cn) 中的方法来执行 IPC。

如要使用 AIDL 创建绑定服务，请执行以下步骤：

1. 创建 .aidl 文件

   此文件定义带有方法签名的编程接口。

2. 实现接口

   Android SDK 工具会基于您的 `.aidl` 文件，使用 Java 编程语言生成接口。此接口拥有一个名为 `Stub` 的内部抽象类，用于扩展 `Binder` 类并实现 AIDL 接口中的方法。您必须扩展 `Stub` 类并实现这些方法。

3. 向客户端公开接口

   实现 `Service` 并重写 `onBind()`，从而返回 `Stub` 类的实现。

{% hint style="info" %}
 **注意：**如果您在首次发布 AIDL 接口后对其进行更改，则每次更改必须保持向后兼容性，以免中断其他应用使用您的服务。换言之，由于只有在将您的 `.aidl` 文件复制到其他应用后，才能使其访问服务接口，因而您必须保留对原始接口的支持。
{% endhint %}

