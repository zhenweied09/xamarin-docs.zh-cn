---
title: 在 Xamarin.iOS PhotoKit
description: 本文档介绍 PhotoKit，讨论其模型对象，如何查询模型数据以及保存照片库的更改。
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 4aeeec5b96e24c654407ad672930c0cb78592450
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787892"
---
# <a name="photokit-in-xamarinios"></a>在 Xamarin.iOS PhotoKit

PhotoKit 是一个新的框架，允许应用程序查询系统图像库和创建自定义用户界面查看和修改其内容。 它包括了多个类表示图像和视频资产，以及资产，例如专辑和文件夹的集合。

## <a name="model-objects"></a>模型对象

PhotoKit 表示它所调用模型对象中的这些资源。 表示的照片和视频本身的模型对象属于类型`PHAsset`。 A`PHAsset`包含元数据，例如资产的媒体类型和其创建日期。
同样，`PHAssetCollection`和`PHCollectionList`类分别包含有关资产集合和集合列表的元数据。 资产集合是的资产，如所有的照片和视频给定年份的分组。 同样，集合列表是组的资产集合，如照片和视频按年分组。

## <a name="querying-model-data"></a>查询模型数据

PhotoKit 轻松地查询模型数据通过各种提取方法。 例如，若要检索的所有映像，将调用`PFAsset.Fetch`，并传递`PHAssetMediaType.Image`媒体类型。

    PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);

`PHFetchResult`实例都将包含所有`PFAsset`表示图像的实例。 若要获取图像本身，你可以使用`PHImageManager`(或缓存的版本中， `PHCachingImageManager`) 以通过调用进行映像请求`RequestImageForAsset`。 例如，下面的代码检索每个资产中的映像`PHFetchResult`若要在集合视图单元格中显示：


    public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
    {
              var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
            imageMgr.RequestImageForAsset ((PHAsset)fetchResults [(uint)indexPath.Item], thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (), (img, info) => {
            imageCell.ImageView.Image = img;
        });
        return imageCell;
    }

这将导致网格的映像，如下所示：

![](photokit-images/image4.png "正在运行的应用显示的图像的网格")
 
## <a name="saving-changes-to-the-photo-library"></a>将更改保存到照片库

这是如何处理查询和读取数据。 你可以还将更改写回库。 由于多个关注应用程序能够与系统照片库进行交互，您可以注册观察程序接收通知的使用 PhotoLibraryObserver 进行更改。 然后，当更改，你的应用程序可以相应地更新。 例如，下面是一个简单的实现，以重新加载在上面的集合视图：

    class PhotoLibraryObserver : PHPhotoLibraryChangeObserver
    {
        readonly PhotosViewController controller;
        public PhotoLibraryObserver (PhotosViewController controller)
        
        {
            this.controller = controller;
        }
    
        public override void PhotoLibraryDidChange (PHChange changeInstance)
        {
            DispatchQueue.MainQueue.DispatchAsync (() => {
            var changes = changeInstance.GetFetchResultChangeDetails (controller.fetchResults);
            controller.fetchResults = changes.FetchResultAfterChanges;
            controller.CollectionView.ReloadData ();
            });
        }
    }
    
若要实际将更改写回从你的应用程序，你可以创建更改请求。 每个模型类具有关联的更改请求类。 例如，若要更改 PHAsset，你可以创建 PHAssetChangeRequest。 若要执行写回照片库并发送到与上述的观察者的更改的步骤如下：

-   执行编辑操作。
-   将筛选的图像数据保存到 PHContentEditingOutput 实例。
-   请更改请求将更改窗体发布编辑输出。

下面是将更改写回适用 Core 映像树上筛选器的图像示例：

    void ApplyNoirFilter (object sender, EventArgs e)
    {
            
            Asset.RequestContentEditingInput (new PHContentEditingInputRequestOptions (), (input, options) => {
            
        // perform the editing operation, which applies a noir filter in this case
            var image = CIImage.FromUrl (input.FullSizeImageUrl);
            image = image.CreateWithOrientation((CIImageOrientation)input.FullSizeImageOrientation);
            var noir = new CIPhotoEffectNoir {
                Image = image
            };
            var ciContext = CIContext.FromOptions (null);
            var output = noir.OutputImage;
            var uiImage = UIImage.FromImage (ciContext.CreateCGImage (output, output.Extent));
            imageView.Image = uiImage;
        //
        // save the filtered image data to a PHContentEditingOutput instance
            var editingOutput = new PHContentEditingOutput(input);
            var adjustmentData = new PHAdjustmentData();
            var data = uiImage.AsJPEG();
            NSError error;
            data.Save(editingOutput.RenderedContentUrl, false, out error);
            editingOutput.AdjustmentData = adjustmentData;
        //
        // make a change request to publish the changes form the editing output
            PHPhotoLibrary.GetSharedPhotoLibrary.PerformChanges (() => {
                PHAssetChangeRequest request = PHAssetChangeRequest.ChangeRequest(Asset);
                request.ContentEditingOutput = editingOutput;
          },
          (ok, err) => Console.WriteLine ("photo updated successfully: {0}", ok));
      });
    }
    
当用户选择按钮时，将应用筛选器：

![](photokit-images/image5.png "下面举例说明正在应用的筛选器")
 
和感谢 PHPhotoLibraryChangeObserver，更改将反映在集合视图当用户导航回：

![](photokit-images/image6.png "当用户导航回会反映集合视图中更改")
