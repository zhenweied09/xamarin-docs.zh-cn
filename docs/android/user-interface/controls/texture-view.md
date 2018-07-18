---
title: TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/30/2017
ms.openlocfilehash: 1cd332894deac1e1fb076f647bb0120bda2306da
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763600"
---
# <a name="textureview"></a>TextureView

`TextureView`类是一个使用硬件加速二维呈现若要启用的视频或 OpenGL 内容流，要显示的视图。 例如，下面的屏幕截图显示`TextureView`显示从设备的照相机的实时源：

[![从设备的照相机的实时映像的示例屏幕快照](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

与不同`SurfaceView`类，该类还可以用于显示 OpenGL 或视频内容，TextureView 不呈现到单独的窗口。
因此，`TextureView`能够支持像任何其他视图的视图转换。 例如，轮换`TextureView`可以通过只需设置来完成其`Rotation`属性时，通过设置其透明度其`Alpha`属性，依次类推。

因此，对于`TextureView`我们现在可以从相机执行诸如显示实时流和转换，如下面的代码中所示：

```csharp
public class TextureViewActivity : Activity,
    TextureView.ISurfaceTextureListener
{
    Camera _camera;
    TextureView _textureView;
       
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        _textureView = new TextureView (this);
        _textureView.SurfaceTextureListener = this;
           
        SetContentView (_textureView);
    }
       
    public void OnSurfaceTextureAvailable (
        Android.Graphics.SurfaceTexture surface,
        int width, int height)
    {
        _camera = Camera.Open ();
        var previewSize = _camera.GetParameters ().PreviewSize;
        _textureView.LayoutParameters =
            new FrameLayout.LayoutParams (previewSize.Width,
                previewSize.Height, (int)GravityFlags.Center);

        try {
            _camera.SetPreviewTexture (surface);
            _camera.StartPreview ();
        } catch (Java.IO.IOException ex) {
            Console.WriteLine (ex.Message);
        }
           
        // this is the sort of thing TextureView enables
        _textureView.Rotation = 45.0f;
        _textureView.Alpha = 0.5f;
    }
    …
}
```

上面的代码创建`TextureView`中活动的实例`OnCreate`方法，并将设置为活动`TextureView`的`SurfaceTextureListener`。 要`SurfaceTextureListener`，活动实现`TextureView.ISurfaceTextureListener`接口。 系统将呼叫`OnSurfaceTextAvailable`方法时`SurfaceTexture`随时可供使用。 在此方法中，我们需要`SurfaceTexture`传递中并将其设置为的照相机的预览纹理。 然后我们就可以随意执行正常的视图基于的操作，例如设置`Rotation`和`Alpha`，上面的示例所示。 生成的应用程序，在设备上，运行如下所示：

[![显示图像的设备上运行的应用示例](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

若要使用`TextureView`，硬件加速必须启用，它将默认情况下，截至 API 级别 14。 此外，因为此示例使用相机，同时`android.permission.CAMERA`权限和`android.hardware.camera`功能必须设置**AndroidManifest.xml**。



## <a name="related-links"></a>相关链接

- [TextureViewDemo （示例）](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [引入冰激凌德桑威奇](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
