---
title: "사용자 지정 모델 바인딩"
author: ardalis
description: "ASP.NET Core mvc에서 모델 바인딩 사용자 지정합니다."
keywords: "ASP.NET Core, 모델 바인딩, 사용자 지정 모델 바인더"
ms.author: riande
manager: wpickett
ms.date: 4/10/2017
ms.topic: article
ms.assetid: ebd98159-a028-4a94-b06c-43981c79c6be
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 8dae9cf1597ae2bddf2943af4a7ab9d50620825b
ms.sourcegitcommit: 26166785ad181a8519cb008800d71d96499b0499
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2017
---
# <a name="custom-model-binding"></a>사용자 지정 모델 바인딩

으로 [Steve Smith](http://ardalis.com)

모델 바인딩 컨트롤러 작업을 모델 (전달 되는 형식에서 메서드 인수로), 대신 HTTP 요청에 비해 직접 작업할 수 있습니다. 모델 바인더에서 들어오는 요청 데이터 및 응용 프로그램 모델 간의 매핑을 처리 됩니다. 개발자 (경우에 일반적으로 사용자 고유의 공급자를 작성할 필요가 없습니다) 사용자 지정 모델 바인더를 구현 하 여 기본 제공 모델 바인딩 기능을 확장할 수 있습니다.

[GitHub에서 보거나 다운로드 샘플](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/)

## <a name="default-model-binder-limitations"></a>기본 모델 바인더 제한 사항

기본 모델 바인더는 대부분의 공용.NET Core 데이터 형식이 지원 및 대부분의 개발자의 요구 사항을 충족 해야 합니다. 요청에서 입력 텍스트 기반 모델 형식에 직접 바인딩할 기대 합니다. 입력 하기에 바인딩하기 전에 데이터베이스를 변환 해야 합니다. 예를 들어 있는 경우 모델 데이터를 조회 하는 데 사용할 수 있는 키입니다. 키에 따라 데이터를 인출 하는 사용자 지정 모델 바인더를 사용할 수 있습니다.

## <a name="model-binding-review"></a>모델 바인딩 검토

모델 바인딩에서 작동 하는 형식에 대 한 특정 정의 사용 합니다. A *단순 유형* 입력에서 단일 문자열로 변환 됩니다. A *복합 형식* 여러 입력된 값에서 변환 됩니다. 프레임 워크의 존재 여부에 따라 차이점을 확인 한 `TypeConverter`합니다. 간단한 있는 경우는 형식 변환기를 만드는 것이 좋습니다 `string`  ->  `SomeType` 외부 리소스를 필요 하지 않은 매핑.

사용자 고유의 사용자 지정 모델 바인더를 만들기 전에 검토 방법 기존 모델 가치가 바인더 구현 되는 합니다. 고려는 [ByteArrayModelBinder](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) base64 인코딩된 문자열을 바이트 배열로 변환에 사용할 수 있는 합니다. 바이트 배열은 종종 파일 또는 데이터베이스 BLOB 필드로 저장 됩니다.

### <a name="working-with-the-bytearraymodelbinder"></a>ByteArrayModelBinder 사용

Base64 인코딩 문자열 이진 데이터를 나타내는 데 사용할 수 있습니다. 예를 들어 다음 이미지는 문자열으로 인코딩할 수 있습니다.

![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")

인코딩된 문자열의 작은 부분 다음 그림에 표시 됩니다.

![인코딩된 dotnet bot](custom-model-binding/images/encoded-bot.png "dotnet bot 인코딩")

지침에 따라는 [샘플의 추가 정보](https://github.com/aspnet/Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) 파일로 base64 인코딩된 문자열을 변환 하도록 합니다.

ASP.NET Core MVC base64 인코딩 문자열 하 고 사용할 수는 `ByteArrayModelBinder` 바이트 배열로 변환 합니다. [ByteArrayModelBinderProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) 구현 하는 [IModelBinderProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) 매핑합니다 `byte[]` 에 대 한 인수 `ByteArrayModelBinder`:

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

사용자 고유의 사용자 지정 모델 바인더를 만들 때 구현할 수 있습니다 직접 `IModelBinderProvider` 입력 하거나 사용 하 여는 [ModelBinderAttribute](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.modelbinderattribute)합니다.

다음 예제에서는 사용 하는 방법을 보여 줍니다. `ByteArrayModelBinder` base64 인코딩된 문자열을 변환 하는 `byte[]` 파일로 결과 저장:

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

와 같은 도구를 사용 하 여이 api 메서드를 base64 인코딩 문자열을 게시할 수 [우체부](https://www.getpostman.com/):

![우체부](custom-model-binding/images/postman.png "우체부")

바인더는 요청 데이터를 적절 하 게 명명 된 속성 또는 인수를 바인딩할 수,으로 모델 바인딩 성공 합니다. 다음 예제에서는 사용 하는 방법을 보여 줍니다. `ByteArrayModelBinder` 보기 모델을 사용 합니다.

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a>사용자 지정 모델 바인더 샘플

이 섹션에서는 구현 하는 사용자 지정 모델 바인더입니다.

- 강력한 형식의 키 인수로 들어오는 요청 데이터를 변환합니다.
- Entity Framework Core를 사용 하 여 연결 된 엔터티를 가져옵니다.
- 동작 메서드에 연결 된 엔터티를 인수로 전달합니다.

다음 샘플에서는 `ModelBinder` 특성에 `Author` 모델:

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

위의 코드는 `ModelBinder` 특성의 유형을 지정 `IModelBinder` 바인딩할 사용 해야 하는 `Author` 액션 매개 변수입니다. 

`AuthorEntityBinder` 바인딩하는 데 사용 되는 `Author` Entity Framework Core를 사용 하 여 데이터 소스에서 엔터티를 인출 하 여 매개 변수 및 `authorId`:

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

다음 코드에서는 사용 하 여 `AuthorEntityBinder` 작업 메서드에서:

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

`ModelBinder` 적용할 특성을 사용할 수는 `AuthorEntityBinder` 기본 규칙을 사용 하지 않는 매개 변수를:

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

인수의 이름을 기본 아니기 때문에이 예제에서는 `authorId`를 사용 하 여 매개 변수에서 지정 된 `ModelBinder` 특성입니다. Note는 컨트롤러 및 작업 메서드는 작업 메서드에서 엔터티를 조회에 비해 간단 합니다. 모델 바인더에 Entity Framework Core를 사용 하 여 작성자를 인출 하는 논리를 이동 합니다. 만든 모델에 바인딩하고 수행 하는 데 도움이 하는 여러 메서드가 있는 경우 상당히 단순화 수 있습니다는 [건조 원칙](http://deviq.com/don-t-repeat-yourself/)합니다.

적용할 수 있습니다는 `ModelBinder` 개별 모델 속성을 특성 (같은 viewmodel에서) 또는 특정 모델 바인더 모델에 대 한 또는 해당 형식이 나 작업에만 지정 하는 동작 메서드 매개 변수에 합니다.

### <a name="implementing-a-modelbinderprovider"></a>ModelBinderProvider 구현

특성을 적용 하는 대신 구현할 수 있습니다 `IModelBinderProvider`합니다. 이 기본 제공 프레임 워크 바인더를 구현 하는 방법입니다. 작동 하 여 바인더 형식을 지정 하는 경우를 생성 하는 인수의 형식을 지정 **하지** 입력 프로그램 바인더 허용 합니다. 다음 바인더 공급자는는 `AuthorEntityBinder`합니다. 공급자의 MVC의 컬렉션에 추가 될 때 사용할 필요가 없습니다는 `ModelBinder` 특성을 `Author` 또는 `Author` 매개 변수를 입력 합니다.

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> 참고: 위의 코드 반환는 `BinderTypeModelBinder`합니다. `BinderTypeModelBinder`모델 바인더에 대 한 팩터리 역할 하며 종속성 주입 DI ()를 제공 합니다. `AuthorEntityBinder` DI EF 코어를 액세스할 수 있어야 합니다. 사용 하 여 `BinderTypeModelBinder` 모델 바인더 DI에서 서비스를 필요로 하는 경우.

사용자 지정 모델 바인더 공급자를 사용 하려면 추가 `ConfigureServices`:

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

모델 바인더를 평가할 때는 공급자의 컬렉션 순서 대로 검사 합니다. 바인더를 반환 하는 첫 번째 공급자를 사용 합니다.

다음 이미지는 기본 디버거에서 모델 바인더를 보여 줍니다.

![기본 모델 바인더](custom-model-binding/images/default-model-binders.png "기본 모델 바인더")

사용자 지정 사용자 바인더 되기도 전에 호출 되는 기본 제공 모델 바인더 공급자 컬렉션의 끝에 추가 될 수 있습니다. 이 예제에서는 사용자 지정 공급자에 대해 사용 되도록 컬렉션의 시작 부분에 추가 됩니다 `Author` 함수 인수입니다.

[!code-csharp[Main](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

## <a name="recommendations-and-best-practices"></a>권장 사항 및 모범 사례

사용자 지정 모델 바인더:
- 상태 코드를 설정 하거나 결과 반환할 수 없습니다 (예를 들어 404 찾을 수 없음). 모델 바인딩 실패 하면는 [작업 필터](xref:mvc/controllers/filters) 또는 작업 메서드 자체 내에서 논리 오류를 처리 해야 합니다.
- 반복 되는 코드와 작업 방법 중에서 일반적인 문제를 제거 하는 데 가장 유용 합니다.
- 일반적으로 쓰일 수 없습니다 문자열 사용자 지정 형식으로 변환 하는 [ `TypeConverter` ](https://msdn.microsoft.com/library/ayybcxe5.aspx) 는 일반적으로 더 나은 옵션입니다.
