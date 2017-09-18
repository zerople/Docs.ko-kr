---
title: "ASP.NET Core의 구성"
author: rick-anderson
description: "여러 원본에서 ASP.NET Core 응용 프로그램을 구성 하는 구성 API를 사용 하는 방법에 알아봅니다."
keywords: "ASP.NET Core, 구성, JSON, 구성"
ms.author: riande
manager: wpickett
ms.date: 6/24/2017
ms.topic: article
ms.assetid: b3a5984d-e172-42eb-8a48-547e4acb6806
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/configuration
ms.openlocfilehash: 7d591259587766a932a14bb030c76274101d16ac
ms.sourcegitcommit: f8f6b5934bd071a349f5bc1e389365c52b1c00fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2017
---
# <a name="configuration-in-aspnet-core"></a>ASP.NET Core의 구성

[Rick Anderson](https://twitter.com/RickAndMSFT), [표시 Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](https://ardalis.com/), 및 [김 Roth](https://github.com/danroth27)

구성 API 앱 이름-값 쌍의 목록에 따라 구성 하는 방법을 제공 합니다. 여러 원본에서 런타임에 구성 읽기입니다. 여러 수준 계층에 이름-값 쌍을 그룹화 할 수 있습니다. 에 대 한 구성 공급자가 있습니다.

* 파일 형식 (INI, JSON 및 XML)
* 명령줄 인수
* 환경 변수
* 메모리 내.NET 개체
* 암호화 된 사용자 저장소
* [Azure 주요 자격 증명 모음](xref:security/key-vault-configuration)
* 사용자 지정 공급자가 설치 또는 만들기

각 구성 값 문자열 키에 매핑됩니다. 사용자 지정 설정을 deserialize 하는 데 기본 바인딩인 지원은 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 개체 (속성이 있는 간단한.NET 클래스).

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/sample)

## <a name="simple-configuration"></a>단순 구성

다음 콘솔 응용 프로그램의 JSON 구성 공급자를 사용합니다.

[!code-csharp[Main](configuration/sample/ConfigJson/Program.cs)]

응용 프로그램을 읽고 다음 구성 설정을 표시 합니다.

[!code-json[Main](configuration/sample/ConfigJson/appsettings.json)]

구성 노드는 콜론으로 구분 하는 이름-값 쌍의 계층적 목록으로 구성 합니다. 액세스 값을 검색 하는 `Configuration` 인덱서를 해당 항목의 키:

```csharp
Console.WriteLine($"option1 = {Configuration["subsection:suboption1"]}");
```

JSON 형식 구성 소스에서 배열 작업을 콜론으로 구분 된 문자열의 일부로 배열 인덱스를 사용 합니다. 다음 예제에서는 앞의 첫 번째 항목의 이름을 가져옵니다 `wizards` 배열:

```csharp
Console.Write($"{Configuration["wizards:0:Name"]}, ");
```

하지만 기본 제공에 기록 하는 이름-값 쌍의 `Configuration` 공급자는 **하지** 유지 만들 수 있습니다 값을 저장 하는 사용자 지정 공급자입니다. 참조 [사용자 지정 구성 공급자](xref:fundamentals/configuration#custom-config-providers)합니다.

앞에 나온 샘플 값을 읽을 구성 인덱서를 사용 합니다. 외부 액세스 구성으로 `Startup`를 사용 하 여는 [옵션 패턴](xref:fundamentals/configuration#options-config-objects)합니다. *옵션 패턴* 이 문서의 뒷부분에 표시 됩니다.

예를 들어, 개발, 테스트 및 프로덕션 환경에 대 한 서로 다른 구성 설정을 일반적이. `CreateDefaultBuilder` ASP.NET Core 2.x 응용 프로그램의 확장 메서드 (또는 사용 하 여 `AddJsonFile` 및 `AddEnvironmentVariables` ASP.NET Core 1.x 응용 프로그램에서 직접) JSON 파일 및 시스템 구성 소스 읽기에 대 한 구성 공급자를 추가 합니다.

* *appsettings.json*
* *appsettings 합니다. \<EnvironmentName >.json*
* 환경 변수

참조 [AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions) 에 대 한 설명은 매개 변수입니다. `reloadOnChange`ASP.NET Core 1.1에서 이상 에서만 지원 됩니다. 

지정 된 순서 구성 소스를 읽습니다. 위의 코드에서 환경 변수 마지막 읽혀집니다. 두 이전 공급자에 설정 된 환경을 통해 설정 된 구성 값 바꾸어야 합니다.

환경 중 하나에 일반적으로 설정 되어 `Development`, `Staging`, 또는 `Production`합니다. 참조 [여러 환경 작업](environments.md) 자세한 정보에 대 한 합니다.

구성 고려 사항:

* `IOptionsSnapshot`변경 시 구성 데이터를 다시 로드할 수 있습니다. 사용 하 여 `IOptionsSnapshot` 구성 데이터를 다시 로드 하는 경우.  참조 [IOptionsSnapshot](#ioptionssnapshot) 자세한 정보에 대 한 합니다.
* 구성 키 대/소문자를 구분 합니다.
* 가장 좋은 방법은 로컬 환경에서 배포 된 구성 파일을 설정 하는 아무 것도 재정의할 수 있도록 마지막으로, 환경 변수를 지정 하는 것입니다.
* **하지** 구성 공급자 코드에 또는 일반 텍스트로 구성 파일에 암호 또는 기타 중요 한 데이터를 저장 합니다. 테스트 환경 하거나 사용자가 개발에서 생산 암호를 사용 하지 마십시오. 대신 저장소로 실수로 커밋된 없습니다 하므로 프로젝트 트리 외부 암호를 지정 합니다. 에 대 한 자세한 내용은 [여러 환경 작업](environments.md) 및 관리 [개발 하는 동안 앱 암호의 안전한 저장소](../security/app-secrets.md)합니다.
* 경우 `:` 수 없습니다 시스템에서 환경 변수에서 사용 되는 대체 `:` 와 `__` (이중 밑줄).

<a name=options-config-objects></a>

## <a name="using-options-and-configuration-objects"></a>옵션 및 구성 개체를 사용 하 여

옵션 패턴을 관련된 설정 그룹을 나타내는 사용자 지정 옵션 클래스를 사용 합니다. 앱 내에서 각 기능에 대해 분리 된 클래스를 만드는 것이 좋습니다. 분리 된 클래스를 수행합니다.

* [인터페이스 분리 원칙 (ISP)](http://deviq.com/interface-segregation-principle/) : 클래스를 사용 하는 구성 설정에 따라서만 결정 합니다.
* [문제의 분리](http://deviq.com/separation-of-concerns/) : 응용 프로그램의 다른 부분에 대 한 설정을 종속 또는 서로 결합 합니다.

옵션 클래스는 추상 이어야 합니다. 매개 변수가 없는 public 생성자를 사용 합니다. 예:

[!code-csharp[Main](configuration/sample/UsingOptions/Models/MyOptions.cs)]

<a name=options-example></a>

다음 코드에서 JSON 구성 공급자가 사용 됩니다. `MyOptions` 클래스는 서비스 컨테이너에 추가 되 고 구성에 연결 합니다.

[!code-csharp[Main](configuration/sample/UsingOptions/Startup.cs?name=snippet1&highlight=8,20-21)]

다음 [컨트롤러](../mvc/controllers/index.md) 사용 하 여 [생성자 종속성 주입](xref:fundamentals/dependency-injection#what-is-dependency-injection) 에 [ `IOptions<TOptions>` ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.options.ioptions-1) 설정에 액세스 하려면:

[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController.cs?name=snippet1)]

다음으로 *appsettings.json* 파일:

[!code-json[Main](configuration/sample/UsingOptions/appsettings1.json)]

`HomeController.Index` 메서드 반환 `option1 = value1_from_json, option2 = 2`합니다.

일반적인 앱 단일 옵션 파일에 전체 구성을 바인딩 되지 않습니다. 나중에 사용 하는 방법을 보여 드리 려 `GetSection` 섹션에 바인딩할 합니다.

다음 코드에서 두 번째 `IConfigureOptions<TOptions>` 서비스를 서비스 컨테이너에 추가 합니다. 대리자를 사용 하 여 바인딩을 구성 `MyOptions`합니다.

[!code-csharp[Main](configuration/sample/UsingOptions/Startup2.cs?name=snippet1&highlight=9-13)]

여러 구성 공급자를 추가할 수 있습니다. 구성 공급자는 NuGet 패키지에서 사용할 수 있습니다. 등록 된 순서에 적용 됩니다.

호출할 때마다 `Configure<TOptions>` 추가 `IConfigureOptions<TOptions>` 서비스를 서비스 컨테이너입니다. 위의 예제에서는 값 `Option1` 및 `Option2` 에 지정 된 *appsettings.json* -하지만의 값 `Option1` 구성 된 대리자가 재정의 됩니다. 

둘 이상의 구성 서비스를 사용 하는 경우 마지막 구성 소스 지정 "wins" (구성 값을 설정). 위의 코드는 `HomeController.Index` 메서드 반환 `option1 = value1_from_action, option2 = 2`합니다.

각 속성에 옵션 유형 폼의 구성 키에 바인딩된 구성 옵션을 바인딩할 때 `property[:sub-property:]`합니다. 예를 들어는 `MyOptions.Option1` 속성의 키에 바인딩된 `Option1`에서 읽음는 `option1` 속성 *appsettings.json*합니다. 하위 속성 샘플은이 문서의 뒷부분에 표시 됩니다.

다음 코드에서는 세 번째 `IConfigureOptions<TOptions>` 서비스를 서비스 컨테이너에 추가 합니다. 바인딩할 `MySubOptions` 섹션 `subsection` 의 *appsettings.json* 파일:

[!code-csharp[Main](configuration/sample/UsingOptions/Startup3.cs?name=snippet1&highlight=16-17)]

참고:이 확장 방법을 사용 하려면는 `Microsoft.Extensions.Options.ConfigurationExtensions` NuGet 패키지 합니다.

다음을 사용 하 여 *appsettings.json* 파일:

[!code-json[Main](configuration/sample/UsingOptions/appsettings.json)]

`MySubOptions` 클래스:

[!code-csharp[Main](configuration/sample/UsingOptions/Models/MySubOptions.cs?name=snippet1)]

다음으로 `Controller`:

[!code-csharp[Main](configuration/sample/UsingOptions/Controllers/HomeController2.cs?name=snippet1)]

`subOption1 = subvalue1_from_json, subOption2 = 200`이 반환 됩니다.

옵션을 보기 모델에 제공 하거나 삽입할 수도 `IOptions<TOptions>` 보기에 직접:

[!code-html[Main](configuration/sample/UsingOptions/Views/Home/Index.cshtml?highlight=3-4,16-17,20-21)]

<a name=in-memory-provider></a>

## <a name="ioptionssnapshot"></a>IOptionsSnapshot

*ASP.NET Core 1.1 이상이 필요합니다.*

`IOptionsSnapshot`구성 파일 변경 될 때 구성 데이터를 다시 로드를 지원 합니다. 오버 헤드가 최소로 유지가 된 있습니다. 사용 하 여 `IOptionsSnapshot` 와 `reloadOnChange: true`, 옵션에 바인딩된 `IConfiguration` 변경 될 때 다시 로드 합니다.

다음 샘플에는 새로운 방법을 보여 줍니다 `IOptionsSnapshot` 후에 만들어진 *config.json* 변경 합니다. 동일한 서버에 요청을 반환 합니다 시간 *config.json* 가 **하지** 변경 합니다. 첫 번째 요청 이후 *config.json* 변경 시간을 표시 합니다.

[!code-csharp[Main](configuration/sample/IOptionsSnapshot2/Startup.cs?name=snippet1&highlight=1-9,13-18,32,33,52,53)]

다음 그림에서는 서버 출력을 보여 줍니다.

![이미지 표시 된 브라우저 "마지막 업데이트: 2016 년 11 월 22 일 오후 4시 43분"](configuration/_static/first.png)

메시지 값 또는 표시 된 시간이 변경 되지 않습니다 브라우저 새로 고침 (때 *config.json* 변경 되지 않은).

변경 하 고 저장 된 *config.json* 후 브라우저를 새로 고칩니다.

![이미지 표시 된 브라우저 "마지막으로 업데이트를 e: 2016 년 11 월 22 일 오후 4시 53분"](configuration/_static/change.png)

## <a name="in-memory-provider-and-binding-to-a-poco-class"></a>메모리 내 공급자 및 POCO 클래스에 바인딩

다음 샘플에는 클래스에 바인딩하고 메모리 내 공급자를 사용 하는 방법을 보여 줍니다.

[!code-csharp[Main](configuration/sample/InMemory/Program.cs)]

구성 값을 문자열로 반환 됩니다 있지만 바인딩 개체의 생성을 사용 합니다. 바인딩은 POCO 개체 또는 전체 개체 그래프를 검색할 수 있습니다. 다음 샘플에서는에 바인딩하는 방법을 보여 줍니다. `MyWindow` ASP.NET Core MVC 응용 프로그램으로 옵션 패턴을 사용 하 고 있습니다.

[!code-csharp[Main](configuration/sample/WebConfigBind/MyWindow.cs)]

[!code-json[Main](configuration/sample/WebConfigBind/appsettings.json)]

사용자 지정 클래스 바인딩 `ConfigureServices` 호스트를 작성할 때:

[!code-csharp[Main](configuration/sample/WebConfigBind/Program.cs?name=snippet1&highlight=3-4)]

설정을 표시는 `HomeController`:

[!code-csharp[Main](configuration/sample/WebConfigBind/Controllers/HomeController.cs)]

### <a name="getvalue"></a>GetValue

다음 샘플을 참조 하십시오.는 [GetValue<T> ](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationbinder#Microsoft_Extensions_Configuration_ConfigurationBinder_GetValue_Microsoft_Extensions_Configuration_IConfiguration_System_Type_System_String_System_Object_) 확장 메서드:

[!code-csharp[Main](configuration/sample/InMemoryGetValue/Program.cs?highlight=27-29)]

ConfigurationBinder `GetValue<T>` 메서드 기본값 (이 샘플에는 80)을 지정할 수 있습니다. `GetValue<T>`간단한 시나리오 되며 전체 섹션에 바인딩되지 않습니다. `GetValue<T>`스칼라 값을 가져옵니다 `GetSection(key).Value` 특정 형식으로 변환 합니다.

## <a name="binding-to-an-object-graph"></a>개체 그래프에 바인딩

클래스의 각 개체를 재귀적으로 바인딩할을 수 있습니다. 다음 사항을 고려 `AppOptions` 클래스:

[!code-csharp[Main](configuration/sample/ObjectGraph/AppOptions.cs)]

다음 샘플에 바인딩하는 `AppOptions` 클래스:

[!code-csharp[Main](configuration/sample/ObjectGraph/Program.cs?highlight=15-16)]

**ASP.NET Core 1.1** 및 이상을 사용 하 여 `Get<T>`, 전체 섹션와 함께 작업 하 합니다. `Get<T>`사용 하 여 보다 자세한 convienent 수 `Bind`합니다. 다음 코드를 사용 하는 방법을 보여 줍니다 `Get<T>` 위의 샘플:

```csharp
var appConfig = config.GetSection("App").Get<AppOptions>();
```

다음을 사용 하 여 *appsettings.json* 파일:

[!code-json[Main](configuration/sample/ObjectGraph/appsettings.json)]

프로그램은 표시 `Height 11`합니다.

다음 코드를 장치에 사용할 수 구성을 테스트 합니다.

```csharp
[Fact]
public void CanBindObjectTree()
{
    var dict = new Dictionary<string, string>
        {
            {"App:Profile:Machine", "Rick"},
            {"App:Connection:Value", "connectionstring"},
            {"App:Window:Height", "11"},
            {"App:Window:Width", "11"}
        };
    var builder = new ConfigurationBuilder();
    builder.AddInMemoryCollection(dict);
    var config = builder.Build();

    var options = new AppOptions();
    config.GetSection("App").Bind(options);

    Assert.Equal("Rick", options.Profile.Machine);
    Assert.Equal(11, options.Window.Height);
    Assert.Equal(11, options.Window.Width);
    Assert.Equal("connectionstring", options.Connection.Value);
}
```

<a name=custom-config-providers></a>

## <a name="basic-sample-of-entity-framework-custom-provider"></a>Entity Framework 사용자 지정 공급자의 기본 샘플

이 섹션에서는 EF를 사용 하 여 데이터베이스에서 이름-값 쌍을 읽을 수 있는 기본 구성 공급자가 만들어집니다. 

정의 `ConfigurationValue` 데이터베이스의 구성 값을 저장 하기 위한 엔터티:

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationValue.cs)]

추가 `ConfigurationContext` 저장 하 고 구성된 된 값에 액세스 합니다.

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]

구현 하는 클래스를 만드는 [IConfigurationSource](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]

상속 하 여 사용자 지정 구성 공급자를 만들기 [ConfigurationProvider](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.configurationprovider)합니다.  구성 공급자는 비어 있는 경우 데이터베이스를 초기화 합니다.

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]

("Value_from_ef_1" 및 "value_from_ef_2")은 데이터베이스의 강조 표시 된 값은 샘플 실행 될 때 표시 됩니다.

추가할 수는 `EFConfigSource` 구성 소스를 추가 하기 위한 확장 메서드:

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]

다음 코드에서는 사용자 지정을 사용 하는 방법을 보여 줍니다. `EFConfigProvider`:

[!code-csharp[Main](configuration/sample/CustomConfigurationProvider/Program.cs?highlight=21-26)]

사용자 지정을 추가 하는 샘플 참고 `EFConfigProvider` JSON 공급자 이므로 데이터베이스의 모든 설정의 설정을 재정의 합니다에서 *appsettings.json* 파일입니다.

다음을 사용 하 여 *appsettings.json* 파일:

[!code-json[Main](configuration/sample/CustomConfigurationProvider/appsettings.json)]

다음이 표시됩니다.

```console
key1=value_from_ef_1
key2=value_from_ef_2
key3=value_from_json_3
```

## <a name="commandline-configuration-provider"></a>명령줄 구성 공급자

다음 샘플 마지막 CommandLine 구성 공급자를 사용 합니다.

[!code-csharp[Main](configuration/sample/CommandLine/Program.cs)]

구성 설정에 전달 하려면 다음을 사용 합니다.

```console
dotnet run /Profile:MachineName=Bob /App:MainWindow:Left=1234
```

표시 합니다.

```console
Hello Bob
Left 1234
```

`GetSwitchMappings` 메서드를 사용 하면 `-` 대신 `/` 하 고 선행 하위 키 접두사를 제거 합니다. 예:

```console
dotnet run -MachineName=Bob -Left=7734
```

표시:

```console
Hello Bob
Left 7734
```

명령줄 인수 (null 일 수) 값을 포함 해야 합니다. 예:

```console
dotnet run /Profile:MachineName=
```

정상 이지만

```console
dotnet run /Profile:MachineName
```

예외가 발생 합니다. 해당 스위치 매핑이 지원 되지 않습니다는 대 한-또는--의 명령줄 스위치 접두사를 지정 하는 경우 예외가 throw 됩니다.

## <a name="the-webconfig-file"></a>Web.config 파일

A *web.config* 파일은 IIS 또는 IIS Express에서 응용 프로그램을 호스팅하는 경우에 필요 합니다. *web.config* AspNetCoreModule IIS에서 앱을 시작 하도록 설정 합니다. 설정 *web.config* AspNetCoreModule iis 응용 프로그램을 시작 하 고 다른 IIS 설정 및 모듈 구성에 사용 하도록 설정 합니다. Visual Studio를 사용 하는 경우 삭제 *web.config*, Visual Studio는 새 이름을 만듭니다.

### <a name="additional-notes"></a>추가 참고 사항

* DI (dependency Injection) 후 까지의 설정 되지 않은 `ConfigureServices` 가 호출 됩니다.
* 구성 시스템이 DI 인식 되지 않습니다.
* `IConfiguration`두 가지 특수화에 있습니다.
  * `IConfigurationRoot`루트 노드에 대해 사용 합니다. 다시 로드를 트리거할 수 있습니다.
  * `IConfigurationSection`구성 값의 섹션을 나타냅니다. `GetSection` 및 `GetChildren` 메서드는 반환 된 `IConfigurationSection`합니다.

### <a name="additional-resources"></a>추가 리소스

* [여러 환경 사용](environments.md)
* [개발 중 안전한 앱 비밀 저장소](../security/app-secrets.md)
* [종속성 주입](dependency-injection.md)
* [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)
