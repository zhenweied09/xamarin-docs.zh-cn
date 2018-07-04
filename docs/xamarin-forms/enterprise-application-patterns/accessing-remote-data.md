---
title: 访问远程数据
description: 本章介绍 eShopOnContainers 移动应用程序如何从容器化微服务访问数据。
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: a140560731cc68dd85c97dc5a89aedcb32abd405
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242084"
---
# <a name="accessing-remote-data"></a>访问远程数据

许多现代的基于 web 的解决方案进行 web 承载的服务，由 web 服务器，以便为远程客户端应用程序提供功能的使用。 Web 服务公开的操作构成 web API。

客户端应用程序应该能够使用 web API，而不知道如何实现的数据或 API 公开的操作。 这要求该 API 遵守通用标准，使客户端应用程序和 web 服务以使用和客户端应用程序和 web 服务之间交换的数据的结构哪些数据格式上达成一致。

## <a name="introduction-to-representational-state-transfer"></a>具象状态传输简介

具象状态传输 (REST) 是用于构建基于超媒体的分布式的系统体系结构样式。 REST 模型的主要优势是它具有基于开放标准并不将绑定的模型或访问任何特定实现到客户端应用程序的实现。 因此，无法使用 Microsoft ASP.NET Core MVC，实现 REST web 服务，并且无法使用任何语言和工具集，可以生成 HTTP 请求并分析 HTTP 响应开发客户端应用程序。

REST 模型使用导航方案来表示网络，称为资源上的对象和服务。 通常实现 REST 的系统使用 HTTP 协议来传输请求访问这些资源。 在此类系统中，客户端应用程序提交的请求中标识资源的 URI 和 HTTP 方法 （如 GET、 POST、 PUT 或 DELETE），该值指示对该资源执行的操作的形式。 HTTP 请求的正文包含执行该操作所需的任何数据。

> [!NOTE]
> REST 定义无状态的请求模型。 因此，HTTP 请求必须独立，并且可能会以任何顺序出现。

从 REST 响应请求可以使用标准 HTTP 状态代码。 例如，返回有效的数据的请求应时无法找到或删除指定的资源的请求应返回包含 HTTP 状态代码 404 （未找到） 的响应包括 HTTP 响应代码 200 （正常）。

RESTful web API 公开了一组连接资源，并提供核心操作使应用操作这些资源，并轻松地在它们之间导航。 为此，构成典型 RESTful web API 的 Uri 面向的是它公开的数据和使用功能提供通过 HTTP 对此数据进行操作。

提供 HTTP 请求，并在 web 服务器上，相应的响应消息中的客户端应用的数据会以各种格式，称为媒体类型。 当客户端应用程序发送一条消息的正文中返回数据的请求时，它可以指定它可以在中处理的媒体类型`Accept`的请求标头。 如果 web 服务器支持此媒体类型，它可以与包含的响应来应答`Content-Type`指定消息的正文中的数据格式的标头。 然后，它是客户端应用程序，可以分析响应消息并相应地解释消息正文中的结果的责任。

有关 REST 的详细信息，请参阅[API 设计](/azure/architecture/best-practices/api-design/)和[API 实现](/azure/architecture/best-practices/api-implementation/)。

## <a name="consuming-restful-apis"></a>使用 RESTful Api

EShopOnContainers 移动应用程序使用模型-视图-视图模型 (MVVM) 模式，并在应用程序中使用的域实体模式表示的模型元素。 EShopOnContainers 引用应用程序中的控制器和存储库类接受并返回中的许多这些模型对象。 因此，它们用作保持移动应用程序和容器化微服务之间传递的所有数据的数据传输对象 (Dto)。 使用 Dto 将数据传递到和从 web 服务接收数据的主要优势是，通过传输在单个远程调用的更多数据，应用程序可以减少需要进行的远程调用数。

### <a name="making-web-requests"></a>发出 Web 请求

EShopOnContainers 移动应用程序使用`HttpClient`类，以通过 HTTP，使用被用作的媒体类型的 JSON 发出请求。 此类提供了用于以异步方式发送 HTTP 请求功能，并接收从 URI 的 HTTP 响应标识资源。 `HttpResponseMessage`类表示进行 HTTP 请求之后收到从 REST API 的 HTTP 响应消息。 它包含有关响应，包括状态代码、 标头，以及任何正文的信息。 `HttpContent`类表示的 HTTP 正文和内容标头，如`Content-Type`和`Content-Encoding`。 可以使用任一读取内容`ReadAs`方法，如`ReadAsStringAsync`和`ReadAsByteArrayAsync`，取决于数据的格式。

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>发出 GET 请求

`CatalogService`类用于从目录 microservice 管理数据检索过程。 在`RegisterDependencies`中的方法`ViewModelLocator`类，`CatalogService`类注册针对的类型映射为`ICatalogService`与 Autofac 依赖关系注入容器的类型。 然后，当实例`CatalogViewModel`创建类，其构造函数接受`ICatalogService`键入，其中 Autofac 解决后，返回的实例`CatalogService`类。 有关依赖关系注入的详细信息，请参阅[简介依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

图 10-1 显示了用于显示由目录微服务构成从读取目录数据的类的交互`CatalogView`。

[![](accessing-remote-data-images/catalogdata.png "从目录微服务检索数据")](accessing-remote-data-images/catalogdata-large.png#lightbox "目录微服务构成从检索数据")

**图 10-1**： 目录微服务构成从检索数据

当`CatalogView`导航到，`OnInitialize`中的方法`CatalogViewModel`类名。 如下面的代码示例中所示，此方法从目录 microservice，检索目录数据：

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

此方法调用`GetCatalogAsync`方法`CatalogService`注入到的实例`CatalogViewModel`Autofac 通过。 下面的代码示例演示`GetCatalogAsync`方法：

```csharp
public async Task<ObservableCollection<CatalogItem>> GetCatalogAsync()  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.CatalogEndpoint);  
    builder.Path = "api/v1/catalog/items";  
    string uri = builder.ToString();  

    CatalogRoot catalog = await _requestProvider.GetAsync<CatalogRoot>(uri);  
    ...  
    return catalog?.Data.ToObservableCollection();            
}
```

此方法生成用于标识请求将发送到的资源的 URI，并使用`RequestProvider`类用于对资源的 GET HTTP 方法调用返回的结果之前`CatalogViewModel`。 `RequestProvider`类包含提交中标识的资源，指示要对该资源执行的操作的 HTTP 方法的 URI 的形式的请求的功能和执行该操作所需的正文包含任何数据。 有关如何信息`RequestProvider`类注入到`CatalogService class`，请参阅[简介依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

下面的代码示例演示`GetAsync`中的方法`RequestProvider`类：

```csharp
public async Task<TResult> GetAsync<TResult>(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    HttpResponseMessage response = await httpClient.GetAsync(uri);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>   
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

此方法调用`CreateHttpClient`方法，返回的实例`HttpClient`使用适当的标头集的类。 然后将提交对由 URI，标识与存储在响应的资源的异步 GET 请求`HttpResponseMessage`实例。 `HandleResponse`然后调用方法，这将引发异常，如果响应不包括为成功，则 HTTP 状态代码。 然后为字符串，转换从到 JSON 中读取的响应`CatalogRoot`对象，并返回到`CatalogService`。

`CreateHttpClient`方法下面的代码示例中所示：

```csharp
private HttpClient CreateHttpClient(string token = "")  
{  
    var httpClient = new HttpClient();  
    httpClient.DefaultRequestHeaders.Accept.Add(  
        new MediaTypeWithQualityHeaderValue("application/json"));  

    if (!string.IsNullOrEmpty(token))  
    {  
        httpClient.DefaultRequestHeaders.Authorization =   
            new AuthenticationHeaderValue("Bearer", token);  
    }  
    return httpClient;  
}
```

此方法创建的新实例`HttpClient`类，并设置`Accept`标头的所做的任何请求`HttpClient`到实例`application/json`，这指示它期望任何要设置格式使用 JSON 的响应的内容。 然后，如果作为参数传递了访问令牌`CreateHttpClient`方法，它将添加到`Authorization`标头的所做的任何请求`HttpClient`实例，使用字符串作为前缀`Bearer`。 有关授权的详细信息，请参阅[授权](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

当`GetAsync`中的方法`RequestProvider`类调用`HttpClient.GetAsync`、`Items`中的方法`CatalogController`调用 Catalog.API 项目中的类时，这下面的代码示例中所示：

```csharp
[HttpGet]  
[Route("[action]")]  
public async Task<IActionResult> Items(  
    [FromQuery]int pageSize = 10, [FromQuery]int pageIndex = 0)  
{  
    var totalItems = await _catalogContext.CatalogItems  
        .LongCountAsync();  

    var itemsOnPage = await _catalogContext.CatalogItems  
        .OrderBy(c=>c.Name)  
        .Skip(pageSize * pageIndex)  
        .Take(pageSize)  
        .ToListAsync();  

    itemsOnPage = ComposePicUri(itemsOnPage);  
    var model = new PaginatedItemsViewModel<CatalogItem>(  
        pageIndex, pageSize, totalItems, itemsOnPage);             

    return Ok(model);  
}
```

此方法从使用 EntityFramework，SQL 数据库中检索目录数据和将其作为响应消息，其中包含为成功，则 HTTP 状态代码返回，集合的 JSON 格式`CatalogItem`实例。

#### <a name="making-a-post-request"></a>发出 POST 请求

`BasketService`类用于管理数据检索和更新与购物篮微服务构成的过程。 在`RegisterDependencies`中的方法`ViewModelLocator`类，`BasketService`类注册针对的类型映射为`IBasketService`与 Autofac 依赖关系注入容器的类型。 然后，当实例`BasketViewModel`创建类，其构造函数接受`IBasketService`键入，其中 Autofac 解决后，返回的实例`BasketService `类。 有关依赖关系注入的详细信息，请参阅[简介依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

图 10-2 显示的类，用于发送所显示的购物篮数据交互`BasketView`，到篮微服务。

[![](accessing-remote-data-images/basketdata.png "将数据发送到篮 microservice")](accessing-remote-data-images/basketdata-large.png#lightbox "将数据发送到篮微服务")

**图 10-2**： 将数据发送到篮微服务

当某一项添加到购物篮，`ReCalculateTotalAsync`中的方法`BasketViewModel`类名。 此方法将更新购物篮中的项的总计值并将购物篮数据发送到篮微服务，如下面的代码示例中所示：

```csharp
private async Task ReCalculateTotalAsync()  
{  
    ...  
    await _basketService.UpdateBasketAsync(new CustomerBasket  
    {  
        BuyerId = userInfo.UserId,   
        Items = BasketItems.ToList()  
    }, authToken);  
}
```

此方法调用`UpdateBasketAsync`方法`BasketService`注入到的实例`BasketViewModel`Autofac 通过。 下面的方法演示`UpdateBasketAsync`方法：

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

此方法生成用于标识请求将发送到的资源的 URI，并使用`RequestProvider`类用于对资源的 POST HTTP 方法调用返回的结果之前`BasketViewModel`。 请注意，在身份验证过程中，从 IdentityServer 获取访问令牌，需要购物篮微服务的请求进行授权。 有关授权的详细信息，请参阅[授权](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下面的代码示例演示之一`PostAsync`中的方法`RequestProvider`类：

```csharp
public async Task<TResult> PostAsync<TResult>(  
    string uri, TResult data, string token = "", string header = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    ...  
    var content = new StringContent(JsonConvert.SerializeObject(data));  
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");  
    HttpResponseMessage response = await httpClient.PostAsync(uri, content);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>  
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

此方法调用`CreateHttpClient`方法，返回的实例`HttpClient`使用适当的标头集的类。 然后将提交到由 URI，标识与所发送 JSON 格式和存储在响应中的序列化的购物篮数据资源的异步 POST 请求`HttpResponseMessage`实例。 `HandleResponse`然后调用方法，这将引发异常，如果响应不包括为成功，则 HTTP 状态代码。 然后，为字符串，转换从到 JSON 中读取的响应`CustomerBasket`对象，并返回到`BasketService`。 有关详细信息`CreateHttpClient`方法，请参阅[进行获取请求](#making_a_get_request)。

当`PostAsync`中的方法`RequestProvider`类调用`HttpClient.PostAsync`、`Post`中的方法`BasketController`调用 Basket.API 项目中的类时，这下面的代码示例中所示：

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

此方法使用的实例`RedisBasketRepository`类以购物篮将数据保存到 Redis 缓存中，并将其返回响应消息，其中包含为成功，则 HTTP 状态代码和 JSON 的格式相同`CustomerBasket`实例。

#### <a name="making-a-delete-request"></a>发出删除请求

图 10-3 显示了为删除从购物篮 microservice，购物篮数据的类的交互`CheckoutView`。

![](accessing-remote-data-images/checkoutdata.png "从购物篮 microservice 删除数据")

**图 10-3**： 从购物篮 microservice 删除数据

在结帐过程调用时，`CheckoutAsync`中的方法`CheckoutViewModel`类名。 此方法创建一个新的顺序之前清除购物篮中，如下面的代码示例中所示,：

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

此方法调用`ClearBasketAsync`方法`BasketService`注入到的实例`CheckoutViewModel`Autofac 通过。 下面的方法演示`ClearBasketAsync`方法：

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

此方法生成用于标识请求将发送到的资源的 URI，并使用`RequestProvider`类来调用该资源上的 DELETE HTTP 方法。 请注意，在身份验证过程中，从 IdentityServer 获取访问令牌，需要购物篮微服务的请求进行授权。 有关授权的详细信息，请参阅[授权](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization)。

下面的代码示例演示`DeleteAsync`中的方法`RequestProvider`类：

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

此方法调用`CreateHttpClient`方法，返回的实例`HttpClient`使用适当的标头集的类。 然后，它将提交至 uri 标识的资源的异步删除请求。 有关详细信息`CreateHttpClient`方法，请参阅[进行获取请求](#making_a_get_request)。

当`DeleteAsync`中的方法`RequestProvider`类调用`HttpClient.DeleteAsync`、`Delete`中的方法`BasketController`调用 Basket.API 项目中的类时，这下面的代码示例中所示：

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

此方法使用的实例`RedisBasketRepository`类从 Redis 缓存中删除购物篮数据。

## <a name="caching-data"></a>缓存数据

可以通过缓存到位于接近的快速存储经常访问的数据提高应用性能的应用程序。 如果快速存储位于更靠近应用于原始的源，则缓存可以显著提高响应时间时检索数据。

最常见的缓存形式是事后缓存，其中应用程序通过引用缓存检索数据。 如果数据不在缓存中，它从数据存储区检索并添加到缓存。 应用程序可以实现事后缓存与缓存端模式。 此模式确定项是否处于当前在缓存中。 如果项目不在缓存中，它从数据存储读取并添加到缓存。 有关详细信息，请参阅[缓存端](/azure/architecture/patterns/cache-aside/)模式。

> [!TIP]
> 频繁读取，并且不经常更改的缓存数据。 此数据可以添加到按需第一次它将检索应用程序的缓存。 这意味着应用程序需要从数据存储中提取数据一次，并且可以通过使用缓存来满足该后续的访问权限。

分布式应用程序，如 eShopOnContainers 引用应用程序，应提供一个或两个以下的缓存：

-   可由多个进程或计算机访问共享的缓存。
-   专用缓存，其中的数据保存本地运行应用程序的设备。

EShopOnContainers 移动应用程序使用专用缓存，其中的数据保存本地设备运行的应用程序实例。 有关使用 eShopOnContainers 引用应用程序的缓存的信息，请参阅[.NET 微服务： 为容器化.NET 应用程序的体系结构](https://aka.ms/microservicesebook)。

> [!TIP]
> 将缓存视为可能随时消失的暂时性数据存储。 确保数据都保留在原始数据存储，以及缓存。 如果缓存变为不可用，丢失数据的风险然后降至最低。

### <a name="managing-data-expiration"></a>管理数据到期

它是不切实际的缓存的数据将始终会与原始数据一致。 原始数据存储区中的数据可能会更改后它已缓存，从而导致缓存的数据变得陈旧。 因此，应用应实现的策略，可帮助确保在缓存中的数据是尽可能保持最新状态，但可以检测和处理缓存中的数据没有变得陈旧时出现的情况。 最缓存机制启用缓存配置为过期数据，并因此缩短为其数据可能已过期的期。

> [!TIP]
> 设置默认过期时间时配置缓存。 许多缓存实现到期，其数据失效并将其删除从缓存中，如果不能对指定的期间访问。 但是，必须小心选择过期时间时。 如果它进行太短，数据将很快过期，并且将降低缓存的优势。 如果它由太长，正逐渐变旧的数据风险。 因此，到期时间应与匹配使用数据的应用程序的访问的模式。

当缓存的数据过期，它应从缓存中，且应用程序必须检索与原始数据的数据存储，并将其放回缓存。

还有可能缓存可能会填满数据允许太长一段时间内保持不变。 因此，将新项添加到缓存的请求可能需要在该过程称为中删除某些项*逐出*。 缓存服务通常逐出最少最近使用的基础上的数据。 但是，有其他逐出策略，包括最近的使用，和后进先出。有关详细信息，请参阅[缓存指南](/azure/architecture/best-practices/caching/)。

<a name="caching_images" />

### <a name="caching-images"></a>缓存的映像

EShopOnContainers 移动应用程序使用从正在缓存中受益的远程产品图像。 这些映像将由[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)控件，与`CachedImage`提供管理[FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/)库。

Xamarin.Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)控件支持的已下载图像缓存。 默认情况下，启用缓存，并将在 24 小时内的本地存储的映像。 此外，可以使用配置的过期时间[ `CacheValidity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CacheValidity/)属性。 有关详细信息，请参阅[下载图像缓存](~/xamarin-forms/user-interface/images.md#Image_Caching)。

FFImageLoading 的`CachedImage`控件是 Xamarin.Forms 的替代[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)控件，提供启用补充功能的其他属性。 在此功能，该控件提供可配置缓存，同时支持错误并加载图像占位符。 下面的代码示例演示如何 eShopOnContainers 移动应用程序使用`CachedImage`中控制`ProductTemplate`，后者是使用的数据模板[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)中控制`CatalogView`:

```xaml
<ffimageloading:CachedImage
    Grid.Row="0"
    Source="{Binding PictureUri}"     
    Aspect="AspectFill">
    <ffimageloading:CachedImage.LoadingPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="default_campaign" />
            <On Platform="UWP" Value="Assets/default_campaign.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.LoadingPlaceholder>
    <ffimageloading:CachedImage.ErrorPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="noimage" />
            <On Platform="UWP" Value="Assets/noimage.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.ErrorPlaceholder>
</ffimageloading:CachedImage>
```

`CachedImage`控件将设置`LoadingPlaceholder`和`ErrorPlaceholder`到特定于平台的映像的属性。 `LoadingPlaceholder`属性指定所指定的图像时显示的图像`Source`检索属性，与`ErrorPlaceholder`属性指定出错时尝试检索图像时显示的图像指定的`Source`属性。

顾名思义，`CachedImage`控件的值指定的时间将缓存在设备上的远程图像`CacheDuration`属性。 当未显式设置此属性的值时，被应用 30 天的默认值。

## <a name="increasing-resilience"></a>增加复原能力

与远程服务和资源进行通信的所有应用都必须对暂时性故障敏感。 暂时性故障包括瞬间断开网络连接到服务，暂时不可用的服务或当服务繁忙时出现超时。 这些故障通常是自我纠正，而且如果适合延迟后重复操作，则很可能会成功。

暂时性故障可以察觉到质量的应用程序，具有很大的影响，即使已在所有可预见的情况下全面测试。 若要确保的应用程序与远程服务进行通信，可靠地运行，必须能够执行所有以下操作：

-   检测到故障的发生，并确定是否可能是暂时性错误。
-   如果确定错误很可能是暂时的并跟踪操作重试次数，重试该操作。
-   使用适当的重试策略，用于指定数目的重试次数、 每次尝试和操作尝试失败后要采取之间的延迟。

可以通过包装所有尝试访问远程服务代码中实现重试模式来实现此暂时性故障处理。

### <a name="retry-pattern"></a>重试模式

如果它尝试将请求发送到远程服务时，应用程序检测到故障，它可以在任何通过以下方式来处理失败：

-   重试该操作。 应用程序无法立即重试失败请求。
-   重试该操作的延迟后。 应用程序应等待适合重试请求之前的时间量。
-   取消该操作。 应用程序应取消操作并报告异常。

重试策略应进行优化以匹配应用程序的业务要求。 例如，务必优化重试计数，然后重试间隔传输到正在尝试的操作。 如果该操作是用户交互的一部分，重试间隔应该较短，且仅几次重试尝试以避免让用户等待响应。 如果该操作是长时间运行的工作流的一部分其中取消或重新启动工作流代价高昂或耗时，它适合再尝试之间的等待并重试更多次。

> [!NOTE]
> 积极重试策略，并尝试及大量的重试次数，之间的最小延迟可能会降低接近或达到容量运行的远程服务。 此外，重试策略可能还会影响应用的响应能力如果不断尝试执行失败操作。

如果某个请求仍然失败重试次数后，最好是为应用程序来防止转到同一资源的后续请求，并报告故障。 然后后一个设定的段，, 应用程序可以对一个或多个请求的资源，以查看它们是否成功。 有关详细信息，请参阅[断路器模式](#circuit_breaker_pattern)。

> [!TIP]
> 永远不会实现一个无限的重试机制。 使用有限数量的重试，或实现[断路器](/azure/architecture/patterns/circuit-breaker/)模式，以允许服务可以恢复。

EShopOnContainers 移动应用程序目前不实施重试模式，发出 rest 样式 web 请求时。 但是，`CachedImage`由提供的控件[FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/)库支持通过重试映像加载的暂时性故障处理。 如果加载的映像失败，则将进行进一步的尝试。 指定的尝试次数`RetryCount`属性，然后重试将在之后通过指定的延迟`RetryDelay`属性。 如果这些属性值不显式设置，其默认值应用 – 3`RetryCount`属性，并为 250ms年`RetryDelay`属性。 有关详细信息`CachedImage`控制，请参阅[缓存映像](#caching_images)。

EShopOnContainers 引用应用程序未实现重试模式。 有关详细信息，包括如何组合使用的重试模式的讨论`HttpClient`类，请参阅[.NET 微服务： 为容器化.NET 应用程序的体系结构](https://aka.ms/microservicesebook)。

有关重试模式的详细信息，请参阅[重试](/azure/architecture/patterns/retry/)模式。

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>断路器模式

在某些情况下，错误可能导致需要更长时间，若要修复的预期事件。 这些故障的范围可以从部分连接断开到服务的完整失败。 在这些情况下，它是应用程序无法重试不太可能成功，而是应接受的操作已失败并相应地处理这种失败的操作毫无意义。

断路器模式可以重复尝试执行的操作，则可能会失败，同时还可以实现应用程序以检测是否已经解决错误阻止应用程序。

> [!NOTE]
> 断路器模式的目的是不同的重试模式。 重试模式使应用可以在重试操作将成功的假定条件。 断路器模式可防止应用程序执行的操作，则可能会失败。

断路器充当代理的操作，可能会失败。 该代理应监视已发生的最新失败的次数，并使用此信息来决定是否允许该操作，若要继续，或立即返回异常。

EShopOnContainers 移动应用程序目前不实施断路器模式。 但是，eShopOnContainers 执行。 有关详细信息，请参阅[.NET 微服务： 为容器化.NET 应用程序的体系结构](https://aka.ms/microservicesebook)。

> [!TIP]
> 将组合的重试和断路器模式。 应用程序可以通过使用重试模式来调用通过断路器运算组合的重试和断路器模式。 但是，重试逻辑应返回断路器的任何异常对于敏感，并且如果断路器指示故障不是暂时性放弃重试尝试。

断路器模式有关的详细信息，请参阅[断路器](/azure/architecture/patterns/circuit-breaker/)模式。

## <a name="summary"></a>总结

许多现代的基于 web 的解决方案进行 web 承载的服务，由 web 服务器，以便为远程客户端应用程序提供功能的使用。 Web 服务公开的操作构成 web API，并且客户端应用程序应该能够利用 web API，而不必知道如何实现的数据或 API 公开的操作。

可以通过缓存到位于接近的快速存储经常访问的数据提高应用性能的应用程序。 应用程序可以实现事后缓存与缓存端模式。 此模式确定项是否处于当前在缓存中。 如果项目不在缓存中，它从数据存储读取并添加到缓存。

当与 web Api 通信时，应用程序必须对暂时性故障敏感。 暂时性故障包括瞬间断开网络连接到服务，暂时不可用的服务或当服务繁忙时出现超时。 这些故障通常是自我纠正，并在适当的延迟后重复操作，则它可能会成功。 因此，应用程序应自动访问 web API 实现暂时性故障处理机制的代码中的所有尝试换都行。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
