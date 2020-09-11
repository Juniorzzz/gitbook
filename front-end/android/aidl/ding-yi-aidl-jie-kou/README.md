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

> **注意：**只有在需要不同应用的客户端通过 IPC 方式访问服务，并且希望在服务中进行多线程处理时，您才有必要使用 AIDL。如果您无需跨不同应用执行并发 IPC，则应通过实现 Binder 来创建接口；或者，如果您想执行 IPC，但_不_需要处理多线程，请使用 Messenger 来实现接口。无论如何，在实现 AIDL 之前，请您务必理解绑定服务。



