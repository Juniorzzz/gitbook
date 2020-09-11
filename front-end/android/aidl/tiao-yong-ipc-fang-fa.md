# 调用 IPC 方法

如要调用通过 AIDL 定义的远程接口，调用类必须执行以下步骤：

1. 在项目的 `src/` 目录中加入 `.aidl` 文件。
2. 声明一个 `IBinder` 接口实例（基于 AIDL 生成）。
3. 实现 `ServiceConnection`。
4. 调用 `Context.bindService()`，从而传入您的 `ServiceConnection` 实现。
5. 在 `onServiceConnected()` 实现中，您将收到一个 `IBinder` 实例（名为 `service`）。调用 _`YourInterfaceName`_`.Stub.asInterface((IBinder)`_`service`_`)`，以将返回的参数转换为 _YourInterface_ 类型。
6. 调用您在接口上定义的方法。您应始终捕获 `DeadObjectException` 异常，系统会在连接中断时抛出此异常。您还应捕获 `SecurityException` 异常，当 IPC 方法调用中两个进程的 AIDL 定义发生冲突时，系统会抛出此异常。
7. 如要断开连接，请使用您的接口实例调用 `Context.unbindService()`。

有关调用 IPC 服务的几点说明：

* 对象是跨进程计数的引用。
* 您可以方法参数的形式发送匿名对象。

以下示例代码摘自 ApiDemos 项目的远程服务示例，展示如何调用 AIDL 创建的服务。

{% tabs %}
{% tab title="KOTLIN" %}
```text
private const val BUMP_MSG = 1

class Binding : Activity() {

    /** The primary interface we will be calling on the service.  */
    private var mService: IRemoteService? = null

    /** Another interface we use on the service.  */
    internal var secondaryService: ISecondary? = null

    private lateinit var killButton: Button
    private lateinit var callbackText: TextView
    private lateinit var handler: InternalHandler

    private var isBound: Boolean = false

    /**
     * Class for interacting with the main interface of the service.
     */
    private val mConnection = object : ServiceConnection {

        override fun onServiceConnected(className: ComponentName, service: IBinder) {
            // This is called when the connection with the service has been
            // established, giving us the service object we can use to
            // interact with the service.  We are communicating with our
            // service through an IDL interface, so get a client-side
            // representation of that from the raw service object.
            mService = IRemoteService.Stub.asInterface(service)
            killButton.isEnabled = true
            callbackText.text = "Attached."

            // We want to monitor the service for as long as we are
            // connected to it.
            try {
                mService?.registerCallback(mCallback)
            } catch (e: RemoteException) {
                // In this case the service has crashed before we could even
                // do anything with it; we can count on soon being
                // disconnected (and then reconnected if it can be restarted)
                // so there is no need to do anything here.
            }

            // As part of the sample, tell the user what happened.
            Toast.makeText(
                    this@Binding,
                    R.string.remote_service_connected,
                    Toast.LENGTH_SHORT
            ).show()
        }

        override fun onServiceDisconnected(className: ComponentName) {
            // This is called when the connection with the service has been
            // unexpectedly disconnected -- that is, its process crashed.
            mService = null
            killButton.isEnabled = false
            callbackText.text = "Disconnected."

            // As part of the sample, tell the user what happened.
            Toast.makeText(
                    this@Binding,
                    R.string.remote_service_disconnected,
                    Toast.LENGTH_SHORT
            ).show()
        }
    }

    /**
     * Class for interacting with the secondary interface of the service.
     */
    private val secondaryConnection = object : ServiceConnection {

        override fun onServiceConnected(className: ComponentName, service: IBinder) {
            // Connecting to a secondary interface is the same as any
            // other interface.
            secondaryService = ISecondary.Stub.asInterface(service)
            killButton.isEnabled = true
        }

        override fun onServiceDisconnected(className: ComponentName) {
            secondaryService = null
            killButton.isEnabled = false
        }
    }

    private val mBindListener = View.OnClickListener {
        // Establish a couple connections with the service, binding
        // by interface names.  This allows other applications to be
        // installed that replace the remote service by implementing
        // the same interface.
        val intent = Intent(this@Binding, RemoteService::class.java)
        intent.action = IRemoteService::class.java.name
        bindService(intent, mConnection, Context.BIND_AUTO_CREATE)
        intent.action = ISecondary::class.java.name
        bindService(intent, secondaryConnection, Context.BIND_AUTO_CREATE)
        isBound = true
        callbackText.text = "Binding."
    }

    private val unbindListener = View.OnClickListener {
        if (isBound) {
            // If we have received the service, and hence registered with
            // it, then now is the time to unregister.
            try {
                mService?.unregisterCallback(mCallback)
            } catch (e: RemoteException) {
                // There is nothing special we need to do if the service
                // has crashed.
            }

            // Detach our existing connection.
            unbindService(mConnection)
            unbindService(secondaryConnection)
            killButton.isEnabled = false
            isBound = false
            callbackText.text = "Unbinding."
        }
    }

    private val killListener = View.OnClickListener {
        // To kill the process hosting our service, we need to know its
        // PID.  Conveniently our service has a call that will return
        // to us that information.
        try {
            secondaryService?.pid?.also { pid ->
                // Note that, though this API allows us to request to
                // kill any process based on its PID, the kernel will
                // still impose standard restrictions on which PIDs you
                // are actually able to kill.  Typically this means only
                // the process running your application and any additional
                // processes created by that app as shown here; packages
                // sharing a common UID will also be able to kill each
                // other's processes.
                Process.killProcess(pid)
                callbackText.text = "Killed service process."
            }
        } catch (ex: RemoteException) {
            // Recover gracefully from the process hosting the
            // server dying.
            // Just for purposes of the sample, put up a notification.
            Toast.makeText(this@Binding, R.string.remote_call_failed, Toast.LENGTH_SHORT).show()
        }
    }

    // ----------------------------------------------------------------------
    // Code showing how to deal with callbacks.
    // ----------------------------------------------------------------------

    /**
     * This implementation is used to receive callbacks from the remote
     * service.
     */
    private val mCallback = object : IRemoteServiceCallback.Stub() {
        /**
         * This is called by the remote service regularly to tell us about
         * new values.  Note that IPC calls are dispatched through a thread
         * pool running in each process, so the code executing here will
         * NOT be running in our main thread like most other things -- so,
         * to update the UI, we need to use a Handler to hop over there.
         */
        override fun valueChanged(value: Int) {
            handler.sendMessage(handler.obtainMessage(BUMP_MSG, value, 0))
        }
    }

    /**
     * Standard initialization of this activity.  Set up the UI, then wait
     * for the user to poke it before doing anything.
     */
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContentView(R.layout.remote_service_binding)

        // Watch for button clicks.
        var button: Button = findViewById(R.id.bind)
        button.setOnClickListener(mBindListener)
        button = findViewById(R.id.unbind)
        button.setOnClickListener(unbindListener)
        killButton = findViewById(R.id.kill)
        killButton.setOnClickListener(killListener)
        killButton.isEnabled = false

        callbackText = findViewById(R.id.callback)
        callbackText.text = "Not attached."
        handler = InternalHandler(callbackText)
    }

    private class InternalHandler(
            textView: TextView,
            private val weakTextView: WeakReference<TextView> = WeakReference(textView)
    ) : Handler() {
        override fun handleMessage(msg: Message) {
            when (msg.what) {
                BUMP_MSG -> weakTextView.get()?.text = "Received from service: ${msg.arg1}"
                else -> super.handleMessage(msg)
            }
        }
    }
}
```
{% endtab %}

{% tab title="JAVA" %}
```text
public static class Binding extends Activity {
    /** The primary interface we will be calling on the service. */
    IRemoteService mService = null;
    /** Another interface we use on the service. */
    ISecondary secondaryService = null;

    Button killButton;
    TextView callbackText;

    private InternalHandler handler;
    private boolean isBound;

    /**
     * Standard initialization of this activity.  Set up the UI, then wait
     * for the user to poke it before doing anything.
     */
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        setContentView(R.layout.remote_service_binding);

        // Watch for button clicks.
        Button button = (Button)findViewById(R.id.bind);
        button.setOnClickListener(mBindListener);
        button = (Button)findViewById(R.id.unbind);
        button.setOnClickListener(unbindListener);
        killButton = (Button)findViewById(R.id.kill);
        killButton.setOnClickListener(killListener);
        killButton.setEnabled(false);

        callbackText = (TextView)findViewById(R.id.callback);
        callbackText.setText("Not attached.");
        handler = new InternalHandler(callbackText);
    }

    /**
     * Class for interacting with the main interface of the service.
     */
    private ServiceConnection mConnection = new ServiceConnection() {
        public void onServiceConnected(ComponentName className,
                IBinder service) {
            // This is called when the connection with the service has been
            // established, giving us the service object we can use to
            // interact with the service.  We are communicating with our
            // service through an IDL interface, so get a client-side
            // representation of that from the raw service object.
            mService = IRemoteService.Stub.asInterface(service);
            killButton.setEnabled(true);
            callbackText.setText("Attached.");

            // We want to monitor the service for as long as we are
            // connected to it.
            try {
                mService.registerCallback(mCallback);
            } catch (RemoteException e) {
                // In this case the service has crashed before we could even
                // do anything with it; we can count on soon being
                // disconnected (and then reconnected if it can be restarted)
                // so there is no need to do anything here.
            }

            // As part of the sample, tell the user what happened.
            Toast.makeText(Binding.this, R.string.remote_service_connected,
                    Toast.LENGTH_SHORT).show();
        }

        public void onServiceDisconnected(ComponentName className) {
            // This is called when the connection with the service has been
            // unexpectedly disconnected -- that is, its process crashed.
            mService = null;
            killButton.setEnabled(false);
            callbackText.setText("Disconnected.");

            // As part of the sample, tell the user what happened.
            Toast.makeText(Binding.this, R.string.remote_service_disconnected,
                    Toast.LENGTH_SHORT).show();
        }
    };

    /**
     * Class for interacting with the secondary interface of the service.
     */
    private ServiceConnection secondaryConnection = new ServiceConnection() {
        public void onServiceConnected(ComponentName className,
                IBinder service) {
            // Connecting to a secondary interface is the same as any
            // other interface.
            secondaryService = ISecondary.Stub.asInterface(service);
            killButton.setEnabled(true);
        }

        public void onServiceDisconnected(ComponentName className) {
            secondaryService = null;
            killButton.setEnabled(false);
        }
    };

    private OnClickListener mBindListener = new OnClickListener() {
        public void onClick(View v) {
            // Establish a couple connections with the service, binding
            // by interface names.  This allows other applications to be
            // installed that replace the remote service by implementing
            // the same interface.
            Intent intent = new Intent(Binding.this, RemoteService.class);
            intent.setAction(IRemoteService.class.getName());
            bindService(intent, mConnection, Context.BIND_AUTO_CREATE);
            intent.setAction(ISecondary.class.getName());
            bindService(intent, secondaryConnection, Context.BIND_AUTO_CREATE);
            isBound = true;
            callbackText.setText("Binding.");
        }
    };

    private OnClickListener unbindListener = new OnClickListener() {
        public void onClick(View v) {
            if (isBound) {
                // If we have received the service, and hence registered with
                // it, then now is the time to unregister.
                if (mService != null) {
                    try {
                        mService.unregisterCallback(mCallback);
                    } catch (RemoteException e) {
                        // There is nothing special we need to do if the service
                        // has crashed.
                    }
                }

                // Detach our existing connection.
                unbindService(mConnection);
                unbindService(secondaryConnection);
                killButton.setEnabled(false);
                isBound = false;
                callbackText.setText("Unbinding.");
            }
        }
    };

    private OnClickListener killListener = new OnClickListener() {
        public void onClick(View v) {
            // To kill the process hosting our service, we need to know its
            // PID.  Conveniently our service has a call that will return
            // to us that information.
            if (secondaryService != null) {
                try {
                    int pid = secondaryService.getPid();
                    // Note that, though this API allows us to request to
                    // kill any process based on its PID, the kernel will
                    // still impose standard restrictions on which PIDs you
                    // are actually able to kill.  Typically this means only
                    // the process running your application and any additional
                    // processes created by that app as shown here; packages
                    // sharing a common UID will also be able to kill each
                    // other's processes.
                    Process.killProcess(pid);
                    callbackText.setText("Killed service process.");
                } catch (RemoteException ex) {
                    // Recover gracefully from the process hosting the
                    // server dying.
                    // Just for purposes of the sample, put up a notification.
                    Toast.makeText(Binding.this,
                            R.string.remote_call_failed,
                            Toast.LENGTH_SHORT).show();
                }
            }
        }
    };

    // ----------------------------------------------------------------------
    // Code showing how to deal with callbacks.
    // ----------------------------------------------------------------------

    /**
     * This implementation is used to receive callbacks from the remote
     * service.
     */
    private IRemoteServiceCallback mCallback = new IRemoteServiceCallback.Stub() {
        /**
         * This is called by the remote service regularly to tell us about
         * new values.  Note that IPC calls are dispatched through a thread
         * pool running in each process, so the code executing here will
         * NOT be running in our main thread like most other things -- so,
         * to update the UI, we need to use a Handler to hop over there.
         */
        public void valueChanged(int value) {
            handler.sendMessage(handler.obtainMessage(BUMP_MSG, value, 0));
        }
    };

    private static final int BUMP_MSG = 1;

    private static class InternalHandler extends Handler {
        private final WeakReference<TextView> weakTextView;

        InternalHandler(TextView textView) {
            weakTextView = new WeakReference<>(textView);
        }

        @Override
        public void handleMessage(Message msg) {
            switch (msg.what) {
                case BUMP_MSG:
                    TextView textView = weakTextView.get();
                    if (textView != null) {
                        textView.setText("Received from service: " + msg.arg1);
                    }
                    break;
                default:
                    super.handleMessage(msg);
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

