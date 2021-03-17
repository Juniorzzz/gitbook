# Android 8 and above require HTTPS by default, change to HTTPS or enable ClearText in the AndroidMani

```text
<?xml version="1.0" encoding="utf-8"?>
<manifest ...>
    <uses-permission android:name="android.permission.INTERNET" />
    <application
       ...
       android:usesCleartextTraffic="true"
       ...>
    </application>
</manifest>
```

