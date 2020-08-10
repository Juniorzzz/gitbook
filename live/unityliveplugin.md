# UnityLivePlugin

受 [https://www.gero.pub/2017/10/26/how-to-use-ijkplayer-in-unity/](https://www.gero.pub/2017/10/26/how-to-use-ijkplayer-in-unity/) 启发， 搜索了好久没有发现 [PLDroidMediaStreaming](https://github.com/pili-engineering/PLDroidMediaStreaming) 的移植版本， 所以自己移植了一版， 目前测试正常，待后续测试

```text
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package com.EasyMovieTexture;

import android.annotation.TargetApi;
import android.app.Activity;
import android.content.res.AssetFileDescriptor;
import android.content.res.AssetManager;
import android.graphics.SurfaceTexture;
import android.graphics.SurfaceTexture.OnFrameAvailableListener;
import android.opengl.GLES20;
import android.util.Log;
import android.view.Surface;

import com.EasyMovieTexture.utils.Utils;
import com.android.vending.expansion.zipfile.ZipResourceFile;
import com.android.vending.expansion.zipfile.ZipResourceFile.ZipEntryRO;
import com.pili.pldroid.player.AVOptions;
import com.pili.pldroid.player.PLMediaPlayer;
import com.pili.pldroid.player.PLOnAudioFrameListener;
import com.pili.pldroid.player.PLOnBufferingUpdateListener;
import com.pili.pldroid.player.PLOnCompletionListener;
import com.pili.pldroid.player.PLOnErrorListener;
import com.pili.pldroid.player.PLOnInfoListener;
import com.pili.pldroid.player.PLOnPreparedListener;
import com.pili.pldroid.player.PLOnVideoFrameListener;
import com.pili.pldroid.player.PLOnVideoSizeChangedListener;

import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;

public class EasyMovieTexture implements OnFrameAvailableListener {
    private Activity m_UnityActivity = null;
    private PLMediaPlayer mMediaPlayer = null;
    private AVOptions mAVOptions;
    private int m_iUnityTextureID = -1;
    private int m_iSurfaceTextureID = -1;
    private SurfaceTexture m_SurfaceTexture = null;
    private Surface m_Surface = null;
    private int m_iCurrentSeekPercent = 0;
    private int m_iCurrentSeekPosition = 0;
    public int m_iNativeMgrID;
    private String m_strFileName;
    private int m_iErrorCode;
    private int m_iErrorCodeExtra;
    private boolean m_bRockchip = true;
    private boolean m_bSplitOBB = false;
    private String m_strOBBName;
    public boolean m_bUpdate = false;
    public static ArrayList<EasyMovieTexture> m_objCtrl = new ArrayList();
    private static final int GL_TEXTURE_EXTERNAL_OES = 36197;
    EasyMovieTexture.MEDIAPLAYER_STATE m_iCurrentState;

    static {
        System.loadLibrary("BlueDoveMediaRender");
    }

    public EasyMovieTexture() {
        this.m_iCurrentState = EasyMovieTexture.MEDIAPLAYER_STATE.NOT_READY;
    }

    public static EasyMovieTexture GetObject(int iID) {
        for (int i = 0; i < m_objCtrl.size(); ++i) {
            if (((EasyMovieTexture) m_objCtrl.get(i)).m_iNativeMgrID == iID) {
                return (EasyMovieTexture) m_objCtrl.get(i);
            }
        }

        return null;
    }

    public native int InitNDK(Object var1);

    public native void SetAssetManager(AssetManager var1);

    public native int InitApplication();

    public native void QuitApplication();

    public native void SetWindowSize(int var1, int var2, int var3, boolean var4);

    public native void RenderScene(float[] var1, int var2, int var3);

    public native void SetManagerID(int var1);

    public native int GetManagerID();

    public native int InitExtTexture();

    public native void SetUnityTextureID(int var1);

    public void Destroy() {
        Log.i(Utils.LOG_TAG, "EasyMovieTexture:Destroy");

        if (this.m_iSurfaceTextureID != -1) {
            int[] textures = new int[]{this.m_iSurfaceTextureID};
            GLES20.glDeleteTextures(1, textures, 0);
            this.m_iSurfaceTextureID = -1;
        }

        this.SetManagerID(this.m_iNativeMgrID);
        this.QuitApplication();
        m_objCtrl.remove(this);
    }

    public void UnLoad() {
        Log.i(Utils.LOG_TAG, "EasyMovieTexture:UnLoad");

        if (this.mMediaPlayer != null) {
            if (this.m_iCurrentState != EasyMovieTexture.MEDIAPLAYER_STATE.NOT_READY) {
                try {
                    this.mMediaPlayer.stop();
                    this.mMediaPlayer.release();
                } catch (SecurityException var4) {
                    var4.printStackTrace();
                } catch (IllegalStateException var5) {
                    var5.printStackTrace();
                }

                this.mMediaPlayer = null;
            } else {
                try {
                    this.mMediaPlayer.release();
                } catch (SecurityException var2) {
                    var2.printStackTrace();
                } catch (IllegalStateException var3) {
                    var3.printStackTrace();
                }

                this.mMediaPlayer = null;
            }

            if (this.m_Surface != null) {
                this.m_Surface.release();
                this.m_Surface = null;
            }

            if (this.m_SurfaceTexture != null) {
                this.m_SurfaceTexture.release();
                this.m_SurfaceTexture = null;
            }

            if (this.m_iSurfaceTextureID != -1) {
                int[] textures = new int[]{this.m_iSurfaceTextureID};
                GLES20.glDeleteTextures(1, textures, 0);
                this.m_iSurfaceTextureID = -1;
            }
        }

        this.m_iCurrentState = EasyMovieTexture.MEDIAPLAYER_STATE.NOT_READY;
    }

    private void InitOptions() {
        Log.i(Utils.LOG_TAG, "EasyMovieTexture:InitOptions");

        mAVOptions = new AVOptions();
        mAVOptions.setInteger(AVOptions.KEY_CACHE_BUFFER_DURATION, 0);
        mAVOptions.setInteger(AVOptions.KEY_PREPARE_TIMEOUT, 10 * 1000);
        // 1 -> hw codec enable, 0 -> disable [recommended]
        mAVOptions.setInteger(AVOptions.KEY_MEDIACODEC, AVOptions.MEDIA_CODEC_AUTO);
        mAVOptions.setInteger(AVOptions.KEY_LIVE_STREAMING, 1);
        mAVOptions.setInteger(AVOptions.KEY_LOG_LEVEL, 5);
        mAVOptions.setInteger(AVOptions.KEY_VIDEO_RENDER_EXTERNAL, 1);
        mAVOptions.setInteger(AVOptions.KEY_AUDIO_RENDER_EXTERNAL, 1);
    }

    public boolean Load() throws SecurityException, IllegalStateException, IOException {

        Log.i(Utils.LOG_TAG, "EasyMovieTexture:Load");

        this.UnLoad();
        this.m_iCurrentState = EasyMovieTexture.MEDIAPLAYER_STATE.NOT_READY;

        InitOptions();
        mMediaPlayer = new PLMediaPlayer(this.m_UnityActivity.getApplicationContext(), mAVOptions);


        this.m_bUpdate = false;
        if (this.m_strFileName.contains("file://")) {
            File sourceFile = new File(this.m_strFileName.replace("file://", ""));
            if (sourceFile.exists()) {
                FileInputStream fs = new FileInputStream(sourceFile);
//                mMediaPlayer.setDataSource(fs.getFD());
                fs.close();
            }
        } else {
            int i;
            if (this.m_strFileName.contains("://")) {

                try {
                    mMediaPlayer.setDataSource(m_strFileName);
                } catch (UnsatisfiedLinkError e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }

            } else if (this.m_bSplitOBB) {
                try {
                    ZipResourceFile expansionFile = new ZipResourceFile(this.m_strOBBName);
                    Log.e("unity", this.m_strOBBName + " " + this.m_strFileName);
                    AssetFileDescriptor afd = expansionFile.getAssetFileDescriptor("assets/" + this.m_strFileName);
                    ZipEntryRO[] data = expansionFile.getAllEntries();

                    for (i = 0; i < data.length; ++i) {
                        Log.e("unity", data[i].mFileName);
                    }

                    Log.e("unity", afd + " ");
//                    this.m_MediaPlayer.setDataSource(afd.getFileDescriptor(), afd.getStartOffset(), afd.getLength());
                } catch (IOException var10) {
                    this.m_iCurrentState = EasyMovieTexture.MEDIAPLAYER_STATE.ERROR;
                    var10.printStackTrace();
                    return false;
                }
            } else {
                try {
                    AssetFileDescriptor afd = this.m_UnityActivity.getAssets().openFd(this.m_strFileName);
//                    this.m_MediaPlayer.setDataSource(afd.getFileDescriptor(), afd.getStartOffset(), afd.getLength());
                    afd.close();
                } catch (IOException var8) {
                    Log.e("Unity", "Error m_MediaPlayer.setDataSource() : " + this.m_strFileName);
                    var8.printStackTrace();
                    this.m_iCurrentState = EasyMovieTexture.MEDIAPLAYER_STATE.ERROR;
                    return false;
                }
            }
        }

        if (this.m_iSurfaceTextureID == -1) {
            this.m_iSurfaceTextureID = this.InitExtTexture();
        }

        this.m_SurfaceTexture = new SurfaceTexture(this.m_iSurfaceTextureID);
        this.m_SurfaceTexture.setOnFrameAvailableListener(this);
        this.m_Surface = new Surface(this.m_SurfaceTexture);

        mMediaPlayer.setSurface(m_Surface);
        mMediaPlayer.setIOCacheSize(1024*10);
        mMediaPlayer.setOnPreparedListener(mOnPreparedListener);
        mMediaPlayer.setOnCompletionListener(mOnCompletionListener);
        mMediaPlayer.setOnErrorListener(mOnErrorListener);
        mMediaPlayer.setOnBufferingUpdateListener(mOnBufferingUpdateListener);
        mMediaPlayer.prepareAsync();

        return true;
    }

    public synchronized void onFrameAvailable(SurfaceTexture surface) {
        this.m_bUpdate = true;
    }

    @TargetApi(23)
    public void SetSpeed(float fSpeed) {
        Log.i(Utils.LOG_TAG, "EasyMovieTexture:SetSpeed");

//        this.mMediaPlayer.setPlaybackParams(this.mMediaPlayer.getPlaybackParams().setSpeed(fSpeed));
    }

    public void UpdateVideoTexture() {
        if (this.m_bUpdate) {
            if (this.mMediaPlayer != null && (this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.PLAYING || this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.PAUSED)) {
                this.SetManagerID(this.m_iNativeMgrID);
                boolean[] abValue = new boolean[1];
                GLES20.glGetBooleanv(2929, abValue, 0);
                GLES20.glDisable(2929);
                this.m_SurfaceTexture.updateTexImage();
                float[] mMat = new float[16];
                this.m_SurfaceTexture.getTransformMatrix(mMat);
                this.RenderScene(mMat, this.m_iSurfaceTextureID, this.m_iUnityTextureID);
                if (abValue[0]) {
                    GLES20.glEnable(2929);
                }

                Object var3 = null;
            }

        }
    }

    public void SetRockchip(boolean bValue) {
        this.m_bRockchip = bValue;
    }

    public void SetLooping(boolean bLoop) {
        if (this.mMediaPlayer != null) {
            this.mMediaPlayer.setLooping(bLoop);
        }

    }

    public void SetVolume(float fVolume) {
        if (this.mMediaPlayer != null) {
            this.mMediaPlayer.setVolume(fVolume, fVolume);
        }

    }

    public void SetSeekPosition(int iSeek) {
        if (this.mMediaPlayer != null && (this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.READY || this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.PLAYING || this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.PAUSED)) {
            this.mMediaPlayer.seekTo(iSeek);
        }
    }

    public int GetSeekPosition() {
        if (this.mMediaPlayer != null && (this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.READY || this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.PLAYING || this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.PAUSED)) {
            try {
                this.m_iCurrentSeekPosition = (int) this.mMediaPlayer.getCurrentPosition();
            } catch (SecurityException var2) {
                var2.printStackTrace();
            } catch (IllegalStateException var3) {
                var3.printStackTrace();
            }
        }

        return this.m_iCurrentSeekPosition;
    }

    public int GetCurrentSeekPercent() {
        return this.m_iCurrentSeekPercent;
    }

    public void Play(int iSeek) {
        Log.i(Utils.LOG_TAG, "EasyMovieTexture:Play");

        if (this.mMediaPlayer != null && (this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.READY || this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.PAUSED || this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.END)) {
            this.mMediaPlayer.start();
            this.m_iCurrentState = EasyMovieTexture.MEDIAPLAYER_STATE.PLAYING;
        }
    }

    public void Reset() {
        Log.i(Utils.LOG_TAG, "EasyMovieTexture:Reset");
        if (this.mMediaPlayer != null && this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.PLAYING) {
//            this.mMediaPlayer.reset();
        }

        this.m_iCurrentState = EasyMovieTexture.MEDIAPLAYER_STATE.NOT_READY;
    }

    public void Stop() {
        Log.i(Utils.LOG_TAG, "EasyMovieTexture:Stop");

        if (this.mMediaPlayer != null && this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.PLAYING) {
            this.mMediaPlayer.stop();
        }

        this.m_iCurrentState = EasyMovieTexture.MEDIAPLAYER_STATE.NOT_READY;
    }

    public void RePlay() {
        if (this.mMediaPlayer != null && this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.PAUSED) {
            this.mMediaPlayer.start();
            this.m_iCurrentState = EasyMovieTexture.MEDIAPLAYER_STATE.PLAYING;
        }

    }

    public void Pause() {
        Log.i(Utils.LOG_TAG, "EasyMovieTexture:Pause");

        if (this.mMediaPlayer != null && this.m_iCurrentState == EasyMovieTexture.MEDIAPLAYER_STATE.PLAYING) {
            this.mMediaPlayer.pause();
            this.m_iCurrentState = EasyMovieTexture.MEDIAPLAYER_STATE.PAUSED;
        }

    }

    public int GetVideoWidth() {
        return this.mMediaPlayer != null ? this.mMediaPlayer.getVideoWidth() : 0;
    }

    public int GetVideoHeight() {
        return this.mMediaPlayer != null ? this.mMediaPlayer.getVideoHeight() : 0;
    }

    public boolean IsUpdateFrame() {
        return this.m_bUpdate;
    }

    public void SetUnityTexture(int iTextureID) {
        Log.i(Utils.LOG_TAG, "EasyMovieTexture:SetUnityTexture");

        this.m_iUnityTextureID = iTextureID;
        this.SetManagerID(this.m_iNativeMgrID);
        this.SetUnityTextureID(this.m_iUnityTextureID);
    }

    public void SetUnityTextureID(Object texturePtr) {
    }

    public void SetSplitOBB(boolean bValue, String strOBBName) {
        this.m_bSplitOBB = bValue;
        this.m_strOBBName = strOBBName;
    }

    public int GetDuration() {
        return this.mMediaPlayer != null ? (int) this.mMediaPlayer.getDuration() : -1;
    }

    public int InitNative(EasyMovieTexture obj) {
        Log.i(Utils.LOG_TAG, "EasyMovieTexture:InitNative");

        this.m_iNativeMgrID = this.InitNDK(obj);
        m_objCtrl.add(this);
        return this.m_iNativeMgrID;
    }

    public void SetUnityActivity(Activity unityActivity) {
        Log.i(Utils.LOG_TAG, "EasyMovieTexture:SetUnityActivity");

        this.SetManagerID(this.m_iNativeMgrID);
        this.m_UnityActivity = unityActivity;
        this.SetAssetManager(this.m_UnityActivity.getAssets());
    }

    public void NDK_SetFileName(String strFileName) {
        Log.i(Utils.LOG_TAG, "EasyMovieTexture:NDK_SetFileName:" + strFileName);

        this.m_strFileName = strFileName;
    }

    public void InitJniManager() {
        this.SetManagerID(this.m_iNativeMgrID);
        this.InitApplication();
    }

    public int GetStatus() {
        return this.m_iCurrentState.GetValue();
    }

    public void SetNotReady() {
        this.m_iCurrentState = EasyMovieTexture.MEDIAPLAYER_STATE.NOT_READY;
    }

    public void SetWindowSize() {
        this.SetManagerID(this.m_iNativeMgrID);
        this.SetWindowSize(this.GetVideoWidth(), this.GetVideoHeight(), this.m_iUnityTextureID, this.m_bRockchip);
    }

    public int GetError() {
        return this.m_iErrorCode;
    }

    public int GetErrorExtra() {
        return this.m_iErrorCodeExtra;
    }

    public static enum MEDIAPLAYER_STATE {
        NOT_READY(0),
        READY(1),
        END(2),
        PLAYING(3),
        PAUSED(4),
        STOPPED(5),
        ERROR(6);

        private int iValue;

        private MEDIAPLAYER_STATE(int i) {
            this.iValue = i;
        }

        public int GetValue() {
            return this.iValue;
        }
    }

    private PLOnPreparedListener mOnPreparedListener = new PLOnPreparedListener() {
        @Override
        public void onPrepared(int preparedTime) {
            Log.i(Utils.LOG_TAG, "On Prepared ! prepared time = " + preparedTime + " ms");

            m_iCurrentState = EasyMovieTexture.MEDIAPLAYER_STATE.READY;
            SetManagerID(m_iNativeMgrID);
            m_iCurrentSeekPercent = 0;
            mMediaPlayer.setOnBufferingUpdateListener(mOnBufferingUpdateListener);
//            mMediaPlayer.start();
        }
    };

    private PLOnBufferingUpdateListener mOnBufferingUpdateListener = new PLOnBufferingUpdateListener() {
        public void onBufferingUpdate(int arg1) {
            m_iCurrentSeekPercent = arg1;
        }
    };

    private PLOnCompletionListener mOnCompletionListener = new PLOnCompletionListener() {
        @Override
        public void onCompletion() {
            Log.d(Utils.LOG_TAG, "Play Completed !");
            m_iCurrentState = EasyMovieTexture.MEDIAPLAYER_STATE.END;
        }
    };

    private PLOnErrorListener mOnErrorListener = new PLOnErrorListener() {
        @Override
        public boolean onError(int errorCode) {
            Log.e(Utils.LOG_TAG, "Error happened, errorCode = " + errorCode);
            switch (errorCode) {
                case PLOnErrorListener.ERROR_CODE_IO_ERROR:
                    /**
                     * SDK will do reconnecting automatically
                     */
//                    Utils.showToastTips(PLMediaPlayerActivity.this, "IO Error !");
                    return false;
                case PLOnErrorListener.ERROR_CODE_OPEN_FAILED:
//                    Utils.showToastTips(PLMediaPlayerActivity.this, "failed to open player !");
                    break;
                case PLOnErrorListener.ERROR_CODE_SEEK_FAILED:
//                    Utils.showToastTips(PLMediaPlayerActivity.this, "failed to seek !");
                    break;
                default:
//                    Utils.showToastTips(PLMediaPlayerActivity.this, "unknown error !");
                    break;
            }

            m_iCurrentState = EasyMovieTexture.MEDIAPLAYER_STATE.ERROR;

            return true;
        }
    };
}

```

