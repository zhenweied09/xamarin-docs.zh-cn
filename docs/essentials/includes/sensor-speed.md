---
ms.topic: include
ms.openlocfilehash: 5c11c94956f8d56c66c50a9a480177c5b77c2643
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947428"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[传感器速度](xref:Xamarin.Essentials.SensorSpeed)

- **最快**– 尽可能 （不保证返回 UI 线程上） 快速获取传感器数据。
- **游戏**– 速率适用于游戏 （不保证返回 UI 线程上）。
- **正常**– 适用于屏幕方向更改默认速率。
- **Ui** – 速率适用于常规用户界面。

如果事件处理程序不能保证在 UI 线程上运行，如果事件处理程序需要访问用户界面元素，使用[ `MainThread.BeginInvokeOnMainThread` ](~/essentials/main-thread.md)方法在 UI 线程上运行该代码。