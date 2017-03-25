---
title: "ASP.NET 핵심에서 구성 | Microsoft 문서"
author: rick-anderson
description: "구성 API를 보여 줍니다."
keywords: "ASP.NET Core, 구성, JSON"
ms.author: riande
manager: wpickett
ms.date: 11/29/2016
ms.topic: article
ms.assetid: b3a5984d-e172-42eb-8a48-547e4acb6806
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/configuration
translationtype: Machine Translation
ms.sourcegitcommit: 196d0ef439afe13d37372121cb83e1bc46bd80c4
ms.openlocfilehash: 9eee4ae6d88decb3a709cafd0c8927892a88254c
ms.lasthandoff: 03/23/2017

---
<a name=fundamentals-configuration></a>

  # <a name="configuration-in-aspnet-core"></a>ASP.NET Core의 구성

[Rick Anderson](https://twitter.com/RickAndMSFT), [Mark Michaelis](http://intellitect.com/author/mark-michaelis/), [Steve Smith](http://ardalis.com), [Daniel Roth](https://github.com/danroth27)

구성 API의 여러 원본에서 런타임에 읽을 수 있는 이름-값 쌍의 목록에 따라 응용 프로그램을 구성 하는 방법을 제공 합니다. 여러 수준 계층에 이름-값 쌍을 그룹화 할 수 있습니다. 에 대 한 구성 공급자가 있습니다.

* 파일 형식 (INI, JSON 및 XML)
* 명령줄 인수
* 환경 변수
* 메모리 내에서.NET 개체
* 암호화 된 사용자 저장소
* [Azure 키 자격 증명 모음](xref:security/key-vault-configuration)
* 사용자 지정 공급자가 설치 또는 만들기

각 구성 값 문자열 키에 매핑됩니다. 설정을 사용자 지정 구현으로 deserialize 하는 데 기본 제공 바인딩 지원은 [POCO](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object) 개체 (속성을 가진 간단한.NET 클래스).

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/docs/tree/master/aspnetcore/fundamentals/configuration/sample)

  ## <a name="simple-configuration"></a>단순 구성

다음 콘솔 응용 프로그램 JSON 구성 공급자를 사용합니다.

[!code-csharp[주](configuration/sample/src/ConfigJson/Program.cs)]

앱은 읽고 다음 구성 설정을 표시 합니다.

[!code-json[주](configuration/sample/src/ConfigJson/appsettings.json)]

노드는 콜론으로 구분 하는 이름-값 쌍의 계층적 목록을 구성 합니다. 액세스 값을 검색 하는 `Configuration` 인덱서를 해당 항목의 키:

```csharp
Console.WriteLine($"option1 = {Configuration["subsection:suboption1"]}");
```

하지만 이름/값 쌍에 기본 제공 기록에서 `Configuration` 공급자는 **하지** 유지 만들 수 있습니다 값을 저장 하는 사용자 지정 공급자입니다. 참조 [사용자 지정 구성 공급자](xref:fundamentals/configuration#custom-config-providers)합니다.

위의 샘플 구성 인덱서를 사용 하 여 값을 읽습니다. 경우는 `Configuration` 개체를 사용할 수 있는 인덱서가 설정에 액세스 하는 편리한 방법입니다. 외부의 응용 프로그램의 다른 부분으로 구성에서 흐름 정보를 `Startup`를 사용 하는 것이 좋습니다는 [옵션 패턴](xref:fundamentals/configuration#options-config-objects)합니다. 이 문서의 뒷부분에 있는 살펴보겠습니다.

일반적으로 다른 환경, 예를 들어, 개발, 테스트 및 프로덕션에 대 한 구성 설정이 다를 경우합니다 다음 강조 표시 된 코드 세 가지 원본에 두 명의 구성 공급자를 연결 합니다.

1. JSON 공급자를 읽는 *appsettings.json*
2. JSON 공급자를 읽는 *appsettings.\< EnvironmentName >.json*
3. 환경 변수 공급자

[!code-csharp[주](configuration/sample/src/WebConfigBind/Startup.cs?name=snippet2&highlight=7-9)]

참조 [AddJsonFile](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.configuration.jsonconfigurationextensions) 에 대 한 설명은 매개 변수입니다. `reloadOnChange`ASP.NET 핵심 1.1 이상 에서만 지원 됩니다. 

지정 된 순서로 구성 소스 읽혀집니다. 위 코드에서 환경 변수 마지막 읽혀집니다. 환경을 통해 설정 된 모든 구성 값에는 두 명의 이전 공급자에 설정 된 대신 대체 됩니다.

환경 중 하나에 일반적으로 설정 됩니다 `Development`, `Staging`, 또는 `Production`합니다. 참조 [여러 환경 작업](environments.md) 에 대 한 자세한 내용은 합니다.

구성 고려 사항:

* `IOptionsSnapshot`변경 될 때 구성 데이터를 다시 로드할 수 있습니다. 사용 하 여 `IOptionsSnapshot` 구성 데이터를 다시 로드 하는 경우.  참조 [IOptionsSnapshot](#ioptionssnapshot) 에 대 한 자세한 내용은 합니다.
* 구성 키가 대/소문자 구분 합니다.
* 가장 좋은 방법은 로컬 환경 설정 배포 된 구성 파일에 항목을 재정의할 수 있도록 마지막으로, 환경 변수를 지정 하는 것입니다.
* **하지** 구성 공급자 코드 또는 일반 텍스트 구성 파일에 암호 또는 기타 중요 한 데이터를 저장 합니다. 또한 안 개발에서 생산 암호를 사용 또는 테스트 환경. 대신,를 리포지토리로 실수로 커밋 수 있도록 프로젝트 트리에서 외부 암호를 지정 합니다. 에 대 한 자세한 내용은 [여러 환경 작업](environments.md) 및 관리 [개발 하는 동안 앱 암호의 안전한 저장소](../security/app-secrets.md)합니다.
* 경우 `:` 수 없습니다 시스템에서 환경 변수에서 사용 되는 대체 `:` 와 `__` (이중 밑줄).

<a name=options-config-objects></a>

## <a name="using-options-and-configuration-objects"></a>옵션 및 구성 개체를 사용 하 여

옵션 패턴 관련된 설정 그룹을 나타내는 사용자 지정 옵션 클래스를 사용 합니다. 앱 내에서 각 기능에 대해 분리 된 클래스를 만들어야 하는 것이 좋습니다. 분리 된 클래스를 수행합니다.

* [인터페이스 분리 원칙 (ISP)](http://deviq.com/interface-segregation-principle/) : 클래스를 사용 하는 구성 설정에만 의존 합니다.
* [중요 한 부분의 분리](http://deviq.com/separation-of-concerns/) : 앱의 다른 부분에 대 한 설정을 종속 또는 서로 결합 됩니다.

옵션 클래스에 추상이 아닌 이어야 합니다. 공용 매개 변수가 없는 생성자를 사용 합니다. 예:

[!code-csharp[주](configuration/sample/src/UsingOptions/Models/MyOptions.cs)]

<a name=options-example></a>

다음 코드에서 JSON 구성 공급자가 사용 됩니다. `MyOptions` 클래스는 서비스 컨테이너에 추가 되 고 구성에 바인딩됩니다.

[!code-csharp[주](configuration/sample/src/UsingOptions/Startup.cs?name=snippet1&highlight=8,20-22)]


다음 [컨트롤러](../mvc/controllers/index.md) 사용 하 여 [종속성 주입](dependency-injection.md) 에 [ `IOptions<TOptions>` ](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.extensions.options.ioptions-1) 설정에 액세스 합니다.

[!code-csharp[주](configuration/sample/src/UsingOptions/Controllers/HomeController.cs?name=snippet1)]

다음으로 *appsettings.json* 파일:

[!code-json[주](configuration/sample/src/UsingOptions/appsettings1.json)]

`HomeController.Index` 메서드가 반환한 `option1 = value1_from_json, option2 = 2`합니다.

일반적인 앱 단일 옵션 파일에 전체 구성을 바인딩 되지 않습니다. 나중에 사용 하는 방법을 살펴보겠습니다 `GetSection` 섹션에 바인딩할 합니다.

다음 코드에서 두 번째 `IConfigureOptions<TOptions>` 서비스를 서비스 컨테이너에 추가 합니다. 대리자를 사용 하 여 바인딩을 구성 `MyOptions`합니다.

[!code-csharp[주](configuration/sample/src/UsingOptions/Startup2.cs?name=snippet1&highlight=9-13)]

여러 구성 공급자를 추가할 수 있습니다. 구성 공급자 NuGet 패키지에서 제공 됩니다. 등록 된 순서에 적용 됩니다.

호출할 때마다 `Configure<TOptions>` 추가 `IConfigureOptions<TOptions>` 서비스를 서비스 컨테이너입니다. 값 위의 예에서 `Option1` 및 `Option2` 에 지정 된 *appsettings.json*, 하지만의 값 `Option1` 위의 강조 표시 된 코드에서 구성 된 대리자에 의해 재정의 됩니다. 

구성 서비스가 여러 개 사용 하는 경우 마지막 구성 소스 "wins" 지정 합니다. 위의 코드는 `HomeController.Index` 메서드 반환 `option1 = value1_from_action, option2 = 2`합니다.

옵션 유형의 각 속성을 폼의 구성 키에 바인딩되어 구성 옵션을 바인딩할 때 `property[:sub-property:]`합니다. 예를 들어는 `MyOptions.Option1` 속성의 키에 바인딩된 `Option1`에서 읽음는 `option1` 속성에 *appsettings.json*합니다. 하위 속성 샘플은이 문서의 뒷부분에 표시 됩니다.

다음 코드에서는 세 번째 `IConfigureOptions<TOptions>` 서비스를 서비스 컨테이너에 추가 합니다. 바인딩할 `MySubOptions` 섹션을 `subsection` 의 *appsettings.json* 파일:

[!code-csharp[주](configuration/sample/src/UsingOptions/Startup3.cs?name=snippet1&highlight=16-17)]

다음을 사용 하 여 *appsettings.json* 파일:

[!code-json[주](configuration/sample/src/UsingOptions/appsettings.json)]

`MySubOptions` 클래스:

[!code-csharp[주](configuration/sample/src/UsingOptions/Models/MySubOptions.cs)]

다음으로 `Controller`:

[!code-csharp[주](configuration/sample/src/UsingOptions/Controllers/HomeController2.cs?name=snippet1)]

`subOption1 = subvalue1_from_json, subOption2 = 200`이 반환 됩니다.

<a name=in-memory-provider></a>

## <a name="ioptionssnapshot"></a>IOptionsSnapshot

*ASP.NET Core 1.1 이상이 필요합니다.*

`IOptionsSnapshot`구성 파일이 변경 될 때 구성 데이터를 다시 로드를 지원 합니다.  변경 내용에 대해 신경 쓰지 최소한의 오버 헤드가 있습니다. 사용 하 여 `IOptionsSnapshot` 와 `reloadOnChange: true`, 옵션에 바인딩된 `IConfiguration` 변경 될 때 다시 로드 합니다.

다음 샘플에는 새로운 방법을 보여 줍니다 `IOptionsSnapshot` 후에 만들어진 *config.json* 변경 합니다. 동일한 서버에 요청을 반환 합니다 시간 *config.json* 가 **하지** 변경 합니다. 첫 번째 요청 후 *config.json* 변경 시간을 표시 합니다.

[!code-csharp[주](configuration/sample/IOptionsSnapshot2/Startup.cs?name=snippet1&highlight=1-9,13-18,32,33,52,53)]

다음 이미지에서는 서버 출력을 보여 줍니다.

![이미지 표시 된 브라우저 "마지막 업데이트 날짜: 11/22/2016 오후 4시 43분"](configuration/_static/first.png)

메시지 값 또는 표시 된 시간이 변경 되지 않습니다 브라우저 새로 고침 (때 *config.json* 변경 되지 않은).

변경 하 고 저장 된 *config.json* 후 브라우저를 새로 고칩니다.

![이미지 표시 된 브라우저 "를 e: 마지막으로 업데이트 11/22/2016 오후 4시 53분"](configuration/_static/change.png)

## <a name="in-memory-provider-and-binding-to-a-poco-class"></a>메모리 내 공급자 및 POCO 클래스에 바인딩

다음 샘플에는 메모리 내 공급자를 사용 하는 클래스에 바인딩하는 방법을 보여 줍니다.

[!code-csharp[주](configuration/sample/src/InMemory/Program.cs)]

[!code-csharp[주](configuration/sample/src/InMemory/MyWindow.cs)]

구성 값을 문자열로 반환 됩니다 하지만 바인딩 개체의 생성을 사용 합니다. 바인딩을 사용 하면 전체 개체 그래프 또는 POCO 개체를 검색할 수 있습니다. 다음 샘플에서는를 바인딩하는 방법을 보여 줍니다. `MyWindow` 핵심 ASP.NET MVC 앱과 함께 옵션 패턴을 사용 합니다.

[!code-csharp[주](configuration/sample/src/WebConfigBind/MyWindow.cs)]

[!code-json[주](configuration/sample/src/WebConfigBind/appsettings.json)]

사용자 지정 클래스를 바인딩할 `ConfigureServices` 에 `Startup` 클래스:

[!code-csharp[주](configuration/sample/src/WebConfigBind/Startup.cs?name=snippet1&highlight=3,4)]

설정을 표시는 `HomeController`:

[!code-csharp[주](configuration/sample/src/WebConfigBind/Controllers/HomeController.cs)]

  ### <a name="getvalue"></a>GetValue

다음 샘플을 참조 하십시오.는 `GetValue<T>` 확장 메서드:

[!code-csharp[주](configuration/sample/src/InMemoryGetValue/Program.cs?highlight=25-27)]

ConfigurationBinder의 `GetValue<T>` 메서드 (샘플에는&80;) 기본 값을 지정할 수 있습니다. `GetValue<T>`단순한 시나리오 이며 전체 섹션에 바인딩되지 않습니다. `GetValue<T>`스칼라 값을 가져옵니다 `GetSection(key).Value` 특정 형식으로 변환 합니다.

  ## <a name="binding-to-an-object-graph"></a>개체 그래프에 바인딩

클래스의 각 개체를 재귀적으로 바인딩할을 수 있습니다. 다음은 `AppOptions` 클래스:

[!code-csharp[주](configuration/sample/src/ObjectGraph/AppOptions.cs)]

다음 샘플에 바인딩하는 `AppOptions` 클래스:

[!code-csharp[주](configuration/sample/src/ObjectGraph/Program.cs?highlight=19-20)]

**ASP.NET Core 1.1** 이상 사용할 수 `Get<T>`, 작동 하는 전체 섹션을 사용 합니다. `Get<T>`사용 하 여 보다 자세한 convienent 수 `Bind`합니다. 다음 코드를 사용 하는 방법을 보여 줍니다 `Get<T>` 위의 샘플:

```csharp
var appConfig = config.GetSection("App").Get<AppOptions>();
```

다음을 사용 하 여 *appsettings.json* 파일:

[!code-json[주](configuration/sample/src/ObjectGraph/appsettings.json)]

프로그램은 표시 `Height 11`합니다.

단위에 다음 코드를 사용할 수 있습니다 구성을 테스트 합니다.

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

  ## <a name="simple-sample-of-entity-framework-custom-provider"></a>Entity Framework 사용자 지정 공급자의 간단한 샘플

이 섹션에서는 EF를 사용 하 여 데이터베이스에서 이름-값 쌍을 읽는 하는 간단한 구성 공급자를 만들겠습니다. 

정의 `ConfigurationValue` 엔터티를 데이터베이스에 구성 값을 저장 합니다.

[!code-csharp[주](configuration/sample/src/CustomConfigurationProvider/ConfigurationValue.cs)]

추가 `ConfigurationContext` 저장 하 고 구성된 된 값을 액세스 합니다.

<!-- literal_block {"xml:space": "preserve", "language": "c#", "dupnames": [], "linenos": false, "classes": [], "ids": [], "backrefs": [], "source": "/Users/shirhatti/src/Docs/aspnet/fundamentals/configuration/sample/src/CustomConfigurationProvider/ConfigurationContext.cs", "highlight_args": {"linenostart": 1, "hl_lines": [7]}, "names": []} -->

[!code-csharp[주](configuration/sample/src/CustomConfigurationProvider/ConfigurationContext.cs?name=snippet1)]

구현 하는 클래스를 만들고 [IConfigurationSource](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.extensions.configuration.iconfigurationsource):

[!code-csharp[주](configuration/sample/src/CustomConfigurationProvider/EntityFrameworkConfigurationSource.cs?highlight=7)]

상속 하 여 사용자 지정 구성 공급자를 만들 [ConfigurationProvider](https://docs.microsoft.com/en-us/aspnet/core/api/microsoft.extensions.configuration.configurationprovider)합니다.  구성 공급자는 비어 있는 경우 데이터베이스를 초기화 합니다.

[!code-csharp[주](configuration/sample/src/CustomConfigurationProvider/EntityFrameworkConfigurationProvider.cs?highlight=9,18-31,38-39)]

데이터베이스 ("value_from_ef_1" 및 "value_from_ef_2")에서 강조 표시 된 값은 샘플 실행 될 때 표시 됩니다.

추가할 수는 `EFConfigSource` 구성 소스를 추가 하기 위한 확장 메서드:

[!code-csharp[주](configuration/sample/src/CustomConfigurationProvider/EntityFrameworkExtensions.cs?highlight=12)]

다음 코드에서는 사용자 지정을 사용 하는 방법을 보여 줍니다. `EFConfigProvider`:

[!code-csharp[주](configuration/sample/src/CustomConfigurationProvider/Program.cs?highlight=20-25)]

샘플에서는 사용자 지정을 추가 하는 참고 `EFConfigProvider` JSON 공급자 되므로 데이터베이스의 모든 설정의 설정을 재정의 합니다에서 *appsettings.json* 파일입니다.

다음을 사용 하 여 *appsettings.json* 파일:

[!code-json[주](configuration/sample/src/CustomConfigurationProvider/appsettings.json)]

다음 내용이 표시 됩니다.

```
key1=value_from_ef_1
key2=value_from_ef_2
key3=value_from_json_3
```

  ## <a name="commandline-configuration-provider"></a>명령줄 구성 공급자

다음 샘플 마지막 명령줄 구성 공급자를 사용 합니다.

[!code-csharp[주](configuration/sample/src/CommandLine/Program.cs)]

구성 설정에 전달 하려면 다음을 사용 합니다.

```
dotnet run /Profile:MachineName=Bob /App:MainWindow:Left=1234
```

표시 합니다.

```
Hello Bob
Left 1234
```

`GetSwitchMappings` 메서드를 사용 하면 `-` 보다는 `/` 하 고 선행 하위 키 접두사를 제거 합니다. 예:

```
dotnet run -MachineName=Bob -Left=7734
```

보여줍니다.

<!-- literal_block {"xml:space": "preserve", "dupnames": [], "classes": [], "ids": [], "backrefs": [], "names": []} -->

```
Hello Bob
Left 7734
```

명령줄 인수 (null 일 수) 값을 포함 해야 합니다. 예:

```
dotnet run /Profile:MachineName=
```

확인은 있지만

```
dotnet run /Profile:MachineName
```

예외가 발생 합니다. -또는-는 경우 해당 스위치 매핑이 없는 명령줄 스위치 접두사를 지정 하는 경우 예외가 throw 됩니다.

  ## <a name="the-webconfig-file"></a>*web.config* 파일

*web.config* 은 IIS 또는 IIS Express에서 응용 프로그램을 호스트 하는 경우에 필요 합니다. IIS에서 응용 프로그램을 시작 하도록 AspNetCoreModule을 설정 합니다. 다른 IIS 설정 및 모듈을 구성 하려면 사용 될 수도 있습니다. Visual Studio를 사용 하는 경우에 삭제 *web.config*, Visual Studio에서 새를 만듭니다.

  ### <a name="additional-notes"></a>추가 참고 사항

* 종속성 주입 DI ()가 설치 되지 않은까지 후 `ConfigureServices` 가 호출 구성 시스템이 DI 인식있지 않습니다.
* `IConfiguration`두 가지 특수화에 있습니다.

  * `IConfigurationRoot`루트 노드에 사용 합니다. 다시 로드를 트리거할 수 있습니다.
  * `IConfigurationSection`구성 값의 섹션을 나타냅니다. `GetSection` 및 `GetChildren` 메서드는 반환 된 `IConfigurationSection`합니다.

### <a name="additional-resources"></a>추가 리소스

* [여러 환경 작업](environments.md)
* [개발 하는 동안 앱 암호의 안전한 저장소](../security/app-secrets.md)
* [종속성 주입](dependency-injection.md)
* [Azure 키 자격 증명 모음 구성 공급자](xref:security/key-vault-configuration)

