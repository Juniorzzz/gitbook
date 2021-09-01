# 加密

## Build AssetBundle

```text
 [MenuItem("Tools/BuildAB")]
static void BuildAB() 
{
    FileUtil.DeleteFileOrDirectory(Application.streamingAssetsPath);
    Directory.CreateDirectory(Application.streamingAssetsPath);
    var manifest  = BuildPipeline.BuildAssetBundles(Application.streamingAssetsPath, BuildAssetBundleOptions.ChunkBasedCompression | BuildAssetBundleOptions.ForceRebuildAssetBundle, BuildTarget.iOS);
    foreach (var name in manifest.GetAllAssetBundles())
    {
        var uniqueSalt = Encoding.UTF8.GetBytes(name);
        var data = File.ReadAllBytes(Path.Combine(Application.streamingAssetsPath, name));
        using (var myStream = new MyStream(Path.Combine(Application.streamingAssetsPath, "encypt_" + name),FileMode.Create))
        {
            myStream.Write(data, 0, data.Length);
        }
    }
    AssetDatabase.Refresh();
}
 
```

### Override FilesStream

```text
using System.IO;
 
public class MyStream : FileStream
{
 
    const byte KEY = 64;
    public MyStream(string path, FileMode mode, FileAccess access, FileShare share, int bufferSize, bool useAsync) : base(path, mode, access, share, bufferSize, useAsync)
    {
    }
    public MyStream(string path, FileMode mode) : base(path, mode)
    {
    }
    public override int Read(byte[] array, int offset, int count)
    {
        var index =  base.Read(array, offset, count);
        for (int i = 0 ; i < array.Length; i++)
        {
            array[i] ^= KEY;
        }
        return index;
    }
    public override void Write(byte[] array, int offset, int count)
    {
        for (int i = 0; i < array.Length; i ++)
        {
            array[i] ^= KEY;
        }
        base.Write(array, offset, count);
    }
}
```

### Load AssetBundle

```text
using System.IO;
using UnityEngine;
using UnityEngine.UI;
 
public class TestStream : MonoBehaviour
{
    [Header("是否启用Stream加载")]
    public bool isStream = true;
    float m_LoadTime ;
    void Start()
    {
        if (isStream)
        {
            float t = Time.realtimeSinceStartup;
            using (var fileStream = new MyStream(Application.streamingAssetsPath + "/encypt_myab.unity3d", FileMode.Open, FileAccess.Read, FileShare.None, 1024 * 4, false))
            {
                var myLoadedAssetBundle = AssetBundle.LoadFromStream(fileStream);
                m_LoadTime = Time.realtimeSinceStartup - t;
                GetComponent<Image>().sprite = myLoadedAssetBundle.LoadAsset<Sprite>("1");
                myLoadedAssetBundle.Unload(false);
            }
        }
        else
        {
            float t = Time.realtimeSinceStartup;
            var myLoadedAssetBundle = AssetBundle.LoadFromFile(Application.streamingAssetsPath + "/myab.unity3d");
            m_LoadTime = Time.realtimeSinceStartup - t;
            GetComponent<Image>().sprite = myLoadedAssetBundle.LoadAsset<Sprite>("1");
            myLoadedAssetBundle.Unload(false);
        }
    }
 
    private void OnGUI()
    {
        if (isStream)
        {
            GUILayout.Label(string.Format("<size=50>\nAssetBundle.LoadFromStream :{0} </size>", m_LoadTime));
        }
        else
        {
            GUILayout.Label(string.Format("<size=50>AssetBundle.LoadFromFile :{0} </size>", m_LoadTime));
        }
 
    }
}
```

reference  [https://www.xuanyusong.com/archives/4607](https://www.xuanyusong.com/archives/4607)

