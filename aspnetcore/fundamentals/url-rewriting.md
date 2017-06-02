---
title: "URL 재작성 ASP.NET 코어에서 미들웨어 | Microsoft 문서"
author: guardrex
description: "URL 재작성 하 고 리디렉션을 ASP.NET 핵심 응용 프로그램에서 URL 다시 쓰기 미들웨어를 사용 하는 방법에 대 한 지침을 소개 합니다."
keywords: "ASP.NET Core URL 다시 쓰기, URL 다시 쓰기, URL 리디렉션, 미들웨어, apache_mod 리디렉션 URL"
ms.author: riande
manager: wpickett
ms.date: 03/13/2017
ms.topic: article
ms.assetid: e6130638-c410-4161-9921-b658ce988bd1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/url-rewriting
ms.translationtype: Machine Translation
ms.sourcegitcommit: ec31052983921540dc13052848c77bb88edf3b19
ms.openlocfilehash: 1b74e87ab73b15f048c65983928f71ca5c04fc89
ms.contentlocale: ko-kr
ms.lasthandoff: 03/23/2017

---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>URL 재작성 ASP.NET 코어에서 미들웨어

여 [Luke Latham](https://github.com/GuardRex) 및 [Mikael Mengistu](https://github.com/mikaelm12)

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/sample)

URL 재작성은 하나 이상의 미리 정의 된 규칙을 기반으로 하는 Url 요청을 수정 하는 작업입니다. URL 재작성 하는 위치 및 주소 밀접 하 게 연결 되지 않은 리소스 위치 및 해당 주소 사이는 추상화를 만듭니다. URL 재작성은 중요 한 몇 가지 시나리오가 있습니다.
* 이동 또는 이러한 리소스에 대 한 안정적인 로케이터를 유지 관리 하는 동안 서버 리소스를 일시적 또는 영구적으로 대체
* 서로 다른 응용 프로그램 또는 응용 프로그램의 영역에서 처리 하는 분할 요청
* 제거, 추가 또는 들어오는 요청에서 URL 세그먼트를 다시 구성
* 공용 Url에 대 한 검색 엔진 최적화 SEO () 최적화
* 링크를 클릭 하면 될 콘텐츠를 예측 하는 데 도움이 공용 친화적인 Url의 사용을 허용 합니다.
* 끝점 보안 설정에 대 한 보안 되지 않은 요청을 리디렉션
* 이미지 hotlinking 방지

정규식 (regex) 일치 규칙, Apache mod_rewrite 모듈에 따라 규칙, IIS 다시 쓰기 모듈에 고유한 메서드 및 클래스 규칙 논리를 기반으로 하는 규칙을 포함 하 여 여러 가지 방법으로 URL을 변경 하는 것에 대 한 규칙을 정의할 수 있습니다. 이 문서에서는 URL 재작성 ASP.NET 핵심 응용 프로그램에서 URL 다시 쓰기 미들웨어를 사용 하는 방법에 대 한 지침을 소개 합니다.

> [!NOTE]
> URL 재작성 응용 프로그램의 성능을 줄일 수 있습니다. 사용할 수 있는 규칙의 복잡도 수를 제한 해야 합니다.

## <a name="url-redirect-and-url-rewrite"></a>URL 리디렉션 및 URL 다시 쓰기
단어 사이에서 차이가 *URL 리디렉션* 및 *URL 다시 쓰기* 에 미묘한 보일 수 있지만 첫 번째 않았지만 클라이언트에 리소스를 제공 하는 데 중요 한 의미를가지고 있습니다. ASP.NET Core URL 다시 쓰기 미들웨어는 모두에 대 한 필요성을 충족 수 있습니다.

A *URL 리디렉션* 은 클라이언트 쪽 작업으로 다른 주소에서 리소스에 액세스 하는 클라이언트에 명령 하는 위치입니다. 서버에 대 한 라운드트립이 필요 하 고 클라이언트에 반환 하는 경우 리디렉션 URL이 클라이언트는 리소스에 대 한 새 요청을 수행할 때 브라우저의 주소 표시줄에 나타납니다. 경우 `/resource` 는 *리디렉션* 를 `/different-resource`, 클라이언트에서 요청 `/resource`, 클라이언트에서 리소스를 가져와야 하는 서버는 응답 및 `/different-resource` 리디렉션 일시적 또는 영구적 임을 나타내는 상태 코드와 함께 합니다. 클라이언트가 리디렉션 URL에 대 한 리소스에 대 한 새 요청을 실행 합니다.

![서버에서 버전 2 (v2)로 WebAPI 서비스 끝점을 일시적으로 변경 버전 (v1) 1에서에서 되었습니다. 클라이언트는 서비스 버전 1 경로 /v1/api에는 요청을 만듭니다. 서버 응답을 보냅니다 302 (찾기) 서비스에 대 한 새, 임시 경로 버전 2 /v2/api. 클라이언트는 서비스 리디렉션 URL에는 두 번째 요청을 만듭니다. 서버는 200 (OK) 상태 코드로 응답합니다.](url-rewriting/_static/url_redirect.png)

다른 URL로 요청을 리디렉션, 경우에 영구 또는 임시 리디렉션 적용 되는지 표시 됩니다. 301 (영구적 이동) 상태 코드는 모든 향후 요청 된 리소스에 대 한 새 URL을 사용 해야 하는 클라이언트에 게 지시 하는 데 여기서는 리소스에 새 영구 URL 하 고 원하는 사용 됩니다. 301 상태 코드를 받을 때 클라이언트는 응답을 캐시 합니다. 302 (Found) 상태 코드 변경 클라이언트 해야 하지 저장 하 고 리디렉션 URL을 나중에 다시 사용할 수 있도록 하려면 여기서 리디렉션을 임시 또는 일반적으로 제목 사용 됩니다. 자세한 내용은 참조 [RFC 2616: 상태 코드 정의](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)합니다.

A *URL 다시 쓰기* 리소스는 다른 리소스 주소를 제공 하는 서버 쪽 작업입니다. URL 다시 쓰기는 서버에 대 한 라운드트립이 필요 하지 않습니다. 재작성된 URL 클라이언트에 반환 되지 않으며 브라우저의 주소 표시줄에 표시 되지 않습니다. 때 `/resource` 는 *다시 작성* 를 `/different-resource`, 클라이언트에서 요청 `/resource`, 서버에서는 및 *내부적으로* 의 리소스를 가져올 `/different-resource`합니다. 클라이언트를 다시 작성 된 URL에서 리소스를 검색할 수 있지만 해당 요청을 수행 하 고 응답을 받을 때 다시 작성 된 URL에 리소스가 존재 하는지 클라이언트 알림이 없습니다.

![WebAPI 서비스 끝점을 버전 (v1) 1에서에서 서버 버전 (v2) 2로 변경 되었습니다. 클라이언트는 서비스 버전 1 경로 /v1/api에는 요청을 만듭니다. 버전 2 경로 /v2/api에서 서비스에 액세스 하는 요청 URL 다시 작성 되었습니다. 서비스는 200 (OK) 상태 코드를 사용 하 여 클라이언트에 응답합니다.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-application"></a>URL 다시 쓰기 예제 응용 프로그램
URL 다시 쓰기 미들웨어와 기능을 탐색할 수는 [URL 샘플 응용 프로그램을 재작성](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/sample)합니다. 응용 프로그램 다시 쓰기를 적용 하 고 리디렉션 규칙 및 결과 다시 작성 또는 리디렉션된 URL을 보여 줍니다.

## <a name="when-to-use-url-rewriting-middleware"></a>URL 다시 쓰기 미들웨어를 사용 하는 경우
URL 다시 쓰기 미들웨어를 사용 하 여 사용할 수 없는 경우는 [URL 다시 쓰기 모듈](https://www.iis.net/downloads/microsoft/url-rewrite) Windows 서버에 IIS에서의 [Apache mod_rewrite 모듈](https://httpd.apache.org/docs/2.4/rewrite/) Apache 서버에 [Nginx에서 URL 다시 쓰기](https://www.nginx.com/blog/creating-nginx-rewrite-rules/), 또는 응용 프로그램에서 호스팅되는 [WebListener 서버](xref:fundamentals/servers/weblistener)합니다. 에 포함 된 서버 기반 URL 다시 쓰기 기술을 IIS, Apache 또는 Nginx를 사용 하는 주된 이유는 미들웨어는 이러한 모듈의 전체 기능을 지원 하지 않으므로 미들웨어 성능 아마도 일치 하지 않을 수 있는 모듈입니다. 그러나 일부 기능은 사용 하지 않는 ASP.NET 핵심 프로젝트와 같은 서버 모듈의 `IsFile` 및 `IsDirectory` IIS 다시 쓰기 모듈의 제약 조건입니다. 이러한 시나리오에서 미들웨어를 대신 사용할 수 있습니다.

## <a name="package"></a>패키지
미들웨어를 프로젝트에 포함 하려면 추가에 대 한 참조는 [ `Microsoft.AspNetCore.Rewrite` ](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite/) 패키지 합니다. 미들웨어는.NET Framework 4.5.1 또는 1.3 이상을.NET 표준에 따라 다릅니다. 이 기능은 이상 ASP.NET 핵심 1.1.0을 대상으로 하는 응용 프로그램에 사용할 수 있습니다.

## <a name="extension-and-options"></a>확장 및 옵션
URL 재작성을 설정 하 고 규칙의 인스턴스를 만들어 리디렉션하는 `RewriteOptions` 각 규칙에 대 한 확장 메서드를 사용 하 여 클래스입니다. 원한다는 것을 처리 하는 순서에 여러 규칙을 연결 합니다. `RewriteOptions` 와 요청 파이프라인에 추가 될 때 URL 다시 쓰기 미들웨어로 전달 되 `app.UseRewriter(options);`합니다.

[!code-csharp[주](url-rewriting/sample/Startup.cs?name=snippet1)]

### <a name="url-redirect"></a>URL 리디렉션
사용 하 여 `AddRedirect()` 요청을 리디렉션할 수 있습니다. 첫 번째 매개 변수는 들어오는 URL의 경로에 일치 하는 regex를 포함 됩니다. 두 번째 매개 변수는 대체 문자열입니다. 세 번째 매개 변수 있는 경우 상태 코드를 지정 합니다. 상태 코드를 지정 하지 않으면 기본값 302 (찾기)에 리소스를 일시적으로 이동 하거나 교체를 나타냅니다.

[!code-csharp[주](url-rewriting/sample/Startup.cs?name=snippet1&highlight=2)]

개발자 도구를 사용 하도록 설정 된 브라우저에서 요청 경로로 샘플 응용 프로그램을 할 `/redirect-rule/1234/5678`합니다. 요청 경로 일치 하는 regex `redirect-rule/(.*)`, 경로 대체 `/redirected/1234/5678`합니다. 리디렉션 URL 302 (Found) 상태 코드를 사용 하 여 클라이언트에 다시 전송 됩니다. 브라우저에서 브라우저의 주소 표시줄에 나타나는 리디렉션 URL에서 새 요청 합니다. 샘플 응용 프로그램에 규칙이 없습니다 리디렉션 URL과 일치 두 번째 요청은 응용 프로그램에서 200 (정상) 응답을 받 및 응답의 본문으로 리디렉션 URL을 표시 합니다. URL은 서버에 전체 왕복이 수행 됩니다 *리디렉션*합니다.

원래 요청:`/redirect-rule/1234/5678`

![브라우저 창에는 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect.png)

괄호가 포함 된 식의 일부로 호출 되는 *캡처 그룹*합니다. 점 (`.`) 식의 의미 *임의의 문자*와 별표 (`*`)를 의미 *앞에 오는 문자&0; 개 이상 일치*합니다. URL의 마지막 두 개의 경로 세그먼트에 따라서 `1234/5678`, 캡처 그룹에 의해 캡처된 `(.*)`합니다. 요청 URL 뒤에 제공한 값 `redirect-rule/` 이 단일 캡처 그룹에서 포착 됩니다.

대체 문자열을 캡처된 그룹의 달러 기호를 사용 하 여 문자열에 주입 되어 (`$`) 뒤에 캡처의 시퀀스 번호입니다. 첫 번째 캡처 그룹 값을 사용 하 여 가져오는 `$1`, 두 번째 `$2`, 정규식의 캡처 그룹에 대 한 순서 대로 계속 합니다. 캡처된 그룹이 하나만 리디렉션 규칙 regex에서에서이 샘플 응용 프로그램 인 대체 문자열에 삽입 된 그룹이 하나만 있으므로 `$1`합니다. URL은 규칙을 적용 하면 `/redirected/1234/5678`합니다.

<a name=url-redirect-to-secure-endpoint></a>
### <a name="url-redirect-to-a-secure-endpoint"></a>보안 끝점 URL 리디렉션
사용 하 여 `AddRedirectToHttps()` 안전 하지 않은 요청을 동일한 호스트와 보안 HTTPS 프로토콜을 사용 하 여 경로 리디렉션하도록 (`https://`) 상태 코드 및 포트를 선택할 수 있는 유연성. 상태 코드는 제공 되지 않은 경우 미들웨어 302 (Found) 기본값은입니다. 포트 제공 되지 않은 경우 미들웨어는 기본적으로 `null`, 바뀌어 프로토콜 의미 하는 `https://` 및 클라이언트가 포트 443에서 리소스에 액세스할 수 있습니다. 상태 코드 301 (영구적 이동)를 설정 하 고 포트 5001으로 변경 하는 방법을 보여 줍니다.
```csharp
var options = new RewriteOptions()
    .AddRedirectToHttps(301, 5001);

app.UseRewriter(options);
```
사용 하 여 `AddRedirectToHttpsPermanent()` 안전 하지 않은 요청을 동일한 호스트와 보안 HTTPS 프로토콜을 사용 하 여 경로 리디렉션하도록 (`https://` 포트 443). 미들웨어는 상태 코드 301 (영구적 이동)로 설정 합니다.

샘플 응용 프로그램은 사용 하는 방법을 보여 주는 `AddRedirectToHttps()` 또는 `AddRedirectToHttpsPermanent()`합니다. 확장 메서드를 추가 하는 `RewriteOptions()`합니다. 응용 프로그램 URL에는 안전 하지 않은 요청을 확인 합니다. 브라우저에서 응답을 보려면 브라우저 보안 자체 서명 된 인증서는 신뢰할 수 없는 경고를 해제 해야 할 수 있습니다.

사용 하 여 원래 요청 `AddRedirectToHttps(301, 5001)`:`/secure`

![브라우저 창에는 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect_to_https.png)

사용 하 여 원래 요청 `AddRedirectToHttpsPermanent()`:`/secure`

![브라우저 창에는 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>URL 다시 쓰기
사용 하 여 `AddRewrite()` Url을 다시 작성 하기 위한 규칙을 만들 수 있습니다. 첫 번째 매개 변수는 들어오는 URL 경로에 일치 하는 regex를 포함 됩니다. 두 번째 매개 변수는 대체 문자열입니다. 세 번째 매개 변수 `skipRemainingRules: {true|false}`, 원인이 되는 미들웨어 현재 규칙이 적용 되는 경우 추가 다시 쓰기 규칙을 건너뛸 것인지 여부입니다.

[!code-csharp[주](url-rewriting/sample/Startup.cs?name=snippet1&highlight=3)]

원래 요청:`/rewrite-rule/1234/5678`

![브라우저 창에는 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_rewrite.png)

Regex에 알게 될 것을 캐럿 (`^`) 식의 시작 부분에 있습니다. 이 의미는 일치를 시도 하 여 URL 경로의 시작 부분에서 시작 합니다.

리디렉션 규칙을 사용 하면 앞의 예제에서 `redirect-rule/(.*)`, 정규식의 시작 부분에 없는 캐럿 않습니다; 따라서 모든 수 앞에 있는 문자 `redirect-rule/` 일치에 대 한 경로에 있습니다.

Path | 일치
--- | :---:
`/redirect-rule/1234/5678` | 예
`/my-cool-redirect-rule/1234/5678` | 예
`/anotherredirect-rule/1234/5678` | 예

다시 쓰기 규칙 `^rewrite-rule/(\d+)/(\d+)`와 함께 시작 하는 경우에 경로 찾는다는 것 `rewrite-rule/`합니다. 다시 쓰기 규칙을 아래와 위의 리디렉션 규칙 간에 일치 하는 차이 확인해 보세요.

Path | 일치
--- | :---:
`/rewrite-rule/1234/5678` | 예
`/my-cool-rewrite-rule/1234/5678` | 아니요
`/anotherrewrite-rule/1234/5678` | 아니요

다음은 `^rewrite-rule/` 부분은 식의는 두 개의 캡처 그룹을 `(\d+)/(\d+)`. `\d` 의미 *자릿수 (숫자)와 일치*합니다. 더하기 기호 (`+`) 의미 *앞에 오는 문자를 하나 이상 일치*합니다. 따라서 URL 숫자는 슬래시 뒤 다른 숫자가 뒤에 포함 되어야 합니다. 이러한 캡처 그룹 결과 다시 작성 된 URL로 주입 됩니다 `$1` 및 `$2`합니다. 다시 쓰기 규칙 대체 문자열은 쿼리 문자열에 캡처된 그룹 붙여 넣습니다. 요청한 경로 `/rewrite-rule/1234/5678` 를 리소스를 가져오는 다시 작성 되었습니다 `/rewritten?var1=1234&var2=5678`합니다. 쿼리 문자열이 원래 요청에 있는 경우 URL은 다시 작성 하는 경우 보존 됩니다.

서버 리소스를 얻을 수 없는 왕복이 있습니다. 리소스 존재 인출 있으며 200 (OK) 상태 코드를 사용 하 여 클라이언트에 반환 합니다. 클라이언트가 리디렉션 되지 때문에 브라우저 주소 표시줄에 URL이 변경 되지 않습니다. 관련해 서는 클라이언트의 경우 URL 다시 쓰기 작업을 하지 발생 했습니다.

> [!NOTE]
> `skipRemainingRules: true`해야 하므로 사용할 수 가능한 경우 항상 일치 규칙은 비용이 많이 드는 프로세스 및 응용 프로그램 응답 시간이 느려집니다. 가장 빠른 응용 프로그램 응답에 대 한 주문 프로그램 재작성 가장에서 자주 빈도가 낮은 일치 하는 일치 규칙과 일치 하는 항목이 없는 추가 규칙 처리 해야 하는 경우 나머지 규칙의 처리를 건너뜁니다.

### <a name="apache-modrewrite"></a>Apache mod_rewrite
사용 하 여 Apache mod_rewrite 규칙을 적용할 수 있습니다 `AddApacheModRewrite()`합니다. 첫 번째 매개 변수를 사용는 `IFileProvider`를 통해 샘플 응용 프로그램에 제공 된 [종속성 주입](dependency-injection.md) 삽입 하 여는 `IHostingEnvironment` 제공 하는 데 사용 하는 `ContentRootFileProvider`합니다. 두 번째 매개 변수는, 즉 규칙 파일에 경로 *ApacheModRewrite.txt* 샘플 응용 프로그램에서입니다. 규칙 파일은 응용 프로그램과 함께 배포 되었는지 확인 해야 합니다. 자세한 내용과 mod_rewrite 규칙의 예에 대 한 참조 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)합니다.

[!code-csharp[주](url-rewriting/sample/Startup.cs?name=snippet1&highlight=4)]

샘플 응용 프로그램에서 요청을 리디렉션하 `/apache-mod-rules-redirect/(.\*)` 를 `/redirected?id=$1`합니다. 응답 상태 코드 302 (Found)입니다.

[!code[Main](url-rewriting/sample/ApacheModRewrite.txt)]

원래 요청:`/apache-mod-rules-redirect/1234`

![브라우저 창에는 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_apache_mod_redirect.png)

##### <a name="supported-server-variables"></a>지원 되는 서버 변수
미들웨어 다음 Apache mod_rewrite 서버 변수를 지원합니다.
* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* I P V&6;
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

### <a name="iis-url-rewrite-module-rules"></a>IIS URL 다시 쓰기 모듈 규칙
IIS URL 다시 쓰기 모듈은 일반적으로 적용 하는 규칙을 사용 하려면 사용 `AddIISUrlRewrite()`합니다. 첫 번째 매개 변수는는 `IFileProvider`반면 두 번째 매개 변수는, 즉 XML 규칙 파일에 경로 *IISUrlRewrite.xml* 샘플 응용 프로그램에서입니다. 규칙 파일은 응용 프로그램과 함께 배포 되었는지 확인 해야 합니다. 이 가리키지 않습니다 프로그램 *web.config* 파일을 외부에서 이러한 규칙을 저장 해야 프로그램 *web.config* IIS 다시 쓰기 모듈와 충돌 하지 않도록 합니다. 자세한 내용 및 IIS URL 다시 쓰기 모듈 규칙의 예에 대 한 참조 [모듈 2.0를 다시 작성 Url를 사용 하 여](https://www.iis.net/learn/extensions/url-rewrite-module/using-url-rewrite-module-20) 및 [URL 다시 쓰기 모듈 구성 참조](https://www.iis.net/learn/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)합니다.

[!code-csharp[주](url-rewriting/sample/Startup.cs?name=snippet1&highlight=5)]

샘플 응용 프로그램의 요청을 다시 작성은 `/iis-rules-rewrite/(.*)` 를 `/rewritten?id=$1`합니다. 응답이 200 (OK) 상태 코드를 사용 하 여 클라이언트에 전송 됩니다.

[!code-xml[주](url-rewriting/sample/IISUrlRewrite.xml)]

원래 요청:`/iis-rules-rewrite/1234`

![브라우저 창에는 요청 및 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_iis_url_rewrite.png)

현재 IIS 다시 쓰기 모듈 바람직하지 않은 방법으로 응용 프로그램에 영향을 주게 구성 된 서버 수준 규칙 있는 경우 응용 프로그램에 대 한 IIS 다시 쓰기 모듈을 비활성화할 수 있습니다. 자세한 내용은 참조 [비활성화 IIS 모듈](xref:hosting/iis-modules#disabling-iis-modules)합니다.

#### <a name="unsupported-features"></a>지원 되지 않는 기능
미들웨어는 다음과 같은 IIS URL 다시 쓰기 모듈 기능을 지원 하지 않습니다.
* 전역 규칙 ([기본 미들웨어 #169](https://github.com/aspnet/BasicMiddleware/pull/169))
* 지도 다시 작성 ([기본 미들웨어 #168](https://github.com/aspnet/BasicMiddleware/pull/168))
* CustomResponse 작업 ([기본 미들웨어 #135](https://github.com/aspnet/BasicMiddleware/issues/135))
* 사용자 지정 서버 변수 ([기본 미들웨어 #183](https://github.com/aspnet/BasicMiddleware/issues/183))
* trackAllCaptures ([기본 미들웨어 #178](https://github.com/aspnet/BasicMiddleware/pull/178))
* 와일드카드
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>지원 되는 서버 변수
미들웨어 다음 IIS URL 다시 쓰기 모듈 서버 변수를 지원합니다.
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
> 가져올 수도 있습니다는 `IFileProvider` 통해는 `PhysicalFileProvider`합니다. 이 방법은 규칙 파일 프로그램 재작성의 위치에 대 한 유연성을 제공할 수 있습니다. 다시 쓰기 규칙 파일은 제공 된 경로에 서버에 배포 되었는지 확인 합니다.
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>메서드 기반 규칙
사용 하 여 `Add(Action<RewriteContext> applyRule)` 메서드에서 사용자 고유의 규칙 논리를 구현 합니다. `RewriteContext` 노출은 `HttpContext` 프로그램 메서드에서 사용 합니다. `context.Result` 결정 방법을 추가 파이프라인 처리 됩니다.

`context.Result` | 작업
--- | ---
`RuleResult.ContinueRules`(기본값) | 규칙 적용을 계속합니다
`RuleResult.EndResponse` | 규칙 적용을 중지 하 고 응답을 보냅니다
`RuleResult.SkipRemainingRules` | 규칙 적용을 중지 하 고 미들웨어 컨텍스트 보내기

[!code-csharp[주](url-rewriting/sample/Startup.cs?name=snippet1&highlight=6)]

샘플 응용 프로그램으로 끝나는 경로 대 한 요청을 리디렉션하는 방법을 보여 줍니다. `.xml`합니다. 에 대 한 요청을 수행 하는 경우 `/file.xml`, 리디렉션됩니다 `/xmlfiles/file.xml`합니다. 상태 코드 301 (영구적 이동)으로 설정 됩니다. 리디렉션에 대 한 명시적으로 설정 해야 응답;의 상태 코드 그렇지 200 (OK) 상태 코드가 반환 되 고 리디렉션 클라이언트에서 발생 하지 않습니다.

[!code-csharp[주](url-rewriting/sample/Startup.cs?name=snippet2)]

원래 요청:`/file.xml`

![브라우저 창에 추적 요청 및 응답 파일에 대 한 개발자 도구](url-rewriting/_static/add_redirect_xml_requests.png)

### <a name="irule-based-rule"></a>IRule 기반 규칙
사용 하 여 `Add(IRule)` 에서 파생 된 클래스에서 사용자 고유의 규칙 논리를 구현 `IRule`합니다. 사용 하는 `IRule` 메서드 기반 규칙을 사용 하 여을 통해 더 유연 하 게 제공 합니다. 파생 된 클래스에 대 한 매개 변수에서 전달 될 수 있는 경우에 생성자를 포함 될 수 있습니다는 `ApplyRule` 메서드.

[!code-csharp[주](url-rewriting/sample/Startup.cs?name=snippet1&highlight=7-8)]

샘플 응용 프로그램에 대 한 매개 변수의 값은 `extension` 및 `newPath` 여러 조건을 모두 충족 하도록 선택 합니다. `extension` 값을 포함 해야 하며의 값이 *.png*, *.jpg*, 또는 *.gif*합니다. 하는 경우는 `newPath` 유효 하지는 `ArgumentException` throw 됩니다. 에 대 한 요청을 수행 하는 경우 *image.png*, 리디렉션됩니다 `/png-images/image.png`합니다. 에 대 한 요청을 수행 하는 경우 *image.jpg*, 리디렉션됩니다 `/jpg-images/image.jpg`합니다. 상태 코드 301 (영구적 이동)를로 설정 되 고 `context.Result` 처리 규칙을 중지 하 고 응답을 보내는로 설정 됩니다.

[!code-csharp[주](url-rewriting/sample/RewriteRule.cs?name=snippet1)]

원래 요청:`/image.png`

![브라우저 창에는 요청 및 image.png에 대 한 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect_png_requests.png)

원래 요청:`/image.jpg`

![브라우저 창에는 요청 및 image.jpg에 대 한 응답을 추적 하는 개발자 도구](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>정규식 예제

Goal | Regex 문자열 / /<br>일치 예제 | 대체 문자열 / /<br>출력 예
--- | :---: | :---:
쿼리 문자열에 경로 다시 작성 | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123`
후행 슬래시를 제거 합니다. | `(.*)/$`<br>`/path/` | `$1`<br>`/path`
후행 슬래시를 적용 합니다. | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/`
특정 요청을 다시 작성할 필요 없이 | `(.*[^(\.axd)])$`<br>예:`/resource.htm`<br>없음:`/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd`
URL 세그먼트를 다시 정렬 | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1`
URL 세그먼트를 대체 합니다. | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3`

## <a name="resources"></a>리소스
* [응용 프로그램 시작](startup.md)
* [미들웨어](middleware.md)
* [.NET에서의 정규식](https://docs.microsoft.com/en-us/dotnet/articles/standard/base-types/regular-expressions)
* [정규식 언어 - 빠른 참조](https://docs.microsoft.com/en-us/dotnet/articles/standard/base-types/quick-ref)
* [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)
* [(IIS)에 대 한 Url 재작성 모듈 2.0을 사용 하 여](https://www.iis.net/learn/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [URL 다시 쓰기 모듈 구성 참조](https://www.iis.net/learn/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [IIS URL 다시 쓰기 모듈 포럼](https://forums.iis.net/1152.aspx)
* [간단한 URL 구조를 유지 합니다.](https://support.google.com/webmasters/answer/76329?hl=en)
* [팁과 트릭&10; URL 다시 쓰기](http://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [슬래시 또는 슬래시](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)

