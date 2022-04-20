---

title: IJKPlayer使用SeekTo跳转关键帧不正确

date: 2017-12-21 17:56:53

tags: [ Android , IJKPlayer , ]

categories: [Android , 问题记录 ]

keywords: Android , IJKPlayer , 跳转关键帧

description: IJKPlayer使用SeekTo跳转关键帧不正确

top_img: http://image.jucaiwy.com/image/20161221/ijk_player.jpeg

cover: http://image.jucaiwy.com/image/20161221/ijk_player.jpeg

---

今天在做一个视频播放的功能，播放内核使用的IJKPlayer，在拖动进度条进行跳转的时候，发现跳转位置总是不准确，尤其在10秒以内的视频播放中进行拖动，跳转的更不准确，经常调至开头或者结尾。

分析其原因，发现进度条SeekBar定位是没有问题的，但是使用IjkMediaPlayer.seekTo(int position)方法时，总是不能准确定位。分析是IjkMediaPlayer内部问题，可能因为视频时间较短，压缩率较高等原因，导致关键帧确实，进度条定点位置内没有找到关键帧，所以，定位不准确。


### 解决方法
在初始化视频播放器的时候，添加seekTo支持

```java
((IjkMediaPlayer)mMediaPlayer).setOption(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "enable-accurate-seek", 1);
```

至于这个配置具体还有别的什么，我再找找。
