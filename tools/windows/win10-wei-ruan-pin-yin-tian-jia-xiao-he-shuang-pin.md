# Win10 微软拼音添加小鹤双拼

首先打开注册表，找到这个路径

```
\HKEY_CURRENT_USER\Software\Microsoft\InputMethod\Settings\CHS
```

然后新建一个名为 `UserDefinedDoublePinyinScheme0` 的字符串值，数值数据为

```
小鹤双拼*2*^*iuvdjhcwfg^xmlnpbksqszxkrltvyovt
```

![](<../../.gitbook/assets/image (7).png>)
