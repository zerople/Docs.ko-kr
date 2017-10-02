---
title: "URL의 ASP.NET Core 미들웨어를 다시 작성"
author: guardrex
description: "다시 작성 및 ASP.NET Core 응용 프로그램의 URL 재작성 미들웨어와 리디렉션 URL에 알아봅니다."
keywords: "ASP.NET Core URL 재작성, URL 재작성, URL 리디렉션, 미들웨어, apache_mod 리디렉션 URL"
ms.author: riande
manager: wpickett
ms.date: 08/17/2017
ms.topic: article
ms.assetid: e6130638-c410-4161-9921-b658ce988bd1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/url-rewriting
ms.openlocfilehash: 0a4024edf13651e2ed7e0f87e554e8ba8d895619
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2017
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>URL의 ASP.NET Core 미들웨어를 다시 작성

여 [Luke Latham](https://github.com/guardrex) 및 [Mikael Mengistu](https://github.com/mikaelm12)

[보거나 다운로드 샘플 코드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([다운로드 하는 방법을](xref:tutorials/index#how-to-download-a-sample))

URL 다시 쓰기는 하나 이상의 미리 정의 된 규칙을 기반으로 하는 Url 요청을 수정 하는 작업입니다. URL 다시 작성 하는 위치 및 주소 밀접 하 게 연결 되지 않은 리소스 위치 및 해당 주소 간의 추상화를 만듭니다. URL 다시 쓰기는 중요 한 몇 가지 시나리오가 있습니다.
* 이동 또는 이러한 리소스에 대 한 안정적인 로케이터를 유지 관리 하는 동안 서버 리소스를 일시적 또는 영구적으로 대체
* 하나의 응용 프로그램의 영역에서 또는 다른 앱에서 처리 중인 요청과 분
* 제거, 추가 또는 들어오는 요청에 URL 세그먼트를 다시 구성
* 공용 Url에 대 한 검색 엔진 최적화 SEO () 최적화
* 링크를 클릭 하면 경우가 콘텐츠를 예측 하는 데 도움이 공용 친화적 Url의 사용 허용
* 끝점을 보호 하는 안전 하지 않은 요청 리디렉션
* 이미지 hotlinking 방지

여러 가지 방법으로 URL을 변경 하 고, regex, Apache mod_rewrite 모듈 규칙, IIS를 다시 작성 모듈 규칙을 포함 하 고, 사용자 지정 규칙 논리를 사용 하 여에 대 한 규칙을 정의할 수 있습니다. 이 문서는 ASP.NET Core 응용 프로그램에서 URL 다시 쓰기 미들웨어를 사용 하는 방법에 대 한 지침이 포함 된 URL 다시 쓰기를 소개 합니다.

> [!NOTE]
> URL 다시 쓰기 응용 프로그램의 성능이 저하 될 수 있습니다. 가능한 경우 규칙의 복잡성와 수를 제한 해야 합니다.

## <a name="url-redirect-and-url-rewrite"></a>URL 리디렉션 및 URL 다시 쓰기
단어 사이의 차이 *URL 리디렉션* 및 *URL 재작성* 에 미묘한 보일 수 있지만 첫 번째 요소가 있 클라이언트에 리소스를 제공 하는 데 중요 한 이점이 있습니다. ASP.NET Core URL 다시 쓰기 미들웨어는 모두에 대 한 필요성을 충족 수 있습니다.

A *URL 리디렉션* 는 클라이언트 쪽 작업으로, 다른 주소에서 리소스에 액세스 하려면 클라이언트에 명령입니다. 서버에 대 한 왕복 걸리며 클라이언트는 리소스에 대 한 새 요청을 클라이언트로 반환 된 리디렉션 URL 브라우저의 주소 표시줄에 나타납니다. 경우 `/resource` 은 *리디렉션* 를 `/different-resource`, 클라이언트 요청 `/resource`, 서버 클라이언트에서 리소스를 가져와야 하는 응답 `/different-resource` 리디렉션 임을 나타내는 상태 코드와 함께 임시 또는 영구 합니다. 클라이언트에 리디렉션 URL에 대 한 리소스에 대 한 새 요청을 실행합니다.

![WebAPI 서비스 끝점 v2 (버전 2) 서버를 일시적으로 변경 버전 (v1) 1에서에서 되었습니다. 클라이언트에서 버전 1 경로 /v1/api 서비스에 대 한 요청을 만듭니다. 서버 응답을 보냅니다 302 (있음)는 서비스에 대 한 새, 임시 경로와 /v2/api 버전 2에 있습니다. 클라이언트는 두 번째 요청 리디렉션 URL에서 서비스에 보냅니다. 서버 (정상) 상태 코드가 200 인 응답합니다.](url-rewriting/_static/url_redirect.png)

를 다른 URL로 요청을 리디렉션하는 경우 리디렉션 영구 또는 임시 인지 여부를 나타낼 수 있습니다. 301 (영구적 이동) 상태 코드는 리소스에 대 한 모든 향후 요청 새 URL을 사용 하도록 클라이언트에 게 지시 하 시겠습니까 고 있는 리소스에 영구 새 URL 사용 됩니다. *301 상태 코드를 받을 때 클라이언트는 응답을 캐시할 수 있습니다.* 302 (있음) 상태 코드 변경, 클라이언트를 저장 하 고 리디렉션 URL을 나중에 다시 사용 하지 않아야 하 여기서 리디렉션은 임시 또는 제목 일반적으로 사용 됩니다. 자세한 내용은 참조 [RFC 2616: 상태 코드 정의](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)합니다.

A *URL 재작성* 다른 리소스 주소에서 리소스를 제공 하는 서버 쪽 작업입니다. URL 다시 쓰기 서버에 대 한 라운드트립이 필요 하지 않습니다. 다시 쓴된 URL 클라이언트에 반환 되지 않습니다 및 브라우저의 주소 표시줄에 표시 되지 않습니다. 때 `/resource` 은 *다시 작성* 를 `/different-resource`, 클라이언트 요청 `/resource`와 서버 *내부적으로* 인출에서 리소스 `/different-resource`합니다. 클라이언트를 다시 작성된 URL에 리소스를 검색할 수 있지만 해당 요청을 수행 하 고 응답을 받는 리소스 다시 작성된 URL에 있는지 클라이언트 정보 되지 않습니다.

![WebAPI 서비스 끝점 (v1) 버전 1에서에서 v2 (버전 2) 서버에서 변경 되었습니다. 클라이언트에서 버전 1 경로 /v1/api 서비스에 대 한 요청을 만듭니다. 요청 URL은 버전 2 경로 /v2/api에서 서비스에 액세스 하는 다시 작성 합니다. 서비스는 200 (정상)이 상태 코드를 사용 하 여 클라이언트에 응답합니다.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>URL 다시 쓰기 샘플 응용 프로그램
URL 다시 쓰기 사용 하 여 미들웨어의 기능을 탐색할 수 있습니다는 [URL 다시 쓰기 샘플 응용 프로그램](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)합니다. 응용 프로그램 다시 쓰기를 적용 하 고 리디렉션 규칙 및 다시 작성 또는 리디렉션 URL을 보여 줍니다.

## <a name="when-to-use-url-rewriting-middleware"></a>URL 다시 쓰기 미들웨어를 사용 하는 경우
URL 다시 쓰기 미들웨어를 사용 하 여 사용할 수 없는 경우는 [URL 재작성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite) Windows 서버에서 iis는 [Apache mod_rewrite 모듈](https://httpd.apache.org/docs/2.4/rewrite/) Apache 서버의 [Nginx에URL다시쓰기](https://www.nginx.com/blog/creating-nginx-rewrite-rules/), 응용 프로그램에서 호스팅되는 또는 [HTTP.sys 서버](xref:fundamentals/servers/httpsys) (이전의 [WebListener](xref:fundamentals/servers/weblistener)). 기술을 사용 하는 서버 기반 URL 다시 쓰기 IIS, Apache 또는 Nginx 주된 이유는 있는지 미들웨어 이러한 모듈의 전체 기능을 지원 하지 않습니다 및 미들웨어의 성능을 때문일 수와 일치 하지 않습니다는 모듈입니다. 그러나 일부의 기능은 사용 하지 않는 ASP.NET Core 프로젝트와 같은 서버 모듈의 `IsFile` 및 `IsDirectory` IIS 재작성 모듈의 제약 조건입니다. 이러한 시나리오에서 미들웨어를 대신 사용 합니다.

## <a name="package"></a>패키지
미들웨어를 프로젝트에 포함 하려면 추가에 대 한 참조는 [ `Microsoft.AspNetCore.Rewrite` ](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite/) 패키지 합니다. 이 기능은 이상 ASP.NET Core 1.1을 대상으로 하는 응용 프로그램에 사용할 수 있습니다.

## <a name="extension-and-options"></a>확장 및 옵션
URL 재작성을 설정 하 고 규칙의 인스턴스를 만들어 리디렉션하는 `RewriteOptions` 각 규칙에 대 한 확장 메서드를 사용 하 여 클래스입니다. 여러 규칙 싶다는 의사를 처리 하는 순서를 연결 합니다. `RewriteOptions` 으로 요청 파이프라인에 추가 되는 URL 다시 쓰기 미들웨어로 전달 되 `app.UseRewriter(options);`합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1)]

---

### <a name="url-redirect"></a>URL 리디렉션
사용 하 여 `AddRedirect` 요청을 리디렉션할 수 있습니다. 첫 번째 매개 변수는 받는 URL의 경로에 일치 하는 regex의 포함 되어 있습니다. 두 번째 매개 변수 대체 문자열입니다. 세 번째 매개 변수가 있는 경우 상태 코드를 지정 합니다. 상태 코드를 지정 하지 않으면 기본값으로 302 (있음)을 나타내는 리소스는 일시적으로 이동 또는 대체 합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=2)]

---

개발자 도구를 사용 하도록 설정 된 브라우저에서 요청 경로로 샘플 응용 프로그램을 할 `/redirect-rule/1234/5678`합니다. 요청 경로 일치 하는 regex `redirect-rule/(.*)`, 및 경로 아래 템플릿으로 바뀝니다 `/redirected/1234/5678`합니다. 리디렉션 URL 302 (있음) 상태 코드를 사용 하 여 클라이언트에 다시 전송 됩니다. 브라우저에서 브라우저의 주소 표시줄에 표시 되는 리디렉션 URL에서 새 요청 합니다. 리디렉션 URL에 일치 하는 샘플 앱에 없는 규칙이, 하므로 두 번째 요청 응용 프로그램에서 보낸 200 (정상) 응답을 수신 및 응답의 본문으로 리디렉션 URL을 표시 합니다. URL은 서버로 왕복이 수행 됩니다 *리디렉션*합니다.

> [!WARNING]
> 리디렉션 규칙을 설정할 때 주의 해야 합니다. 리디렉션 규칙 리디렉션 이후에 포함 하 여 응용 프로그램에 각 요청에 대해 평가 됩니다. 가 실수로 쉽게 무한 리디렉션 루프가 생성 합니다.

원래 요청:`/redirect-rule/1234/5678`

![브라우저 창을 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect.png)

괄호 안에 포함 된 식의 일부 라고는 *캡처 그룹*합니다. 점 (`.`) 식의 의미 *임의의 문자*합니다. 별표 (`*`) 나타냅니다 *앞에 오는 문자 0 회 이상 일치*합니다. 따라서 URL의 마지막 두 개의 경로 세그먼트 `1234/5678`, 캡처 그룹에 의해 캡처된 `(.*)`합니다. 후 요청 URL에서 제공한 값 `redirect-rule/` 이 단일 캡처 그룹에 의해 캡처됩니다.

대체 문자열에 캡처된 그룹은 달러 기호를 사용 하 여 문자열에 삽입 (`$`) 다음 캡처의 시퀀스 번호입니다. 첫 번째 캡처 그룹 값에 따라 획득 된은 `$1`, 두 번째 `$2`, 하며 프로그램 정규식에서 캡처 그룹에 대 한 시퀀스에서 지속 합니다. 캡처된 그룹이 하나만 있는에서 경우 리디렉션 규칙 regex 샘플 응용 프로그램을 인 대체 문자열에 삽입 된 그룹이 하나만 있으므로 `$1`합니다. URL은 규칙을 적용 하면 `/redirected/1234/5678`합니다.

<a name=url-redirect-to-secure-endpoint></a>
### <a name="url-redirect-to-a-secure-endpoint"></a>보안 끝점을 URL 리디렉션
사용 하 여 `AddRedirectToHttps` 동일한 호스트 및 HTTPS를 사용 하 여 경로에 HTTP 요청을 리디렉션할 수 (`https://`). 상태 코드는 제공 되지 않는 경우 미들웨어 302 (있음) 기본값이 됩니다. 포트를 제공 하지 않으면, 미들웨어 기본적으로 `null`를로 변경 하는 프로토콜을 의미 하는 `https://` 클라이언트 포트 443에서 리소스에 액세스 합니다. 상태 코드 301 (영구적 이동)로 설정 하 고 5001이 고 다른 포트로 변경 하는 방법을 보여 줍니다.
```csharp
var options = new RewriteOptions()
    .AddRedirectToHttps(301, 5001);

app.UseRewriter(options);
```
사용 하 여 `AddRedirectToHttpsPermanent` 안전 하지 않은 요청을 동일한 호스트와 보안 HTTPS 프로토콜을 사용 하 여 경로 리디렉션하도록 (`https://` 포트 443). 상태 코드 301 (영구적 이동)를 설정 하는 미들웨어입니다.

샘플 응용 프로그램은 사용 하는 방법을 보여 주는 수 `AddRedirectToHttps` 또는 `AddRedirectToHttpsPermanent`합니다. 확장 메서드를 추가 `RewriteOptions`합니다. 모든 URL에서 응용 프로그램에 안전 하지 않은 요청을 확인 합니다. 자체 서명 된 인증서를 신뢰할 수 있는지 경고 브라우저 보안을 해제 합니다.

사용 하 여 원래 요청 `AddRedirectToHttps(301, 5001)`:`/secure`

![브라우저 창을 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect_to_https.png)

사용 하 여 원래 요청 `AddRedirectToHttpsPermanent`:`/secure`

![브라우저 창을 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>URL 다시 쓰기
사용 하 여 `AddRewrite` Url을 다시 작성 하기 위한 규칙을 만듭니다. 첫 번째 매개 변수는 들어오는 URL 경로에 일치 하는 regex의 포함 되어 있습니다. 두 번째 매개 변수 대체 문자열입니다. 세 번째 매개 변수 `skipRemainingRules: {true|false}`, 현재 규칙이 적용 되는 경우에 추가 재작성 규칙 건너뛸 것인지 여부는 미들웨어를 나타냅니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=6)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=3)]

---

원래 요청:`/rewrite-rule/1234/5678`

![브라우저 창을 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_rewrite.png)

정규식에서 첫 번째 점은 캐럿 (`^`) 식의 시작 부분에 있습니다. 이 URL 경로의 시작 부분에서 일치 하는 시작 됨을 의미 합니다.

리디렉션 규칙을 사용 하면 앞의 예제에서 `redirect-rule/(.*)`, 정규식의 시작 부분에 없는 캐럿; 따라서 모든 수 앞에 있는 문자 `redirect-rule/` 성공한 일치에 대 한 경로에 있습니다.

| Path                               | 일치 |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | 예   |
| `/my-cool-redirect-rule/1234/5678` | 예   |
| `/anotherredirect-rule/1234/5678`  | 예   |

다시 쓰기 규칙 `^rewrite-rule/(\d+)/(\d+)`, 경로로 시작 하는 경우에 일치 `rewrite-rule/`합니다. 다시 쓰기 규칙와 위의 리디렉션 규칙 간에 일치 하는 차이 확인 합니다.

| Path                              | 일치 |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | 예   |
| `/my-cool-rewrite-rule/1234/5678` | 아니요    |
| `/anotherrewrite-rule/1234/5678`  | 아니요    |

다음은 `^rewrite-rule/` 부분 식의은 두 개의 캡처 그룹 `(\d+)/(\d+)`합니다. `\d` 의미 *숫자 (number)*합니다. 더하기 기호 (`+`) 의미 *앞에 오는 문자를 하나 이상의 일치*합니다. 따라서 URL에는 슬래시 다음에 다른 숫자와 앞에 숫자가 포함 되어야 합니다. 그룹은 다시 작성된 URL에 삽입이 캡처 `$1` 및 `$2`합니다. 다시 쓰기 규칙 대체 문자열은 문자열에 캡처된 그룹을 배치합니다. 요청 된 경로 `/rewrite-rule/1234/5678` 에서 리소스를 가져올 수는 다시 작성 `/rewritten?var1=1234&var2=5678`합니다. 쿼리 문자열은 원래 요청에 있는 경우 해당 데이터가 보존 됩니다 때 URL을 다시 작성 합니다.

서버 리소스를 가져올 수 없는 왕복이 있습니다. 리소스가 존재 인출 개이고 200 (OK) 상태 코드를 사용 하 여 클라이언트에 반환 합니다. 클라이언트가 리디렉션 없는 때문에 브라우저 주소 표시줄에 URL이 변경 되지 않습니다. 클라이언트의 경우, 관련해 서 URL 다시 쓰기 작업을 하지 발생 했습니다.

> [!NOTE]
> 사용 하 여 `skipRemainingRules: true` 가능 하면 항상, 비용이 많이 드는 프로세스 일치 규칙을 사용 하면 응용 프로그램 응답 시간이 단축 하기 때문입니다. 가장 빠른 응용 프로그램 응답:
> * 가장 자주 일치 하는 규칙에 가장 자주 일치 하는 규칙에서 다시 쓰기 규칙을 정렬 합니다.
> * 일치 하는 항목이 없는 추가 규칙 처리가 필요한 경우 나머지 규칙의 처리를 건너뜁니다.

### <a name="apache-modrewrite"></a>Apache mod_rewrite
사용 하 여 Apache mod_rewrite 규칙 적용 `AddApacheModRewrite`합니다. 규칙 파일 앱 배포 되 고 있는지 확인 합니다. 자세한 내용과 mod_rewrite 규칙의 예에 대 한 참조 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

A `StreamReader` 에서 규칙을 읽는 데 사용 되는 *ApacheModRewrite.txt* 규칙 파일입니다.

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=1,7)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

첫 번째 매개 변수를 사용는 `IFileProvider`를 통해 제공 하는 [종속성 주입](dependency-injection.md)합니다. `IHostingEnvironment` 제공 하는 삽입 된 여 `ContentRootFileProvider`합니다. 두 번째 매개 변수는이 규칙 파일을 경로 *ApacheModRewrite.txt* 샘플 응용 프로그램에서입니다.

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=4)]

---

샘플 응용 프로그램에서 요청을 리디렉션합니다 `/apache-mod-rules-redirect/(.\*)` 를 `/redirected?id=$1`합니다. 응답 상태 코드 302 (있음)입니다.

[!code[Main](url-rewriting/samples/2.x/ApacheModRewrite.txt)]

원래 요청:`/apache-mod-rules-redirect/1234`

![브라우저 창을 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_apache_mod_redirect.png)

##### <a name="supported-server-variables"></a>지원 되는 서버 변수
미들웨어는 다음 Apache mod_rewrite 서버 변수를 지원합니다.
* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* I P V 6
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* 서버 _ 포트
* SERVER_PROTOCOL
* 시간
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>IIS URL 재작성 모듈 규칙
IIS URL 재작성 모듈에 적용 되는 규칙을 사용 하려면 사용 `AddIISUrlRewrite`합니다. 규칙 파일 앱 배포 되 고 있는지 확인 합니다. 사용할 미들웨어 지시 하지 않는 프로그램 *web.config* 파일을 Windows Server IIS에서 실행 합니다. Iis에서 이러한 규칙 외부에 저장 해야 하면 *web.config* IIS 재작성 모듈와 충돌 하지 않도록 합니다. 자세한 내용과 예는 IIS URL 재작성 모듈 규칙에 대 한 참조 [를 사용 하 여 Url 재작성 모듈 2.0](https://docs.microsoft.com/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) 및 [URL 재작성 모듈 구성 참조](https://docs.microsoft.com/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

A `StreamReader` 에서 규칙을 읽는 데 사용 되는 *IISUrlRewrite.xml* 규칙 파일입니다.

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=2,8)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

첫 번째 매개 변수를 사용는 `IFileProvider`, 두 번째 매개 변수, 즉 XML 규칙 파일에 경로 반면, *IISUrlRewrite.xml* 샘플 응용 프로그램에서입니다.

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=5)]

---

샘플 응용 프로그램의 요청을 다시 작성 `/iis-rules-rewrite/(.*)` 를 `/rewritten?id=$1`합니다. 응답은 200 (정상)이 상태 코드를 사용 하 여 클라이언트에 전송 됩니다.

[!code-xml[Main](url-rewriting/samples/2.x/IISUrlRewrite.xml)]

원래 요청:`/iis-rules-rewrite/1234`

![브라우저 창을 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_iis_url_rewrite.png)

영향을 미치는 방식 앱 바람직하지 않은 방법으로 구성 된 서버 수준 규칙 활성 IIS를 다시 작성 모듈 있는 경우 응용 프로그램에 대 한 IIS 재작성 모듈을 해제할 수 있습니다. 자세한 내용은 참조 [비활성화 IIS 모듈](xref:hosting/iis-modules#disabling-iis-modules)합니다.

#### <a name="unsupported-features"></a>지원 되지 않는 기능

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

출시 미들웨어 2.x ASP.NET 코어는 다음과 같은 IIS URL 재작성 모듈 기능을 지원 하지 않습니다.
* 아웃 바운드 규칙
* 사용자 지정 서버 변수
* 와일드카드
* LogRewrittenUrl

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

출시 미들웨어 1.x ASP.NET 코어는 다음과 같은 IIS URL 재작성 모듈 기능을 지원 하지 않습니다.
* 전역 규칙
* 아웃 바운드 규칙
* 지도 다시 작성
* CustomResponse 동작
* 사용자 지정 서버 변수
* 와일드카드
* 동작: CustomResponse
* LogRewrittenUrl

---

#### <a name="supported-server-variables"></a>지원 되는 서버 변수
미들웨어는 다음 IIS URL 재작성 모듈 서버 변수를 지원합니다.
* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> 가져올 수도 있습니다는 `IFileProvider` 통해는 `PhysicalFileProvider`합니다. 이 방법은 규칙 파일 프로그램 재작성의 위치에 대 한 유연성을 제공할 수 있습니다. 다시 쓰기 규칙 파일 제공 된 경로에서 서버에 배포 되 고 있는지 확인 합니다.
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>메서드 기반 규칙
사용 하 여 `Add(Action<RewriteContext> applyRule)` 메서드에서 사용자 고유의 규칙 논리를 구현할 수 있습니다. `RewriteContext` 노출는 `HttpContext` 메서드에서 사용 합니다. `context.Result` 확인 방법을 추가 파이프라인 처리 됩니다.

| 컨텍스트입니다. 결과                       | 작업                                                          |
| ------------------------------------ | --------------------------------------------------------------- |
| `RuleResult.ContinueRules`(기본값) | 계속 규칙 적용                                         |
| `RuleResult.EndResponse`             | 규칙 적용을 중지 하 고 응답 보내기                       |
| `RuleResult.SkipRemainingRules`      | 규칙 적용을 중지 하 고 미들웨어 컨텍스트 보내기 |

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=9)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=6)]

---

샘플 응용 프로그램으로 끝나는 경로 대 한 요청을 리디렉션하는 방법을 보여 줍니다. *.xml*합니다. 에 대 한 요청을 수행 하는 경우 `/file.xml`로 리디렉션되도록 `/xmlfiles/file.xml`합니다. 상태 코드 301 (영구적 이동)로 설정 됩니다. 리디렉션에 대 한 명시적으로 설정 해야 응답;의 상태 코드 그렇지 않은 경우 200 (OK) 상태 코드가 반환 되 고 클라이언트에 리디렉션도 발생 하지 않습니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/RewriteRules.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet2)]

---

원래 요청:`/file.xml`

![브라우저 창을 요청 및 file.xml에 대 한 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect_xml_requests.png)

### <a name="irule-based-rule"></a>IRule 기반 규칙
사용 하 여 `Add(IRule)` 에서 파생 된 클래스에서 사용자 고유의 규칙 논리를 구현 하려면 `IRule`합니다. 사용 하는 `IRule` 규칙 메서드 기반 방식을 사용 하 여 보다 큰 유연성을 제공 합니다. 파생 된 클래스에 대 한 매개 변수에서 전달 될 수 있는 생성자를 포함 될 수 있습니다는 `ApplyRule` 메서드.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=10-11)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=7-8)]

---

샘플 응용 프로그램에 대 한 매개 변수 값의 `extension` 및 `newPath` 여러 조건을 모두 충족 하도록 선택 합니다. `extension` 값을 포함 해야 하며 값 이어야 합니다 *.png*, *.jpg*, 또는 *.gif*합니다. 경우는 `newPath` 유효 하지는 `ArgumentException` throw 됩니다. 에 대 한 요청을 수행 하는 경우 *image.png*로 리디렉션되도록 `/png-images/image.png`합니다. 에 대 한 요청을 수행 하는 경우 *image.jpg*로 리디렉션되도록 `/jpg-images/image.jpg`합니다. 상태 코드가 301 (영구적 이동)을으로 설정 되어 및 `context.Result` 처리 규칙을 중지 하 고 응답을 보내기로 설정 됩니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/RewriteRules.cs?name=snippet2)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/RewriteRule.cs?name=snippet1)]

---

원래 요청:`/image.png`

![브라우저 창을 요청 및 image.png에 대 한 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect_png_requests.png)

원래 요청:`/image.jpg`

![브라우저 창을 요청 및 image.jpg에 대 한 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>정규식 예제

| Goal | Regex 문자열 &<br>일치 예 | 대체 문자열 &<br>출력 예 |
| ---- | :-----------------------------: | :------------------------------------: |
| 쿼리 문자열에 경로 다시 작성 | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| 후행 슬래시를 제거 합니다. | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| 후행 슬래시를 적용 합니다. | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| 특정 요청을 다시 작성 하지 마십시오. | `(.*[^(\.axd)])$`<br>예:`/resource.htm`<br>아니요:`/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| URL 세그먼트를 다시 정렬 | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| URL 세그먼트를 대체 합니다. | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

## <a name="additional-resources"></a>추가 리소스
* [응용 프로그램 시작](startup.md)
* [미들웨어](middleware.md)
* [.NET에서의 정규식](/dotnet/articles/standard/base-types/regular-expressions)
* [정규식 언어 - 빠른 참조](/dotnet/articles/standard/base-types/quick-ref)
* [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)
* [(IIS) 용 Url 재작성 모듈 2.0을 사용 하 여](https://docs.microsoft.com/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [URL 재작성 모듈 구성 참조](https://docs.microsoft.com/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [IIS URL 재작성 모듈 포럼](https://forums.iis.net/1152.aspx)
* [간단한 URL 구조를 유지 합니다.](https://support.google.com/webmasters/answer/76329?hl=en)
* [팁과 트릭 10 URL 다시 쓰기](http://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [슬래시 또는 슬래시](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
