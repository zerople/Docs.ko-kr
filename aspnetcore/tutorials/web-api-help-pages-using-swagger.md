---
title: "Swagger를 사용한 ASP.NET Core Web API 도움말 페이지"
author: spboyer
description: "이 자습서에서는 Swagger를 추가하여 Web API 응용 프로그램에 대한 설명서 및 도움말 페이지를 생성하는 연습을 제공합니다."
keywords: "ASP.NET Core, Swagger, Swashbuckle, 도움말 페이지, Web API"
ms.author: spboyer
manager: wpickett
ms.date: 09/01/2017
ms.topic: article
ms.assetid: 54bb961d-29d9-4dee-8e2c-a93fc33c16f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: fd2f415947c049d1239ce4e6bf0b1cf0264e7836
ms.sourcegitcommit: 41e3e007512c175a42910bc69678f3f0403cab04
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2017
---
# <a name="aspnet-web-api-help-pages-using-swagger"></a>Swagger를 사용한 ASP.NET Web API 도움말 페이지

<a name=web-api-help-pages-using-swagger></a>

작성자: [Shayne Boyer](https://twitter.com/spboyer) 및 [Scott Addie](https://twitter.com/Scott_Addie)

소비 응용 프로그램을 빌드할 경우 개발자가 API의 다양한 메서드를 이해하기 어려울 수 있습니다.

[Swagger](http://swagger.io)와 .NET Core 구현 [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)를 함께 사용하여 Web API에 대한 유용한 설명서와 도움말 페이지를 생성하는 것은 두서너 개의 NuGet 패키지를 추가하고 *Startup.cs*를 수정하는 것만큼 쉽습니다.

* [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)는 ASP.NET Core Web API에 대한 Swagger 문서를 생성하기 위한 오픈 소스 프로젝트입니다.

* [Swagger](http://swagger.io)는 대화형 설명서, 클라이언트 SDK 생성 및 검색 기능에 대한 지원을 가능하게 하는 RESTful API의 컴퓨터에서 읽을 수 있는 표현입니다.

이 자습서는 [ASP.NET Core MVC 및 Visual Studio를 사용하여 첫 번째 Web API 빌드](xref:tutorials/first-web-api)에 대한 샘플을 기반으로 빌드됩니다. 함께 수행해 보려면 [https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample)에서 샘플을 다운로드합니다.

## <a name="getting-started"></a>시작

Swashbuckle에 대한 세 가지 주 구성 요소는 다음과 같습니다.

* `Swashbuckle.AspNetCore.Swagger`: `SwaggerDocument` 개체를 JSON 끝점으로 노출하기 위한 Swagger 개체 모델 및 미들웨어.

* `Swashbuckle.AspNetCore.SwaggerGen`: 경로, 컨트롤러 및 모델에서 직접 `SwaggerDocument` 개체를 빌드하는 Swagger 생성기. 일반적으로 Swagger 끝점 미들웨어와 결합되어 자동으로 Swagger JSON을 노출합니다.

* `Swashbuckle.AspNetCore.SwaggerUI`: Web API 기능을 설명할 수 있는 다양한 사용자 지정 가능한 환경을 빌드하기 위해 Swagger JSON을 해석하는 Swagger UI 도구의 포함된 버전. 여기에는 공용 메서드에 대한 기본 제공 테스트 도구가 포함됩니다.

## <a name="nuget-packages"></a>NuGet 패키지

다음 방법으로 Swashbuckle을 추가할 수 있습니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **패키지 관리자 콘솔** 창에서:

    ```powershell
    Install-Package Swashbuckle.AspNetCore
    ```

* **NuGet 패키지 관리** 대화 상자에서:

     * **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 > **NuGet 패키지 관리** 선택
     * **패키지 소스**를 “nuget.org”로 설정
     * 검색 상자에 “Swashbuckle.AspNetCore” 입력
     * **찾아보기** 탭에서 “Swashbuckle.AspNetCore” 패키지를 선택하고 **설치** 클릭

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **Solution Pad**에서 *Packages* 폴더를 마우스 오른쪽 단추로 클릭 > **패키지 추가...** 선택
* **패키지 추가** 창의 **소스** 드롭다운을 “nuget.org”로 설정
* 검색 상자에 Swashbuckle.AspNetCore 입력
* 결과 창에서 Swashbuckle.AspNetCore 패키지를 선택하고 **패키지 추가** 클릭

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**통합 터미널**에서 다음 명령을 실행합니다.

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

다음 명령을 실행합니다.

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

---

## <a name="add-and-configure-swagger-to-the-middleware"></a>미들웨어에 Swagger 추가 및 구성

*Startup.cs*의 `ConfigureServices` 메서드에서 서비스 컬렉션에 Swagger 생성기를 추가합니다.

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup2.cs?name=snippet_ConfigureServices&highlight=7-10)]

*Startup.cs*의 `Configure` 메서드에서 생성된 JSON 문서 및 SwaggerUI를 지원하기 위해 미들웨어를 사용하도록 설정합니다.

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup2.cs?name=snippet_Configure&highlight=4,7-10)]

앱을 시작하고 `http://localhost:<random_port>/swagger/v1/swagger.json`으로 이동합니다. 끝점을 설명하는 생성된 문서가 나타납니다.

**참고:** Microsoft Edge, Google Chrome 및 Firefox에는 JSON 문서가 기본적으로 표시됩니다. 더 읽기 쉽도록 문서 서식을 지정하는 Chrome용 확장이 있습니다. *간략하게 표현하기 위해 다음 예제는 간소화되어 있습니다.*

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

이 문서에서는 `http://localhost:<random_port>/swagger`로 이동하여 볼 수 있는 Swagger UI를 실행합니다.

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

`TodoController`의 각 공용 작업 메서드를 UI에서 테스트할 수 있습니다. 메서드 이름을 클릭하여 섹션을 확장합니다. 필요한 매개 변수를 추가하고 “직접 시험해 보세요.”를 클릭합니다.

![예제 Swagger GET 테스트](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

## <a name="customization--extensibility"></a>사용자 지정 및 확장성

Swagger는 개체 모델을 설명하고 테마와 일치하도록 UI를 사용자 지정하는 옵션을 제공합니다.

### <a name="api-info-and-description"></a>API 정보 및 설명

`AddSwaggerGen` 메서드에 전달되는 구성 작업은 작성자, 라이선스 및 설명과 같은 정보를 추가하는 데 사용될 수 있습니다.

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?range=20-30,36)]

다음 이미지에서는 버전 정보를 표시하는 Swagger UI를 설명합니다.

![버전 정보가 포함된 Swagger UI: 설명, 작성자 및 추가 링크 확인](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>XML 주석

XML 주석은 다음 방법으로 사용하도록 설정할 수 있습니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성** 선택
* **빌드** 탭의 **출력** 섹션에서 **XML 문서 파일** 상자 선택:

![프로젝트 속성의 빌드 탭](web-api-help-pages-using-swagger/_static/swagger-xml-comments.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **프로젝트 옵션** 대화 상자 열기 > **빌드** > **컴파일러** 선택
* **일반 옵션** 섹션에서 **XML 문서 생성** 상자 선택:

![프로젝트 옵션의 일반 옵션 섹션](web-api-help-pages-using-swagger/_static/swagger-xml-comments-mac.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

*.csproj* 파일에 다음 코드 조각을 수동으로 추가합니다.

[!code-xml[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/TodoApi.csproj?range=7-9)]

---

생성된 XML 파일을 사용하도록 Swagger를 구성합니다. Linux 또는 Windows가 아닌 운영 체제의 경우 파일 이름 및 경로는 대/소문자를 구분할 수 있습니다. 예를 들어 *ToDoApi.XML* 파일은 Windows에는 있지만 CentOS에는 없습니다.

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?name=snippet_ConfigureServices&highlight=20-22)]

이전 코드에서 `ApplicationBasePath`는 앱의 기본 경로를 가져옵니다. 기본 경로는 XML 주석 파일을 찾는 데 사용됩니다. *TodoApi.xml*은 이 예제에서만 작동합니다. 생성된 XML 주석 파일의 이름이 응용 프로그램 이름을 기준으로 하기 때문입니다.

메서드에 3중 슬래시 주석을 추가하면 섹션 헤더에 설명이 추가되어 Swagger UI가 개선됩니다.

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete&highlight=2)]

![DELETE 메서드에 대한 XML 주석 ‘특정 TodoItem을 삭제합니다.’를 보여 주는 Swagger UI](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

UI는 생성된 JSON 파일을 통해 실행되므로 다음 주석을 포함합니다.

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

[<remarks>](https://docs.microsoft.com/dotnet/csharp/programming-guide/xmldoc/remarks) 태그를 `Create` 작업 메서드 문서에 추가합니다. 이 태그는 `<summary>` 태그에 지정된 정보를 보충하고 더 강력한 Swagger UI를 제공합니다. `<remarks>` 태그 콘텐츠는 텍스트, JSON 또는 XML로 구성될 수 있습니다.

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

이러한 추가 주석이 포함된 향상된 UI 기능을 확인할 수 있습니다.

![추가 주석이 표시된 Swagger UI](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>데이터 주석

`System.ComponentModel.DataAnnotations`에 있는 특성으로 API 컨트롤러를 데코레이트하면 Swagger UI 구성 요소를 실행하는 데 도움이 됩니다.

`[Required]` 특성을 `TodoItem` 클래스의 `Name` 속성에 추가합니다.

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Models/TodoItem.cs?highlight=10)]

이 특성이 있으면 UI 동작이 변경되고 기본 JSON 스키마가 변경됩니다.

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

API 컨트롤러에 `[Produces("application/json")]` 특성을 추가합니다. 이 특성은 컨트롤러 동작이 *application/json* 콘텐츠 형식의 반환을 지원하도록 선언하는 데 사용됩니다.

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_TodoController&highlight=3)]

컨트롤러 GET 작업의 경우 **응답 콘텐츠 형식** 드롭다운에서 이 콘텐츠 형식이 기본값으로 선택됩니다.

![기본 응답 콘텐츠 형식이 포함된 Swagger UI](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

Web API에서 데이터 주석 사용이 증가하면 UI 및 API 도움말 페이지에는 더 자세한 설명과 유용한 정보가 제공됩니다.

### <a name="describing-response-types"></a>응답 형식 설명

소비 개발자는 반환된 항목 특히, 응답 형식 및 오류 코드(표준이 아닌 경우)를 가장 중요하게 생각합니다.&mdash; 이러한 항목은 XML 주석 및 데이터 주석에서 처리됩니다.

`Create` 작업은 성공 시 `201 Created`를 반환하고, 게시된 요청 본문이 null일 경우 `400 Bad Request`를 반환합니다. Swagger UI에 적절한 문서가 없으면 소비자는 이러한 예상 결과에 대한 정보를 알 수 없습니다. 다음 예제에서 강조 표시된 줄을 추가하면 이 문제가 해결됩니다.

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

이제 Swagger UI에서는 예상 HTTP 응답 코드를 분명히 설명합니다.

![POST 응답 클래스 설명 ‘새로 만들어진 Todo 항목 반환’ 및 ‘400 - 응답 메시지에서 항목의 상태 코드 및 이유가 null인 경우’를 보여 주는 Swagger UI](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

### <a name="customizing-the-ui"></a>UI 사용자 지정

스톡 UI는 기능적인 동시에 표현 가능하지만 API에 대한 문서 페이지를 빌드할 경우 스톡 UI로 브랜드나 테마를 표현하고자 합니다. Swashbuckle 구성 요소를 사용하여 해당 작업을 수행하려면 리소스를 추가하여 정적 파일을 지원하고 폴더 구조를 빌드하여 해당 파일을 호스트합니다.

.NET Framework를 대상으로 지정할 경우 `Microsoft.AspNetCore.StaticFiles` NuGet 패키지를 프로젝트에 추가합니다.

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

정적 파일 미들웨어를 사용하도록 설정합니다.

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?name=snippet_Configure&highlight=3)]

[Swagger UI GitHub 리포지토리](https://github.com/swagger-api/swagger-ui/tree/2.x/dist)에서 *dist* 폴더의 콘텐츠를 가져옵니다. 이 폴더에는 Swagger UI 페이지에 필요한 자산이 포함됩니다. 해당 폴더의 콘텐츠를 *wwwroot/swagger/ui* 폴더로 복사합니다.

다음 CSS가 포함된 *wwwroot/swagger/ui/css/custom.css* 파일을 만들어 페이지 헤더를 사용자 지정합니다.

[!code-css[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/wwwroot/swagger/ui/css/custom.css)]

*index.html* 파일에서 *custom.css*를 참조합니다.

[!code-html[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/wwwroot/swagger/ui/index.html?range=14)]

`http://localhost:<random_port>/swagger/ui/index.html`의 *index.html* 페이지로 이동합니다. 헤더의 텍스트 상자에 `http://localhost:<random_port>/swagger/v1/swagger.json`을 입력하고 **탐색** 단추를 클릭합니다. 결과 페이지가 다음과 같이 표시됩니다.

![사용자 지정 헤더 제목이 포함된 Swagger UI](web-api-help-pages-using-swagger/_static/custom-header.png)

페이지를 사용하여 훨씬 더 많은 작업을 수행할 수 있습니다. [Swagger UI GitHub 리포지토리](https://github.com/swagger-api/swagger-ui)에서 UI 리소스에 대한 전체 기능을 참조하세요.