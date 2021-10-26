# 通过 IPC 传递对象

您可以通过 IPC 接口，将某个类从一个进程发送至另一个进程。不过，您必须确保 IPC 通道的另一端可使用该类的代码，并且该类必须支持 [`Parcelable`](https://developer.android.com/reference/android/os/Parcelable?hl=zh-cn) 接口。支持 [`Parcelable`](https://developer.android.com/reference/android/os/Parcelable?hl=zh-cn) 接口很重要，因为 Android 系统能通过该接口将对象分解成可编组至各进程的原语。

如要创建支持 [`Parcelable`](https://developer.android.com/reference/android/os/Parcelable?hl=zh-cn) 协议的类，您必须执行以下操作：

1. 让您的类实现 [`Parcelable`](https://developer.android.com/reference/android/os/Parcelable?hl=zh-cn) 接口。
2. 实现 [`writeToParcel`](https://developer.android.com/reference/android/os/Parcelable?hl=zh-cn#writeToParcel%28android.os.Parcel,%20int%29)，它会获取对象的当前状态并将其写入 [`Parcel`](https://developer.android.com/reference/android/os/Parcel?hl=zh-cn)。
3. 为您的类添加 `CREATOR` 静态字段，该字段是实现 [`Parcelable.Creator`](https://developer.android.com/reference/android/os/Parcelable.Creator?hl=zh-cn) 接口的对象。
4.  最后，创建声明 Parcelable 类的 `.aidl` 文件（遵照下文 `Rect.aidl` 文件所示步骤）。

    如果您使用的是自定义编译进程，_请勿_在您的构建中添加 `.aidl` 文件。此 `.aidl` 文件与 C 语言中的头文件类似，并未经过编译。

AIDL 会在其生成的代码中使用这些方法和字段，以对您的对象进行编组和解编。

例如，下方的 `Rect.aidl` 文件可创建 Parcelable 类型的 `Rect` 类：

```
package android.graphics;

// Declare Rect so AIDL can find it and knows that it implements
// the parcelable protocol.
parcelable Rect;
```

以下示例展示 [`Rect`](https://developer.android.com/reference/android/graphics/Rect?hl=zh-cn) 类如何实现 [`Parcelable`](https://developer.android.com/reference/android/os/Parcelable?hl=zh-cn) 协议。

{% tabs %}
{% tab title="KOTLIN" %}
```
import android.os.Parcel
import android.os.Parcelable

class Rect() : Parcelable {
    var left: Int = 0
    var top: Int = 0
    var right: Int = 0
    var bottom: Int = 0

    companion object CREATOR : Parcelable.Creator<Rect> {
        override fun createFromParcel(parcel: Parcel): Rect {
            return Rect(parcel)
        }

        override fun newArray(size: Int): Array<Rect> {
            return Array(size) { Rect() }
        }
    }

    private constructor(inParcel: Parcel) : this() {
        readFromParcel(inParcel)
    }

    override fun writeToParcel(outParcel: Parcel, flags: Int) {
        outParcel.writeInt(left)
        outParcel.writeInt(top)
        outParcel.writeInt(right)
        outParcel.writeInt(bottom)
    }

    private fun readFromParcel(inParcel: Parcel) {
        left = inParcel.readInt()
        top = inParcel.readInt()
        right = inParcel.readInt()
        bottom = inParcel.readInt()
    }

    override fun describeContents(): Int {
        return 0
    }
}
```
{% endtab %}

{% tab title="JAVA" %}
```
import android.os.Parcel;
import android.os.Parcelable;

public final class Rect implements Parcelable {
    public int left;
    public int top;
    public int right;
    public int bottom;

    public static final Parcelable.Creator<Rect> CREATOR = new Parcelable.Creator<Rect>() {
        public Rect createFromParcel(Parcel in) {
            return new Rect(in);
        }

        public Rect[] newArray(int size) {
            return new Rect[size];
        }
    };

    public Rect() {
    }

    private Rect(Parcel in) {
        readFromParcel(in);
    }

    public void writeToParcel(Parcel out, int flags) {
        out.writeInt(left);
        out.writeInt(top);
        out.writeInt(right);
        out.writeInt(bottom);
    }

    public void readFromParcel(Parcel in) {
        left = in.readInt();
        top = in.readInt();
        right = in.readInt();
        bottom = in.readInt();
    }

    public int describeContents() {
        return 0;
    }
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**警告：**请勿忘记从其他进程中接收数据的安全问题。在本例中，`Rect` 从 `Parcel` 读取四个数字，但您需确保：无论调用方目的为何，这些数字均在可接受的值范围内。如需详细了解如何防止应用受到恶意软件侵害、保证应用安全，请参阅[安全与权限](https://developer.android.com/guide/topics/security/security?hl=zh-cn)。
{% endhint %}
