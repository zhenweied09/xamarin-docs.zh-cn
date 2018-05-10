---
title: 与 CocosSharp 二维数学
description: 本指南介绍用于游戏开发的二维数学。 它使用 CocosSharp 来演示如何执行常见的游戏开发任务，并解释了这些任务背后的数学函数。
ms.prod: xamarin
ms.assetid: 5C241AB4-F97E-4B61-B93C-F5D307BCD517
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 13279df69b7a22117c10d74f1a15c082aff13264
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2018
---
# <a name="2d-math-with-cocossharp"></a>与 CocosSharp 二维数学

_本指南介绍用于游戏开发的二维数学。它使用 CocosSharp 来演示如何执行常见的游戏开发任务，并解释了这些任务背后的数学函数。_

若要定位和移动代码的对象是开发游戏的所有大小的核心部分。 定位和移动都需要数学，的使用是否游戏需要移动沿一条直线或用于的 trigonometry 旋转的对象。 本文档将涉及以下主题：

 - 速度
 - 加速
 - 旋转 CocosSharp 对象
 - 使用速度旋转

用户不具有强数学背景，或人员 long 类型的值-忘记了从学校，这些主题的开发人员不需要担心 – 本文档将将概念分解成口大小的各部分，并将伴随理论说明以及实用示例。 简单地说，本文将回答一个老掉牙数学学生问题:"当我实际上需要使用此内容？"


## <a name="requirements"></a>要求

尽管本文档重点介绍主要的数学 CocosSharp 一端，代码示例假定继承窗体的对象的使用`CCNode`。 此外，由于`CCNode`不包含值的速度和加速，该代码假定使用提供值，例如 VelocityX、 VelocityY、 AccelerationX 和 AccelerationY 的实体。 实体的详细信息，请参阅我们的演练上[CocosSharp 中的实体](~/graphics-games/cocossharp/entities.md)。


## <a name="velocity"></a>速度

游戏开发人员使用术语*速度*来描述如何将对象作为移动 – 专门的内容的速度移动和方向，因此，并移动。 

使用两种类型的单位定义速度： 位置单元和时间单位。 例如，一辆汽车的速度被指每小时的英里或公里每小时为单位。 每秒的时间来定义如何快速对象通常使用像素移动游戏开发人员。 例如，一个项目符号可能移动以每秒的 300 像素的速度。 也就是说，如果在 300 像素，每秒移动项目符号，然后它将已移 600 单位中两秒内和 900 单位在三秒，依次类推。 一般来说，速度值*添加*到对象的位置 （如我们所见下面）。

尽管我们使用速度以解释速度的单位，但是术语速度通常是指独立于方向，一个值而术语速度是指速度和方向。 因此，一个项目符号的速度 （假设项目符号一个类，其中包括必需的属性） 的分配可能如下所示：


```csharp
// This bullet is not moving horizontally, so set VelocityX to 0:
bulletInstance.VelocityX = 0;
// Positive Y is "up" so move the bullet up 300 units per second:
bulletInstance.VelocityY = 300;
```


### <a name="implementing-velocity"></a>实现速度

CocosSharp 未实现速度，因此需要移动的对象需要实现其自己的移动逻辑。 新游戏开发人员提供实现速度经常错误地使其速度取决于帧速率。 即以下*不正确实现*看起来提供正确的结果，但将基于游戏的帧速率：

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX;
this.PositionY += this.VelocityY;
```

如果游戏使用率达到更高的帧速率 （如 60 每秒帧数而不是每秒 30 帧），然后将看上去像对象如果运行在速度较慢的帧速率相比更快移动。 同样，如果无法处理作为高的帧速率 （这可能由后台进程使用设备的资源） 在帧游戏，将显示游戏速度下降，这一情况。

若要考虑到这一点，速度通常实现使用时间值。 例如，如果`seconds`变量表示的秒数 （或部分） 由于已应用的最后一个时间速度，则下面的代码会导致帧速率无论一致移动的对象：

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

请考虑其运行速度较低的帧速率游戏将频繁地更新其较少的对象的位置。 因此，每个更新将导致游戏已更频繁地更新它们也会进一步移动的对象。 `seconds`值帐户，则为此操作，请通过报告自上次更新后经过多少时间。

有关如何添加基于时间的移动的示例，请参阅[涵盖时间此食谱基于移动](https://developer.xamarin.com/recipes/cross-platform/game_development/time_based_movement/)。


### <a name="calculating-positions-using-velocity"></a>计算使用速度的位置

可以使用速度，预测相关对象后一定的时间的推移，将在其中或者帮助优化对象的行为而无需运行游戏。 例如，开发人员正在实现一个激发的项目符号的移动需要它实例化后设置该项目符号的速度。 屏幕的大小可以用于为设置速度提供基础。 即如果开发人员知道项目符号应在 2 秒内移动屏幕的高度，则速度应设置为除以 2 屏幕的高度。 如果屏幕为 800 像素、 高，则该项目符号的速度将设置为 400 （这是 800/2）。

同样中的游戏逻辑可能需要计算需要多长时间对象将到达目标给定其速度。 这可以通过除以的距离按 travelling 对象的速度都能够计算得出。 例如，下面的代码演示如何将文本分配给的标签，其中显示了多长时间直到导弹到达其目标：


```csharp
// We'll assume only the X axis for this example
float distanceX = target.PositionX - missile.PositionX;

float secondsToReachTarget = distanceX / missile.VelocityX;

label.Text = secondsToReachTarget + " seconds to reach target"; 
```


## <a name="acceleration"></a>加速

*加速*是在游戏开发中，一个公共概念和它与速度共享许多相似之处。 加速量化是否加快或速度变慢 （速度值如何更改随着时间的推移） 的对象。 加速*添加*于速度，就像速度添加位置。 常见的应用程序的加速包括重力、 加快，一辆汽车和激发其 thrusters 空间装运。 

类似于速度，加速中定义的位置和时间单位;但是，加速的时间单位称为*平方*单元，这反映了数学上定义加速的方式。 也就是说，游戏加速通常的测量单位为*每秒像素平方*。

如果对象具有 10 个单位每秒平方值 X 加速，这意味着，其速度将增加 10 每隔一秒。 如果它将在每秒后, 两个 10 个单位移动的一秒后从停滞，开始，秒每 20 个单位，第二个，依次类推。

在两个维度中的加速要求的 X 和 Y 组件，因此它可能会分配，如下所示：


```csharp
// No horizontal acceleration:
icicle.AccelerationX = 0;
// Simulate gravity with Y acceleration. Negative Y is down, so assign a negative value:
icicle.AccelerationY = -50; 
```


### <a name="acceleration-vs-deceleration"></a>与减速的加速

尽管加速和减速有时区分中的日常语音，但是具有这两者之间没有技术差异。 重力是强制这会导致加速。 如果对象引发向上然后重力将减缓它 （减速），但该对象已停止攀升到 5，和重力的方向相同故障后然后重力加快它 （加速）。 如下所示，使用加速应用程序是否正在按的相同方向或相反方向移动应用均相同。 


### <a name="implementing-acceleration"></a>实现加速

在实现时，加速是类似于速度 – 未自动实现的 CocosSharp，并基于时间的加速是 （而不是基于框架的加速） 所需的实现。 因此 （以及速度） 的简单加速实现可能如下所示：

```csharp
this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds;
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

上面的代码是什么称为*线性近似值*加速实现。 实际上，它实现加速有相当密切程度的准确性，但它不是一个完全准确的模型的加速。 它包括上面帮助说明如何实现加速的概念。

下面的实现是加速和速度的一个数学上准确的应用程序：


```csharp
float halfSecondsSquared = (seconds * seconds) / 2.0f;

this.PositionX += 
    this.Velocity.X * seconds + this.AccelerationX * halfSecondsSquared;
this.PositionY += 
    this.Velocity.Y * seconds + this.AccelerationY * halfSecondsSquared;

this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds; 
```

上面的代码最明显的区别是`halfSecondsSquared`变量和其使用情况将加速位置。 数学原因不在本教程中，范围，但开发人员感兴趣这背后的数学函数可以找到详细信息在[有关将集成加速此讨论。](http://www.cliffsnotes.com/math/calculus/calculus/integration/distance-velocity-and-acceleration)

实际影响`halfSecondSquare`是该加速的表现数学上准确地并可预见的方式而不考虑帧速率。 加速的线性近似值受到帧速率 – 越低帧速率删除变得不太准确近似值。 使用`halfSecondsSquared`保证代码的行为相同，但与帧速率无关。


## <a name="angles-and-rotation"></a>角度和旋转

Visual 对象如`CCSprite`支持通过旋转`Rotation`变量。 这可以分配到一个值以度为单位设置其旋转。 例如，下面的代码演示如何旋转`CCSprite`实例：


```csharp
CCSprite unrotatedSprite = new CCSprite("star.png");
unrotatedSprite.IsAntialiased = false;
unrotatedSprite.PositionX = 100;
unrotatedSprite.PositionY = 100;
this.AddChild (unrotatedSprite);

CCSprite rotatedSprite = new CCSprite("star.png");
rotatedSprite.IsAntialiased = false;
// This sprite is moved to the right so it doesn’t overlap the first
rotatedSprite.PositionX = 130;
rotatedSprite.PositionY = 100;
rotatedSprite.Rotation = 45;
this.AddChild (rotatedSprite); 
```

这将产生以下结果：

![](math-images/image1.png "这会导致此屏幕截图")

请注意，旋转 45 度顺时针旋转 （即出于历史原因旋转如何数学上应用的相反）：

![](math-images/image2.png "请注意，旋转是顺时针旋转 45 度它出于历史原因是旋转如何数学上应用的相反")

一般情况下 CocosSharp 和正则数学旋转可以可视化，如下所示：

![](math-images/image3.png "一般情况下要旋转 CocosSharp 和正则数学可视化如下")

![](math-images/image4.png "一般情况下要旋转 CocosSharp 和正则数学可视化如下")

此区别很重要，因为`System.Math`类使用逆时针旋转，因此开发人员熟悉此类需要时使用反转角度`CCNode`实例。

我们应注意上面显示的图，以度为单位; 显示旋转但是，某些数学函数 (如中的函数`System.Math`命名空间) 期望和返回值中的*弧度*而不是度。 我们将查看如何稍后在本指南中在两个单位类型之间进行转换。


### <a name="rotating-to-face-a-direction"></a>轮换面临着一个方向

如上所示，`CCSprite`可以使用要轮换`Rotation`属性。 `Rotation`提供属性`CCNode`(类的基类`CCSprite`)，这意味着旋转可以应用于实体继承自这`CCNode`以及。 

有些游戏要求对象，因此它们不会遇到目标要轮换。 示例包括在播放器目标时或向其中用户触摸屏幕的点飞行空间发货解决计算机控制的防范对象。 但是，旋转值必须首先将基于计算要轮替的实体的位置和目标中，以便不会遇到的位置。

此过程需要几个步骤：

 - 标识*偏移量*的目标。 偏移量是指的旋转实体和目标实体之间的 X 和 Y 距离。
 - 通过使用反正切值 trigonometry 函数 （下面详细说明） 计算从偏移量的角度。
 - 调整为 0 度箭头指向右侧和旋转实体点时未旋转方向之间存在的差异。
 - 调整为数学旋转 （逆时针） 和 CocosSharp 旋转 （顺时针） 之间的差异。

下面的函数 （假定要写入的实体） 旋转面临目标的实体：


```csharp
// This function assumes that it is contained in a CCNode-inheriting object
public void FacePoint(float targetX, float targetY)
{
    // Calculate the offset - the target's position relative to "this"
    float xOffset = targetX - this.PositionX;
    float yOffset = targetY - this.PositionY;

    // Make sure the target isn't the same point as "this". If so,
    // then rotation cannot be calculated.
    if (targetX != this.PositionX || targetY != this.Position.Y)
    {

        // Call Atan2 to get the radians representing the angle from 
        // "this" to the target
        float radiansToTarget = (float)System.Math.Atan2 (yOffset, xOffset);

        // Since CCNode uses degrees for its rotation, we need to convert
        // from radians
        float degreesToTarget = CCMathHelper.ToDegrees (radiansToTarget);

        // The direction that the entity faces when unrotated. In this case
        // the entity is facing "up", which is 90 degrees 
        const float forwardAngle = 90;

        // Adjust the angle we want to rotate by subtracting the
        // forward angle.
        float adjustedForDirecitonFacing = degreesToTarget - forwardAngle;

        // Invert the angle since CocosSharp uses clockwise rotation
        float cocosSharpAngle = adjustedForDirecitonFacing * -1;

        // Finally assign the rotation
        this.Rotation = rotation = cocosSharpAngle;
    }
} 
```

上面的代码无法用于旋转实体以使其面向用户，如下所示触摸屏幕，点：


```csharp
private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    if(touches.Count > 0)
    {
        CCTouch firstTouch = touches[0];
        FacePoint (firstTouch.Location.X, firstTouch.Location.Y);
    }
} 
```

此代码将导致以下行为：

![](math-images/image5.gif "此代码将导致此行为")

#### <a name="using-atan2-to-convert-offsets-to-angles"></a>使用 Atan2 转换到角度偏移量

`System.Math.Atan2` 可用来将偏移量转换为一个角度。 函数名称`Atan2`来自三角函数反正切值。 "2"的后缀区分此函数从标准`Atan`函数，严格匹配反正切值的数学行为。 反正切值是返回一个值介于-90 之间的函数和 + 90 度 （或以弧度为单位的等效项）。 许多应用程序，包括计算机游戏，通常需要完全 360 度范围的值，因此`Math`类包括`Atan2`为满足此需求。

请注意，上面的代码向传递 Y 参数第一次，然后 X 参数中，在调用时`Atan2`方法。 这是向后从常用的 X，Y 排序的位置坐标。 有关详细信息[请参阅 Atan2 文档](https://msdn.microsoft.com/library/system.math.atan2(v=vs.110).aspx)。

此外值得注意的是返回值从`Atan2`是以弧度表示，即用于度量角度的另一个单元。 本指南不涵盖的详细信息的弧度表示，但请记住，中的所有三角函数`System.Math`命名空间使用弧度表示，因此正在用于 CocosSharp 对象之前，必须将任何值转换为度。 找不到弧度为单位的详细信息[中的弧度维基百科网页](http://en.wikipedia.org/wiki/Radian)。

#### <a name="forward-angle"></a>转发的角度

一次`FacePoint`方法转换为弧度表示的角度，它还定义`forwardAngle`值。 此值表示当其旋转值等于 0 时，该实体面向的角度。 在此示例中，我们假设，该实体朝上，即使用 （而不是 CocosSharp 旋转） 的数学旋转 90 度。 由于我们尚未尚未反转 CocosSharp 的旋转角度，我们此处使用数学旋转。

下面的示例演示具有哪些的实体`forwardAngle`90 度的可能如下所示：

![](math-images/image6.png "下面的示例演示具有 90 度 forwardAngle 的实体可能如下所示")


### <a name="angled-velocity"></a>带角的速度

到目前为止我们介绍了如何将转换为一个角度的偏移量。 本部分将另一种方法 – 将角度，并将其转换为 X 和 Y 值。 常见示例包括在遇到，方向或解决在面向发货的方向，将移动项目符号空间发货中移动一辆汽车。 

从概念上讲，速度可以通过第一个定义的所需的速度时未旋转，则旋转到面向实体的角度该速度。 为了说明这一概念，速度 （和加速） 可以将可视化为 2 维*向量*（其中通常绘制为一个箭头）。 具有 X 速度值的向量 = 100 和 Y = 0，如下所示进行可视化：

![](math-images/image7.png "具有 X 速度值的向量 = 100 和 Y = 0，如下所示进行可视化")

此向量可旋转导致新的速度。 例如，通过 （使用逆时针旋转） 的 45 度旋转向量产生以下结果：

![](math-images/image8.png "在此使用逆时针旋转结果的 45 度旋转向量")

幸运的是，速度、 加速和甚至位置可以所有要轮换使用相同的代码，而不考虑如何才应用属性值。 下面的通用函数可用来旋转向量通过 CocosSharp 旋转值：


```csharp
// Rotates the argument vector by degrees specified by
// cocosSharpDegrees. In other words, the rotation
// value is expected to be clockwise.
// The vector parameter is modified, so it is both an in and out value
void RotateVector(ref CCVector2 vector, float cocosSharpDegrees)
{
    // Invert the rotation to get degrees as is normally
    // used in math (counterclockwise)
    float mathDegrees = -cocosSharpDegrees;

    // Convert the degrees to radians, as the System.Math
    // object expects arguments in radians
    float radians = CCMathHelper.ToRadians (mathDegrees);

    // Calculate the "up" and "right" vectors. This is essentially
    // a 2x2 matrix that we'll use to rotate the vector
    float xAxisXComponent = (float)System.Math.Cos (radians);
    float xAxisYComponent = (float)System.Math.Sin (radians);
    float yAxisXComponent = (float)System.Math.Cos (radians + CCMathHelper.Pi / 2.0f);
    float yAxisYComponent = (float)System.Math.Sin (radians + CCMathHelper.Pi / 2.0f);

    // Store the original vector values which will be used
    // below to perform the final operation of rotation.
    float originalX = vector.X;
    float originalY = vector.Y;

    // Use the axis values calculated above (the matrix values)
    // to rotate and assign the vector.
    vector.X = originalX * xAxisXComponent + originalY * yAxisXComponent;
    vector.Y = originalX * xAxisYComponent + originalY * yAxisYComponent;
} 
```

完全了解`RotateVector`方法需要熟悉余弦值和正弦三角函数以及一些线性代数中，这超出了本文的范围。 但是，一次实现`RotateVector`方法可以用于旋转任何向量，包括速度向量。 例如，下面的代码可能激发中指定的方向的项目符号`rotation`值：


```csharp
// Create a Bullet instance
Bullet newBullet = new Bullet();

// Define the velocity of the bullet when 
// rotation is 0
CCVector2 velocity = new CCVector2 (0, 100);

// Modify the velocity according to rotation
RotateVector (ref velocity, rotation);

// Assign the newBullet's velocity using the
// rotated vector
newBullet.VelocityX = velocity.X;
newBullet.VelocityY = velocity.Y;

// Set the bullet's rotation so it faces
// the direction that it's flying
newBullet.Rotation = rotation; 
```

此代码可能会导致类似：

![](math-images/image9.png "此代码可能会产生类似于此屏幕截图")


## <a name="summary"></a>总结

本指南介绍了常见的数学概念，二维游戏开发中。 它演示如何分配和实施速度和加速，并介绍了如何轮换对象和以任意方向移动的向量。
