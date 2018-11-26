---
ms.topic: include
ms.openlocfilehash: e4dfd1ac12f3010939d483381a785091d71599ed
ms.sourcegitcommit: 676c5a6795ab4896ccd1b288424bf2040b1208aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2018
ms.locfileid: "52294801"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[传感器速度](xref:Xamarin.Essentials.SensorSpeed)

- 最快 – 尽快获取传感器数据（不保证在 UI 线程上返回）。
- 游戏 – 适合游戏的速度（不保证在 UI 线程上返回）。
- 正常 – 适合屏幕方向更改的默认速率。
- UI – 适合常规用户界面的速率。

如果事件处理程序不能保证在 UI 线程上运行，并且如果事件处理程序需要访问用户界面元素，请使用 [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) 方法在 UI 线程上运行该代码。