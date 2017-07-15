---
title: RecyclerView 瀑布流图片闪烁问题
date: 2017-05-20 13:05:44
categories: Android记录
tags:
 - RecyclerView
---

### 前言

看了许多组件化, 模块化开发的文章, 就想着做个APP练练手.

看到[干货集中营](http://gank.io/download)的妹子不错哈, 弄进来养养眼, 同时提升下APP的颜值.   

首先感谢 [代码家](https://daimajia.com/) 提供的 [API](http://gank.io/api)

<!-- more -->



### 准备

在[干货集中营](http://gank.io/download)上已经有许多APP, 先来张别人的效果图



<img src="http://ww2.sinaimg.cn/large/610dc034gw1f1ygccxjvpj20k00zkn02.jpg" width="50%" />



看效果首先想到的是 **RecyclerView** 利用 **StaggeredGridLayoutManager** 来实现的瀑布流



### 问题

代码没啥难度，设置 **LayoutManager**

```java
StaggeredGridLayoutManager layout = new StaggeredGridLayoutManager(2,StaggeredGridLayoutManager.VERTICAL);
layout.setGapStrategy(StaggeredGridLayoutManager.GAP_HANDLING_NONE);
```



不过在这过程中却遇到 **坑**

 ![盗图](http://upload-images.jianshu.io/upload_images/1633070-755a1764d40ea541.gif?imageMogr2/auto-orient/strip)

> 这图是盗得哈~  效果一样的, 懒得录啦~



在滑动过程中，Item不停闪烁，变换位置 ， 效果很差。
这肯定不是想要的效果,首先分析下~



### 分析

图片的显示是利用Glide异步加载的，在 Item 绘制的时候，图片还没加载出来。 

因为瀑布流的特性，图片的宽高不能固定成统一值，所以等到 Glide 将图片加载下来时，图片会重新计算尺寸布局，从而导致整个瀑布流会再次计算布局，调整位置，因此而出现跳动，闪烁等现象.

所以, 现在解决问题的关键是, **在Item绘制的时, 确定图片的宽高**，这样在图片加载出来时就不会重新布局



### 解决

最开始想到的方案是, 异步将所有图片的 **url** 通过 **Glide** 转换成 **Bitmap**，然后扔给 **RecyclerView **，  都不用计算尺寸, 直接显示图片

事实证明 **太天真~~**

持有这么多 Bitmap 对象，滚动出屏幕时有没有释放，内存吃不消啊~



在 Google 了一波之后，发现了两种解决方案。

1. 最好的解决办法是，在请求图片时，后台同时把图片的宽高信息一起返回，这是最完美的解决方案。 如果条件不允许的话，只能通过下面的方式
2. 首先通过 **Glide** 将图片下载到本地,然后通过 [BitmapFactory.Options](https://developer.android.com/reference/android/graphics/BitmapFactory.Options.html) 预读取出图片的宽高，然后将数据封装后扔给 **RecyclerView** 显示

```java
     //Glide 同步加载图片到本地文件, 需在子线程运行 
     File file = Glide.with(Utils.getContext())
       .load(gankItemBean.getUrl())
       .downloadOnly(Target.SIZE_ORIGINAL, Target.SIZE_ORIGINAL)
       .get();

	//预读取图片的宽高
     BitmapFactory.Options options=new BitmapFactory.Options();
     options.inJustDecodeBounds=true;//关键是这个
     BitmapFactory.decodeStream(new FileInputStream(file),null,options);

     return gankItemBean.getUrl() + "*" + options.outWidth + "*" + options.outHeight;    
```

在 RecyclerView 中使用

```
            if (width == 0) {
                width = ((Activity) view.getContext())
                		.getWindowManager().getDefaultDisplay()
                		.getWidth();
                width = width / 2;
            }

            String[] result = item.split("\\*");
            if (result.length != 3) {
                return;
            }

            float rote = (width) / Float.valueOf(result[1]);


            ViewGroup.LayoutParams layoutParams = view.getLayoutParams();
            layoutParams.width = (int) width;
            layoutParams.height = (int) (Float.valueOf(result[2]) * rote);
            view.setLayoutParams(layoutParams);

            Glide.with(view.getContext())
            .load(result[0])
            .diskCacheStrategy(DiskCacheStrategy.ALL)
            .into(view);
```



完成之后的效果：

![效果](http://omrcgnd7e.bkt.clouddn.com/2017-5-22.gif)

话说，做的 gif 图片太大了，效果有点卡，不知道怎么优化....