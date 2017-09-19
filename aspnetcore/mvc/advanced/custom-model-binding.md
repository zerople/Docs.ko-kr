---
title: "사용자 지정 모델 바인딩"
author: ardalis
description: "ASP.NET Core mvc에서 모델 바인딩 사용자 지정합니다."
keywords: "ASP.NET Core, 모델 바인딩, 사용자 지정 모델 바인더"
ms.author: riande
manager: wpickett
ms.date: 04/10/2017
ms.topic: article
ms.assetid: ebd98159-a028-4a94-b06c-43981c79c6be
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 999c4f76354ef6ce07733bbb8b0094be90c03c26
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2017
---
# <a name="custom-model-binding"></a><span data-ttu-id="5dcda-104">사용자 지정 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="5dcda-104">Custom Model Binding</span></span>

<span data-ttu-id="5dcda-105">으로 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5dcda-105">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5dcda-106">모델 바인딩 컨트롤러 작업을 모델 (전달 되는 형식에서 메서드 인수로), 대신 HTTP 요청에 비해 직접 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-106">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="5dcda-107">모델 바인더에서 들어오는 요청 데이터 및 응용 프로그램 모델 간의 매핑을 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-107">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="5dcda-108">개발자 (경우에 일반적으로 사용자 고유의 공급자를 작성할 필요가 없습니다) 사용자 지정 모델 바인더를 구현 하 여 기본 제공 모델 바인딩 기능을 확장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-108">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

[<span data-ttu-id="5dcda-109">GitHub에서 보거나 다운로드 샘플</span><span class="sxs-lookup"><span data-stu-id="5dcda-109">View or download sample from GitHub</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/)

## <a name="default-model-binder-limitations"></a><span data-ttu-id="5dcda-110">기본 모델 바인더 제한 사항</span><span class="sxs-lookup"><span data-stu-id="5dcda-110">Default model binder limitations</span></span>

<span data-ttu-id="5dcda-111">기본 모델 바인더는 대부분의 공용.NET Core 데이터 형식이 지원 및 대부분의 개발자의 요구 사항을 충족 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-111">The default model binders support most of the common .NET Core data types and should meet most developers\` needs.</span></span> <span data-ttu-id="5dcda-112">요청에서 입력 텍스트 기반 모델 형식에 직접 바인딩할 기대 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-112">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="5dcda-113">입력 하기에 바인딩하기 전에 데이터베이스를 변환 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-113">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="5dcda-114">예를 들어 있는 경우 모델 데이터를 조회 하는 데 사용할 수 있는 키입니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-114">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="5dcda-115">키에 따라 데이터를 인출 하는 사용자 지정 모델 바인더를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-115">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="5dcda-116">모델 바인딩 검토</span><span class="sxs-lookup"><span data-stu-id="5dcda-116">Model binding review</span></span>

<span data-ttu-id="5dcda-117">모델 바인딩에서 작동 하는 형식에 대 한 특정 정의 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-117">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="5dcda-118">A *단순 유형* 입력에서 단일 문자열로 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-118">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="5dcda-119">A *복합 형식* 여러 입력된 값에서 변환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-119">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="5dcda-120">프레임 워크의 존재 여부에 따라 차이점을 확인 한 `TypeConverter`합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-120">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="5dcda-121">간단한 있는 경우는 형식 변환기를 만드는 것이 좋습니다 `string`  ->  `SomeType` 외부 리소스를 필요 하지 않은 매핑.</span><span class="sxs-lookup"><span data-stu-id="5dcda-121">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="5dcda-122">사용자 고유의 사용자 지정 모델 바인더를 만들기 전에 검토 방법 기존 모델 가치가 바인더 구현 되는 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-122">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="5dcda-123">고려는 [ByteArrayModelBinder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) base64 인코딩된 문자열을 바이트 배열로 변환에 사용할 수 있는 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-123">Consider the [ByteArrayModelBinder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="5dcda-124">바이트 배열은 종종 파일 또는 데이터베이스 BLOB 필드로 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-124">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="5dcda-125">ByteArrayModelBinder 사용</span><span class="sxs-lookup"><span data-stu-id="5dcda-125">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="5dcda-126">Base64 인코딩 문자열 이진 데이터를 나타내는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-126">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="5dcda-127">예를 들어 다음 이미지는 문자열으로 인코딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-127">For example, the following image can be encoded as a string.</span></span>

<span data-ttu-id="5dcda-128">![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")</span><span class="sxs-lookup"><span data-stu-id="5dcda-128">![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")</span></span>

<span data-ttu-id="5dcda-129">인코딩된 문자열의 작은 부분 다음 그림에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-129">A small portion of the encoded string is shown in the following image:</span></span>

<span data-ttu-id="5dcda-130">![인코딩된 dotnet bot](custom-model-binding/images/encoded-bot.png "dotnet bot 인코딩")</span><span class="sxs-lookup"><span data-stu-id="5dcda-130">![dotnet bot encoded](custom-model-binding/images/encoded-bot.png "dotnet bot encoded")</span></span>

<span data-ttu-id="5dcda-131">지침에 따라는 [샘플의 추가 정보](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) 파일로 base64 인코딩된 문자열을 변환 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-131">Follow the instructions in the [sample's README](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) to convert the base64-encoded string into a file.</span></span>

<span data-ttu-id="5dcda-132">ASP.NET Core MVC base64 인코딩 문자열 하 고 사용할 수는 `ByteArrayModelBinder` 바이트 배열로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-132">ASP.NET Core MVC can take a base64-encoded strings and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="5dcda-133">[ByteArrayModelBinderProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) 구현 하는 [IModelBinderProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) 매핑합니다 `byte[]` 에 대 한 인수 `ByteArrayModelBinder`:</span><span class="sxs-lookup"><span data-stu-id="5dcda-133">The [ByteArrayModelBinderProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) which implements [IModelBinderProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="5dcda-134">사용자 고유의 사용자 지정 모델 바인더를 만들 때 구현할 수 있습니다 직접 `IModelBinderProvider` 입력 하거나 사용 하 여는 [ModelBinderAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinderattribute)합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-134">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the [ModelBinderAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinderattribute).</span></span>

<span data-ttu-id="5dcda-135">다음 예제에서는 사용 하는 방법을 보여 줍니다. `ByteArrayModelBinder` base64 인코딩된 문자열을 변환 하는 `byte[]` 파일로 결과 저장:</span><span class="sxs-lookup"><span data-stu-id="5dcda-135">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

<span data-ttu-id="5dcda-136">와 같은 도구를 사용 하 여이 api 메서드를 base64 인코딩 문자열을 게시할 수 [우체부](https://www.getpostman.com/):</span><span class="sxs-lookup"><span data-stu-id="5dcda-136">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="5dcda-137">![우체부](custom-model-binding/images/postman.png "우체부")</span><span class="sxs-lookup"><span data-stu-id="5dcda-137">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="5dcda-138">바인더는 요청 데이터를 적절 하 게 명명 된 속성 또는 인수를 바인딩할 수,으로 모델 바인딩 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-138">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="5dcda-139">다음 예제에서는 사용 하는 방법을 보여 줍니다. `ByteArrayModelBinder` 보기 모델을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-139">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="5dcda-140">사용자 지정 모델 바인더 샘플</span><span class="sxs-lookup"><span data-stu-id="5dcda-140">Custom model binder sample</span></span>

<span data-ttu-id="5dcda-141">이 섹션에서는 구현 하는 사용자 지정 모델 바인더입니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-141">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="5dcda-142">강력한 형식의 키 인수로 들어오는 요청 데이터를 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-142">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="5dcda-143">Entity Framework Core를 사용 하 여 연결 된 엔터티를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-143">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="5dcda-144">동작 메서드에 연결 된 엔터티를 인수로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-144">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="5dcda-145">다음 샘플에서는 `ModelBinder` 특성에 `Author` 모델:</span><span class="sxs-lookup"><span data-stu-id="5dcda-145">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

<span data-ttu-id="5dcda-146">위의 코드는 `ModelBinder` 특성의 유형을 지정 `IModelBinder` 바인딩할 사용 해야 하는 `Author` 액션 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-146">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span> 

<span data-ttu-id="5dcda-147">`AuthorEntityBinder` 바인딩하는 데 사용 되는 `Author` Entity Framework Core를 사용 하 여 데이터 소스에서 엔터티를 인출 하 여 매개 변수 및 `authorId`:</span><span class="sxs-lookup"><span data-stu-id="5dcda-147">The `AuthorEntityBinder` is used to bind an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

<span data-ttu-id="5dcda-148">다음 코드에서는 사용 하 여 `AuthorEntityBinder` 작업 메서드에서:</span><span class="sxs-lookup"><span data-stu-id="5dcda-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="5dcda-149">`ModelBinder` 적용할 특성을 사용할 수는 `AuthorEntityBinder` 기본 규칙을 사용 하지 않는 매개 변수를:</span><span class="sxs-lookup"><span data-stu-id="5dcda-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that do not use default conventions:</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="5dcda-150">인수의 이름을 기본 아니기 때문에이 예제에서는 `authorId`를 사용 하 여 매개 변수에서 지정 된 `ModelBinder` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-150">In this example, since the name of the argument is not the default `authorId`, it's specified on the parameter using `ModelBinder` attribute.</span></span> <span data-ttu-id="5dcda-151">Note는 컨트롤러 및 작업 메서드는 작업 메서드에서 엔터티를 조회에 비해 간단 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-151">Note that both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="5dcda-152">모델 바인더에 Entity Framework Core를 사용 하 여 작성자를 인출 하는 논리를 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="5dcda-153">만든 모델에 바인딩하고 수행 하는 데 도움이 하는 여러 메서드가 있는 경우 상당히 단순화 수 있습니다는 [건조 원칙](http://deviq.com/don-t-repeat-yourself/)합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-153">This can be considerable simplification when you have several methods that bind to the author model, and can help you to follow the [DRY principle](http://deviq.com/don-t-repeat-yourself/).</span></span>

<span data-ttu-id="5dcda-154">적용할 수 있습니다는 `ModelBinder` 개별 모델 속성을 특성 (같은 viewmodel에서) 또는 특정 모델 바인더 모델에 대 한 또는 해당 형식이 나 작업에만 지정 하는 동작 메서드 매개 변수에 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="5dcda-155">ModelBinderProvider 구현</span><span class="sxs-lookup"><span data-stu-id="5dcda-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="5dcda-156">특성을 적용 하는 대신 구현할 수 있습니다 `IModelBinderProvider`합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="5dcda-157">이 기본 제공 프레임 워크 바인더를 구현 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="5dcda-158">작동 하 여 바인더 형식을 지정 하는 경우를 생성 하는 인수의 형식을 지정 **하지** 입력 프로그램 바인더 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="5dcda-159">다음 바인더 공급자는는 `AuthorEntityBinder`합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="5dcda-160">공급자의 MVC의 컬렉션에 추가 될 때 사용할 필요가 없습니다는 `ModelBinder` 특성을 `Author` 또는 `Author` 매개 변수를 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author` typed parameters.</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="5dcda-161">참고: 위의 코드 반환는 `BinderTypeModelBinder`합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="5dcda-162">`BinderTypeModelBinder`모델 바인더에 대 한 팩터리 역할 하며 종속성 주입 DI ()를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="5dcda-163">`AuthorEntityBinder` DI EF 코어를 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="5dcda-164">사용 하 여 `BinderTypeModelBinder` 모델 바인더 DI에서 서비스를 필요로 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="5dcda-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="5dcda-165">사용자 지정 모델 바인더 공급자를 사용 하려면 추가 `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="5dcda-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

<span data-ttu-id="5dcda-166">모델 바인더를 평가할 때는 공급자의 컬렉션 순서 대로 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="5dcda-167">바인더를 반환 하는 첫 번째 공급자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-167">The first provider that returns a binder is used.</span></span>

<span data-ttu-id="5dcda-168">다음 이미지는 기본 디버거에서 모델 바인더를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-168">The following image shows the default model binders from the debugger.</span></span>

<span data-ttu-id="5dcda-169">![기본 모델 바인더](custom-model-binding/images/default-model-binders.png "기본 모델 바인더")</span><span class="sxs-lookup"><span data-stu-id="5dcda-169">![default model binders](custom-model-binding/images/default-model-binders.png "default model binders")</span></span>

<span data-ttu-id="5dcda-170">사용자 지정 사용자 바인더 되기도 전에 호출 되는 기본 제공 모델 바인더 공급자 컬렉션의 끝에 추가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-170">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="5dcda-171">이 예제에서는 사용자 지정 공급자에 대해 사용 되도록 컬렉션의 시작 부분에 추가 됩니다 `Author` 함수 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-171">In this example, the custom provider is added to the beginning of the collection to ensure it is used for `Author` action arguments.</span></span>

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="5dcda-172">권장 사항 및 모범 사례</span><span class="sxs-lookup"><span data-stu-id="5dcda-172">Recommendations and best practices</span></span>

<span data-ttu-id="5dcda-173">사용자 지정 모델 바인더:</span><span class="sxs-lookup"><span data-stu-id="5dcda-173">Custom model binders:</span></span>
- <span data-ttu-id="5dcda-174">상태 코드를 설정 하거나 결과 반환할 수 없습니다 (예를 들어 404 찾을 수 없음).</span><span class="sxs-lookup"><span data-stu-id="5dcda-174">Should not attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="5dcda-175">모델 바인딩 실패 하면는 [작업 필터](xref:mvc/controllers/filters) 또는 작업 메서드 자체 내에서 논리 오류를 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-175">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="5dcda-176">반복 되는 코드와 작업 방법 중에서 일반적인 문제를 제거 하는 데 가장 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-176">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="5dcda-177">일반적으로 쓰일 수 없습니다 문자열 사용자 지정 형식으로 변환 하는 [ `TypeConverter` ](https://docs.microsoft.com//dotnet/api/system.componentmodel.typeconverter) 는 일반적으로 더 나은 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="5dcda-177">Typically should not be used to convert a string into a custom type, a [`TypeConverter`](https://docs.microsoft.com//dotnet/api/system.componentmodel.typeconverter) is usually a better option.</span></span>
