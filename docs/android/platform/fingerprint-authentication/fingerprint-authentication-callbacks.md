---
title: 响应身份验证回调
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/06/2017
ms.openlocfilehash: b8a3ed64e66cd97faeff78b4d0b008a1a0b14477
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2018
---
# <a name="responding-to-authentication-callbacks"></a>响应身份验证回调

在其自己的线程上在后台运行指纹扫描仪和完成后，它将通过调用一种方法的报告的扫描结果`FingerprintManager.AuthenticationCallback`UI 线程上。 Android 应用程序必须提供自己的处理程序扩展了此抽象类，实现所有以下方法：

* **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; 不可恢复的错误时调用。 没有任何应用程序或用户可以进行以纠正这种情况只可能再试一次。
* **`OnAuthenticationFailed()`** &ndash; 指纹已检测到但无法识别的设备时，将调用此方法。
* **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; 可恢复的错误，如正在快速通过扫描仪刷到上方的手指时调用。
* **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; 这称为时已识别指纹。

如果`CryptoObject`时调用使用`Authenticate`，建议调用`Cipher.DoFinal`中`OnAuthenticationSuccessful`。
`DoFinal` 如果密码已被篡改或不正确初始化，将引发异常，这表明，指纹扫描程序的结果可能已被篡改的应用程序之外。


> [!NOTE]
> 建议保留回调类相对较轻权重和免费的应用程序特定逻辑。 回调应充当"流量个 cop"Android 应用程序和结果之间指纹扫描程序。

## <a name="a-sample-authentication-callback-handler"></a>示例身份验证回调处理程序

下面的类是一个示例的最小`FingerprintManager.AuthenticationCallback`实现： 

```csharp
class MyAuthCallbackSample : FingerprintManagerCompat.AuthenticationCallback
{
    // Can be any byte array, keep unique to application.
    static readonly byte[] SECRET_BYTES = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    // The TAG can be any string, this one is for demonstration.
    static readonly string TAG = "X:" + typeof (SimpleAuthCallbacks).Name;

    public MyAuthCallbackSample()
    {
    }

    public override void OnAuthenticationSucceeded(FingerprintManagerCompat.AuthenticationResult result)
    {
        if (result.CryptoObject.Cipher != null) 
        {
            try
            {
                // Calling DoFinal on the Cipher ensures that the encryption worked.
                byte[] doFinalResult = result.CryptoObject.Cipher.DoFinal(SECRET_BYTES);
    
                // No errors occurred, trust the results.              
            }
            catch (BadPaddingException bpe)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + bpe);
            }
            catch (IllegalBlockSizeException ibse)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + ibse);
            }
        }
        else
        {
            // No cipher used, assume that everything went well and trust the results.
        }
    }

    public override void OnAuthenticationError(int errMsgId, ICharSequence errString)
    {
        // Report the error to the user. Note that if the user canceled the scan,
        // this method will be called and the errMsgId will be FingerprintState.ErrorCanceled.
    }

    public override void OnAuthenticationFailed()
    {
        // Tell the user that the fingerprint was not recognized.
    }

    public override void OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)
    {
        // Notify the user that the scan failed and display the provided hint.
    }
}
```

`OnAuthenticationSucceeded` 检查以查看是否`Cipher`提供给`FingerprintManager`时`Authentication`调用。 如果是这样，`DoFinal`密码上调用方法。 此操作会关闭`Cipher`，还原到其原始状态。 如果时出现问题与密码，然后`DoFinal`将引发异常，应考虑身份验证尝试已失败。

`OnAuthenticationError`和`OnAuthenticationHelp`回调每个接收一个整数，指示出现了什么问题。 以下部分说明了每个可能帮助或错误代码。 两个回调类似用途&ndash;以通知应用程序，该指纹身份验证失败。 它们之间的区别是的严重程度。 `OnAuthenticationHelp` 是用户恢复的错误，如轻扫太快; 的指纹`OnAuthenticationError`是更严重错误，如损坏的指纹扫描仪。

请注意，`OnAuthenticationError`在指纹扫描被取消时通过将调用`CancellationSignal.Cancel()`消息。 `errMsgId`参数将具有值为 5 (`FingerprintState.ErrorCanceled`)。 根据要求的实现`AuthenticationCallbacks`可能不同于其他错误处理这种情况。 

`OnAuthenticationFailed` 已成功扫描指纹，但与任何注册的设备的指纹不匹配时进行调用。 

## <a name="help-codes-and-error-message-ids"></a>帮助代码和错误消息 Id 

在中可能找到的列表和说明的错误代码和帮助代码[Android SDK 文档](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD)FingerprintManager 类。 Xamarin.Android 表示这些值用于`Android.Hardware.Fingerprints.FingerprintState`枚举：


-   **`AcquiredGood`** &ndash; （值 0）获取映像为很好。


-   **`AcquiredImagerDirty`** &ndash; （值 3）由于可疑或检测到灰尘传感器上过多的干扰指纹映像。 例如，它位于合理以在多个后返回此`AcquiredInsufficient`或灰尘传感器 （像素滞留、 负责等） 上的实际检测。 用户需要采取措施时返回此清洁传感器。


-   **`AcquiredInsufficient`** &ndash; （值 2）指纹映像已过多的干扰由于检测到的条件 （即干外观） 或可能是脏的传感器处理 (请参阅`AcquiredImagerDirty`。



-   **`AcquiredPartial`** &ndash; （值 1）检测到仅部分指纹映像。 在注册中，用户应通知上需要进行若要解决此问题，例如，哪些&ldquo;用力按传感器。&rdquo;



-   **`AcquiredTooFast`** &ndash; （值 5）指纹映像已由于快速运动不完整。 虽然主要适用于线性数组传感器，这可能是如果期间获取移动上方的手指。 用户应需要在移动上方的手指速度较慢 （非线性） 或更长传感器上保留上方的手指。




-   **`AcquiredToSlow`** &ndash; （值 4）由于缺乏运动不可读的指纹映像。 这是最适合需要轻扫运动的线性数组传感器。



-   **`ErrorCanceled`** &ndash; （值 5）该操作已取消，因为指纹传感器处于不可用。 例如，这可能会发生时切换用户、 设备被锁定，或者另一个挂起操作会阻止或禁用它。



-   **`ErrorHwUnavailable`** &ndash; （值 1）硬件不可用。 稍后再试。




-   **`ErrorLockout`** &ndash; （值 7）该操作已取消，因为 API 因尝试次数过多而被锁定。




-   **`ErrorNoSpace`** &ndash; （值 4）为操作，如注册; 返回的错误状态无法完成操作，因为没有足够的存储，仍要完成该操作。



-   **`ErrorTimeout`** &ndash; （值 3）当前请求已运行过长时返回的错误状态。 这被为了防止无限期地等待指纹传感器程序。 超时是平台和特定于传感器的但通常大约 30 秒。



-   **`ErrorUnableToProcess`** &ndash; （值 2）当传感器无法处理当前映像时，返回的错误状态。



## <a name="related-links"></a>相关链接

- [Cipher](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
