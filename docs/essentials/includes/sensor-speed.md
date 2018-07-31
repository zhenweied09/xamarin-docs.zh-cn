---
ms.topic: include
ms.openlocfilehash: e4dfd1ac12f3010939d483381a785091d71599ed
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353264"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[传感器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快**– 尽可能 （不保证返回 UI 线程上） 快速获取传感器数据。
- **游戏**– 速率适用于游戏 （不保证返回 UI 线程上）。
- **正常**– 适用于屏幕方向更改默认速率。
- **UI** – 速率适用于常规用户界面。

如果事件处理程序不能保证在 UI 线程上运行，如果事件处理程序需要访问用户界面元素，使用[ `MainThread.BeginInvokeOnMainThread` ](~/essentials/main-thread.md)方法在 UI 线程上运行该代码。