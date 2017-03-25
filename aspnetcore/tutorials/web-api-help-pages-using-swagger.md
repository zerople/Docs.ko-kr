---
title: "ASP.NET 웹 API 도움말 페이지 Swagger를 사용 하 여 | Microsoft 문서"
author: spboyer
description: "Swagger 웹 API 응용 프로그램에 추가 하는 연습"
keywords: ASP.NET Core, Swagger
ms.author: spboyer
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 54bb961d-29d9-4dee-8e2c-a93fc33c16f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/web-api-help-pages-using-swagger
translationtype: Machine Translation
ms.sourcegitcommit: 0c6c54fb67cf77b172f033c3c8aec801fddef81d
ms.openlocfilehash: 801a574b04bb0ebec299b170ee4f7fbd90090332
ms.lasthandoff: 03/23/2017

---
# <a name="aspnet-web-api-help-pages-using-swagger"></a>ASP.NET 웹 API 도움말 페이지를 사용 하 여 Swagger

<a name=web-api-help-pages-using-swagger></a>

[Shayne 보이 어](https://twitter.com/spboyer)

API의 다양 한 메서드를 이해 소비 응용 프로그램을 빌드할 때 개발자 어려울 수 있습니다.

좋은 설명서 및 도움말 페이지를 사용 하 여 웹 API의 일부로 생성 [Swagger](http://swagger.io) .NET Core 구현을 [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 몇 가지 NuGet 패키지를 추가 하 고 수정 하는 것 만큼 쉽습니다는 *Startup.cs*합니다.

* [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) ASP.NET 핵심 MVC를 사용 하 여 작성 된 웹 Api에 대 한 Swagger 문서를 생성 하기 위한 오픈 소스 프로젝트입니다.

* [Swagger](http://swagger.io) 대화형 설명서, 클라이언트 SDK 생성 및 검색 기능을 지원할 수 있도록 하는 RESTful API의 컴퓨터를 읽을 수 있는 표현입니다.

이 자습서 샘플 기반 [건물 Your 첫 번째 웹 API ASP.NET 핵심 MVC 및 Visual Studio와](first-web-api.md)합니다. 샘플을 다운로드 진행 하려는 경우 [https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample)합니다.

## <a name="getting-started"></a>시작

Swashbuckle에 세 가지 주요 구성 요소가

* *Swashbuckle.AspNetCore.Swagger* : Swagger 개체 모델 및 JSON 끝점으로 SwaggerDocument 개체를 노출 하는 미들웨어입니다.

* *Swashbuckle.AspNetCore.SwaggerGen* : 경로, 컨트롤러 및 모델에서 직접 SwaggerDocument 개체를 작성 하는 Swagger 생성기입니다. 일반적으로 자동으로 Swagger JSON을 노출 하는 Swagger 끝점 미들웨어 조합 합니다.

* *Swashbuckle.AspNetCore.SwaggerUI* : 공용 메서드에 대해 테스트 도구 기능에는 포함 된 버전의 Swagger UI 도구를 빌드하는 웹 API 기능을 설명 하는 풍부한 기능의 사용자 지정이 가능한 환경 Swagger JSON을 해석 하 고 포함 하는 기본 제공 합니다.

## <a name="nuget-packages"></a>NuGet 패키지

다음 방법 중 하나를 사용 하 여 Swashbuckle을 추가할 수 있습니다.

* 패키지 관리자 콘솔:

```bash
Install-Package Swashbuckle.AspNetCore -Pre
   ```

* Visual Studio에서 합니다.

     * 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 > NuGet 패키지 관리
     * 검색 상자에 Swashbuckle.AspNetCore를 입력 합니다.
     * "시험판 포함"를 확인 합니다.
     * Nuget.org 패키지 원본을 설정합니다
     * Swashbuckle.AspNetCore 패키지를 누른 다음 설치를 누릅니다

## <a name="add-and-configure-swagger-to-the-middleware"></a>추가 하 고 Swagger 미들웨어를 구성 합니다.

SwaggerGen 컬렉션에 추가 서비스 구성 방법에서 및 ConfigureServices 메서드에서 생성 된 서비스 JSON 문서와는 SwaggerUI 미들웨어를 사용 하도록 설정 합니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [12, 21, 24]}} -->

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add framework services.
    services.AddMvc();

    services.AddLogging();

    // Add our repository type
    services.AddSingleton<ITodoRepository, TodoRepository>();

    // Register the Swagger generator, defining one or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new Info { Title = "My API", Version = "v1" });
    });
}

// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    app.UseMvcWithDefaultRoute();

    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS etc.), specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
    });
}
```

Visual Studio에서 눌러 ^ f&5;를 눌러 응용 프로그램을 실행 하 고 이동 `http://localhost:<random_port>/swagger/v1/swagger.json` 끝점을 설명 하는 생성 된 문서를 볼 수 있습니다.

> [!NOTE]
> Microsoft Edge, Google Chrome 및 Firefox JSON 문서를 고유 하 게 표시합니다.  읽기 쉽도록 문서의 서식을 지정 하는 Chrome에 대 한 확장은. *아래 예제에서는 간단 하 게 줄어듭니다.*

```javascript
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
               "description": "OK",
               "schema": {
               "type": "array",
               "items": {
                   "$ref": "#/definitions/TodoItem"
               }
               }
           }
           },
           "deprecated": false
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
           "key": {
           "type": "string"
           },
           "name": {
           "type": "string"
           },
           "isComplete": {
           "type": "boolean"
           }
       }
       }
   },
   "securityDefinitions": {}
   }
   ```

이 문서는 Swagger ui로 이동 하 여 볼 수 있는 데`http://localhost:<random_port>/swagger`

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

UI에서 각각의 ToDo 컨트롤러에서 메서드를 테스트할 수 있습니다. 섹션을 확장 하 고, 모든 필요한 매개 변수를 추가, "사용해 보세요!"을 탭 하는 메서드를 누릅니다.

![예제 GET Swagger 테스트](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

## <a name="customization--extensibility"></a>사용자 지정 < / 확장성

Swagger는 API를 나타내는 데는 간단한 방법을 아니라 언어 디자인 또는 디자인을 일치 하도록 대화형 UI를 사용자 지정할 수 있을 뿐만 아니라 개체 모델을 문서화 하는 것에 대 한 옵션입니다.

### <a name="api-info-and-description"></a>API 정보 및 설명

구성 합니다. 작업에 전달 되는 `AddSwaggerGen` 작성자, 라이선스, 설명 등의 정보를 추가 하려면 메서드를 사용할 수 있습니다.

```csharp
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new Info
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = "None",
        Contact = new Contact { Name = "Shayne Boyer", Email = "", Url = "http://twitter.com/spboyer"},
        License = new License { Name = "Use under LICX", Url = "http://url.com" }
    });
});
```

다음 이미지는 추가 버전 정보를 표시 하는 Swagger UI를 표시 합니다.

![버전 정보로 UI swagger: 설명을 만들고 추가 링크를 참조 하십시오.](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>XML 주석

XML 주석의 활성화 하려면 Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **속성** 확인 한 다음는 **XML 문서 파일** 아래 상자는 **출력 설정** 섹션입니다.

![빌드 프로젝트 속성 탭](web-api-help-pages-using-swagger/_static/swagger-xml-comments.png)

생성된 된 XML 파일을 사용 하 여 Swagger를 구성 합니다.

> [!NOTE]
> Linux 또는 Windows 이외의 운영 체제 파일 이름 및 경로 가능 대 소문자를 구분 합니다. 따라서 `ToDoApi.XML` 예를 들어 Windows 있지만 하지 CentOS에서 찾을 수 있습니다.

[!code-csharp[주](../tutorials/web-api-help-pages-using-swagger/sample/src/TodoApi/Startup.cs?name=snippet_Configure&highlight=29,33)]

위 코드에서 ApplicationBasePath 하는 XML 설명에 전체 경로 설정 하는 데 필요한 응용 프로그램의 기본 경로 가져옵니다. `TodoApi.xml`이 예제에서는 응용 프로그램의 이름에 따라 파일은 생성된 된 XML 주석의 이름에만 작동 합니다.

삼중 슬래시 주석이 메서드에 추가 섹션의 헤더에 대 한 설명을 추가 하 여 Swagger UI를 향상 시킵니다.

[!code-csharp[주](../tutorials/web-api-help-pages-using-swagger/sample/src/TodoApi/Controllers/TodoController.cs?name=Delete_Method&highlight=2)]

![DELETE 메서드에 대 한 '특정 할 일 항목을 삭제 합니다.' XML 주석을 표시 하는 UI swagger](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

UI로 생성된 된 JSON 파일을 구동 하 고 이러한 주석은 뿐만 아니라 해당 파일에도 note 합니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "javascript", "highlight_args": {"hl_lines": [5]}} -->

```javascript
"delete": {
  "tags": [
    "Todo"
  ],
  "summary": "Deletes a specific TodoItem",
  "operationId": "ApiTodoByIdDelete",
  "consumes": [],
  "produces": [],
  "parameters": [
    {
      "name": "id",
      "in": "path",
      "description": "",
      "required": true,
      "type": "string"
    }
  ],
  "responses": {
    "204": {
      "description": "No Content"
    }
  },
  "deprecated": false
}
```

다음은 더 강력한 예제, 추가 `<remarks />` 콘텐츠 텍스트 또는 그 밖의 설명서는 메서드의 대 한 JSON 또는 XML 개체를 추가할 수 있는 합니다.

[!code-csharp[주](../tutorials/web-api-help-pages-using-swagger/sample/src/TodoApi/Controllers/TodoController.cs?name=Create_Method&highlight=4-14)]

이러한 추가 주석이 있는 UI의 향상 된 기능을 확인 합니다.

![UI를 표시 하는 추가 주석이 있는 swagger](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="dataannotations"></a>DataAnnotations

API 컨트롤러를 데코레이팅 할 수 있습니다 `System.ComponentModel.DataAnnotations` 하 게 도출할 Swagger UI 구성 요소입니다.

추가 `[Required]` 에 대 한 주석을 `Name` 의 속성은 `TodoItem` 클래스 UI ModelSchema 정보를 변경 합니다. `[Produces("application/json")]``RegularExpression` 유효성 검사기 등 생성된 된 페이지에서 제공 하는 정보가 자세히 추가 합니다.  코드에 있는 더 많은 메타 데이터는 더 많은 desciptive UI 또는 API 도움말 페이지를 생성 합니다.  

[!code-csharp[주](../tutorials/web-api-help-pages-using-swagger/sample/src/TodoApi/Models/TodoItem.cs?highlight=10)]


### <a name="describing-response-types"></a>응답 형식을 설명 하는

소비 하는 개발자는 아마도 가장 많이 사용 합니다; 반환 되는 특히 응답 형식 (표준 하지) 경우 오류 코드입니다. 이러한 XML 주석 및 DataAnnotations에서 처리 됩니다.

수행 된 `Create()` 메서드 예를 들어 현재 반환만 기본적으로 "201 생성 됨" 응답 합니다. 되는 항목이 실제로 만들어집니다, 또는 물론 "204 콘텐츠 없음" 게시물 본문에 전달 되는 데이터가 없는 경우.  그러나 알 수 없는 설명서 나 다른 응답 없기 때문입니다. 다음 코드 부분을 추가 하 여 해결할 수 있습니다.

[!code-csharp[주](../tutorials/web-api-help-pages-using-swagger/sample/src/TodoApi/Controllers/TodoController.cs?name=Create_Method&highlight=17,18,20,21)]

![Swagger UI를 보여주는 POST 응답 클래스 설명을 '새로 만든된 Todo 항목을 반환 합니다.' 및 '400-항목이 null 이면' 상태 코드 및 응답 메시지에서 이유](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

### <a name="customizing-the-ui"></a>UI를 사용자 지정

API에 대 한 설명서 페이지를 작성할 때 원하는 브랜드 나 모양과 느낌을 나타내기 위해 재고 UI는 매우 기능적으로 표현할 수 있는.

Swashbuckle 구성 요소와 해당 태스크를 수행은 간단 하지만 일반적으로 웹 API 프로젝트에 포함 되지 않습니다 정적 파일을 제공 하는 리소스를 추가 하 고 해당 파일을 호스팅할 폴더 구조를 구축 해야 합니다.

추가 된 `"Microsoft.AspNetCore.StaticFiles": "1.0.0-*"` 프로젝트에 NuGet 패키지입니다.

정적 파일 미들웨어를 사용 하도록 설정 합니다.

<!-- literal_block {"ids": [], "linenos": false, "xml:space": "preserve", "language": "csharp", "highlight_args": {"hl_lines": [4]}} -->

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
   {
       // Enable static files middleware.
       app.UseStaticFiles();

       app.UseMvcWithDefaultRoute();

        // Enable middleware to serve generated Swagger as a JSON endpoint.
        app.UseSwagger();

        // Enable middleware to serve swagger-ui (HTML, JS, CSS etc.), specifying the Swagger JSON endpoint.
        app.UseSwaggerUI(c =>
        {
            c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
        });
   }
   ```

핵심 획득 *index.html* Swagger UI 페이지에서 사용 되는 파일의 `Github repository <https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/1.0.0-rc1/test/WebSites/CustomIndexHtml/wwwroot/swagger/index.html>`_에 넣을 `wwwroot/swagger` 폴더 및 새를 만들 수도 `custom.css` 같은 폴더에 파일.

![Swagger UI를 사용자 지정 css 및 응용 프로그램의 wwwroot 폴더 내에서 html 파일을 보여 주는 솔루션 탐색기](web-api-help-pages-using-swagger/_static/custom-files-folder-view.png)

참조 *custom.css* 에 *index.html* 파일입니다.

```html
<link href='custom.css' media='screen' rel='stylesheet' type='text/css' />
```

다음 CSS 페이지에 사용자 지정 헤더 타이틀의 간단한 예제를 제공합니다.

*custom.css 파일*

[!code-css[주](web-api-help-pages-using-swagger/sample/src/TodoApi/wwwroot/swagger/custom.css)]

*index.html 본문*

```html
<body class="swagger-section">
   <div id="header">
    <h1>ToDo API Documentation</h1>
   </div>

   <div id="message-bar" class="swagger-ui-wrap" data-sw-translate>&nbsp;</div>
   <div id="swagger-ui-container" class="swagger-ui-wrap"></div>
</body>
```

![UI 사용자 지정 헤더 이름의 swagger](web-api-help-pages-using-swagger/_static/custom-header.png)

훨씬 더 많은 페이지를 사용 하 여 수행할 수 있으며, 전체 기능에 UI 리소스에 대 한 참조는 [Swagger UI Github 리포지토리](https://github.com/swagger-api/swagger-ui)합니다.

