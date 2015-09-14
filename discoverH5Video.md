###HTML5 Video自动播放调研
- #####安卓4+改变了play()方法播放的条件，需要用户手势的介入才可以播放视频，让用户知晓他们正在使用带宽提升，用户体验。
- ######iPhone不支持自动播放，android上UC浏览器支持自动播放，最新版的chrome不支持。
- ######在android的webview上autoplay自动播放这个特性支持跟设备有关系，和android的版本号没关系，看各个浏览器的实现情况。并且大多数设备都是不能自动播放的。并且loop这个特性也基本上是不支持的。

- #####验证了通过setTimeout延时或者以下方式均无效
    myVideo.addEventListener('canplay', function() {
      video_player.play();
	});
	video_player.load();
	video_player.play();

#####解决方法
> 通过安卓API setMediaPlaybackRequiresUserGesture()

    package com.example.myProject;
    
    import android.os.Bundle;
    import org.apache.cordova.*;
    import android.webkit.WebSettings;
    
    public class myProject extends CordovaActivity 
    {
        @Override
        public void onCreate(Bundle savedInstanceState)
        {
            super.onCreate(savedInstanceState);
            super.init();
            // Set by <content src="index.html" /> in config.xml
            super.loadUrl(Config.getStartUrl());
            //super.loadUrl("file:///android_asset/www/index.html");
    
            WebSettings ws = super.appView.getSettings();
            ws.setMediaPlaybackRequiresUserGesture(false);
        }
    }
    
######参考文档
[Autostart html5 video using android 4 browser](http://stackoverflow.com/questions/11758651/autostart-html5-video-using-android-4-browser)

[How to autoplay HTML5 mp4 video on Android?](http://stackoverflow.com/questions/9075520/how-to-autoplay-html5-mp4-video-on-android)

[HTML5 Video调研](https://github.com/zmmbreeze/DeadSimpleVideoPlayer)

[android webview 播放 video经验总结](http://www.cnblogs.com/laozhbook/p/androidwebviewvideo.html)

[Autoplay, Loop and Muted](http://www.jwplayer.com/html5/autoloop/)

[Android HTML5 Video视频标签自动播放与自动全屏问题解决](http://blog.csdn.net/springlim/article/details/9534649)
