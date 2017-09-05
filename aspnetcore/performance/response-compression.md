---
title: "ASP.NET Core에 대 한 응답 압축 미들웨어"
author: guardrex
description: "응답 압축 및 ASP.NET Core 응용 프로그램에서 응답 압축 미들웨어를 사용 하는 방법에 알아봅니다."
keywords: "ASP.NET Core, 성능, 응답 압축, gzip, 허용 인코딩, 미들웨어"
ms.author: riande
manager: wpickett
ms.date: 08/20/2017
ms.topic: article
ms.assetid: de621887-c5c9-4ac8-9efd-f5cc0457a134
ms.technology: aspnet
ms.prod: asp.net-core
uid: performance/response-compression
ms.openlocfilehash: b79d86358a8f1552118fac508c4cc02cf674f169
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2017
---
# <a name="response-compression-middleware-for-aspnet-core"></a>ASP.NET Core에 대 한 응답 압축 미들웨어

으로 [Luke Latham](https://github.com/GuardRex)

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples)

네트워크 대역폭은 제한 된 리소스입니다. 일반적으로 응답의 크기를 줄이면 응용 프로그램의 응답성 종종 크게 증가 합니다. 페이로드 크기를 줄이기 위해 가지 방법은 응용 프로그램의 응답을 압축 하는 것입니다.

## <a name="when-to-use-response-compression-middleware"></a>응답 압축 미들웨어를 사용 하는 경우
IIS, Apache 또는 Nginx 여기서 미들웨어의 성능을 아마도 일치 하지 않게 서버 모듈의 응답 서버 기반 압축 기술을 사용 합니다. 사용할 수 있는 경우 응답 압축 미들웨어를 사용 합니다.
* [IIS 동적 압축이 모듈](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
* [Apache mod_deflate 모듈](http://httpd.apache.org/docs/current/mod/mod_deflate.html)
* [NGINX 압축 및 압축 풀기](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* [HTTP.sys 서버의](xref:fundamentals/servers/httpsys) (이전의 [WebListener](xref:fundamentals/servers/weblistener))
* [Kestrel](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>응답 압축
일반적으로 압축 되지 않음 응답에서 응답 압축 이점을 얻을 수 있습니다. 일반적으로 압축 되지 않음 응답에 포함: CSS, JavaScript, HTML, XML 및 JSON입니다. PNG 파일 등의 기본적으로 압축 된 자산을 압축 하지 않아야 합니다. 기본적으로 압축 된 응답을 압축 하려고 하면 압축을 처리 하는 데 걸린 시간으로 작은 추가 크기와 전송 시간 감소가 늘린다 가능성이 됩니다. (파일의 콘텐츠 및 압축의 효율성)에 따라 약 150-1000 바이트 보다 작은 파일을 압축 안 함. 작은 파일을 압축 하는 오버 헤드는 압축 되지 않은 파일 보다 큰 압축된 된 파일을 생성할 수 있습니다.

클라이언트 전송 하 여 해당 기능의 서버를 알려야 클라이언트가 압축 된 콘텐츠를 처리할 수 있는 경우는 `Accept-Encoding` 헤더를 요청 합니다. 에 정보를 포함 해야 하는 서버 압축 된 콘텐츠를 보내는 `Content-Encoding` 헤더에 압축 된 응답 인코딩 되는 방식입니다. 미들웨어에서 지 원하는 콘텐츠 인코딩 명칭은 다음 표에 표시 됩니다.

| `Accept-Encoding`헤더 값 | 지원 되는 미들웨어 | 설명                                                 |
| :-----------------------------: | :------------------: | ----------------------------------------------------------- |
| `br`                            | 아니요                   | Brotli 압축 된 데이터 형식                               |
| `compress`                      | 아니요                   | UNIX "압축" 데이터 형식                                 |
| `deflate`                       | 아니요                   | "deflate" 내부 "zlib" 데이터 형식 압축 된 데이터     |
| `exi`                           | 아니요                   | W3C 효율적인 XML 교환                               |
| `gzip`                          | 예 (기본값)        | gzip 파일 형식                                            |
| `identity`                      | 예                  | "인코딩 없이" 식별자: 응답을 인코딩할 수 있어야 합니다. |
| `pack200-gzip`                  | 아니요                   | 네트워크 전송 형식 Java 아카이브에                   |
| `*`                             | 예                  | 명시적으로 요청 되지 않는 인코딩이 사용 가능한 모든 콘텐츠     |

자세한 내용은 참조는 [IANA 공식 콘텐츠 코딩 목록](http://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry)합니다.

미들웨어를 사용 하면 사용자 지정에 대 한 추가 압축 공급자 추가할 `Accept-Encoding` 헤더 값입니다. 자세한 내용은 참조 [사용자 지정 공급자](#custom-providers) 아래 합니다.

미들웨어는 품질 값에 응답할 수 (qvalue, `q`) 클라이언트에서 압축 체계의 우선 순위를 보낼 때 가중치입니다. 자세한 내용은 참조 [RFC 7231: 허용 인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4)합니다.

압축 알고리즘 압축 속도 압축의 효율성 간의 균형을 받습니다. *효율성* 이 컨텍스트에서 출력의 크기는 뒤에 참조 압축 합니다. 가장 작은 크기는 대부분에 의해 이루어집니다 *최적의* 압축 합니다.

요청에 관련 된 헤더, 캐싱, 송수신 압축 된 콘텐츠는 다음 표에서 설명 합니다.

| Header             | 역할 |
| ------------------ | ---- |
| `Accept-Encoding`  | 인코딩 구성을 허용 되는 클라이언트에 콘텐츠를 나타내기 위해 서버에 클라이언트에서 보냅니다. |
| `Content-Encoding` | 페이로드의 콘텐츠 인코딩을 나타내는 클라이언트에 서버에서 전송 합니다. |
| `Content-Length`   | 압축이 발생할 때의 `Content-Length` 응답 압축 되는 경우는 본문 콘텐츠가 변경 이후 헤더를 제거 합니다. |
| `Content-MD5`      | 압축이 발생할 때의 `Content-MD5` 해시는 더 이상 유효 하 고 본문 내용이 변경 되었습니다. 헤더를 제거 합니다. |
| `Content-Type`     | 콘텐츠의 MIME 형식을 지정합니다. 모든 응답을 지정 해야 해당 `Content-Type`합니다. 미들웨어의 응답을 압축할 수를 확인 하려면이 값을 확인 합니다. 집합을 지정 하는 미들웨어 [기본 MIME 형식을](#mime-types) 인코딩할 수 있지만 MIME 형식을 추가 하거나 바꿀 수 있습니다. |
| `Vary`             | 값을 사용 하 여 서버에서 전송 하는 경우 `Accept-Encoding` 클라이언트와 프록시에는 `Vary` 헤더는 클라이언트 또는 캐시 해야 하는 프록시를 나타냅니다 (다)의 값을 기반으로 응답은 `Accept-Encoding` 요청 헤더입니다. 콘텐츠를 반환 결과 `Vary: Accept-Encoding` 헤더는 모두 압축 및 압축 되지 않은 응답이 별도로 캐시 됩니다. |

사용 하 여 응답 압축 미들웨어의 기능을 탐색할 수 있습니다는 [샘플 응용 프로그램](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples)합니다. 이 샘플에서는 다음을 보여 줍니다.
* Gzip 및 사용자 지정 압축 공급자를 사용 하 여 앱 응답의 압축입니다.
* 압축에 대 한 MIME 형식의 기본 목록에 MIME 형식을 추가 하는 방법.

## <a name="package"></a>패키지
미들웨어를 프로젝트에 포함 하려면 추가에 대 한 참조는 [ `Microsoft.AspNetCore.ResponseCompression` ](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) 패키지 하거나 사용 하 여는 [ `Microsoft.AspNetCore.All` ](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) 패키지 합니다. 이 기능은 이상 ASP.NET Core 1.1을 대상으로 하는 응용 프로그램에 사용할 수 있습니다.

## <a name="configuration"></a>구성
다음 코드를 사용 하 여 응답 압축 미들웨어를 사용 하도록 설정 하는 방법을 보여 줍니다는 기본 gzip 압축을 함께 사용 하 고 기본 MIME 형식을 정의 합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 2.x 핵심](#tab/aspnetcore2x)

[!code-csharp[Main](response-compression/samples/2.x/StartupBasic.cs?name=snippet1&highlight=4,8)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](response-compression/samples/1.x/StartupBasic.cs?name=snippet1&highlight=3,8)]

---

> [!NOTE]
> 와 같은 도구를 사용 하 여 [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), 또는 [우체부](https://www.getpostman.com/) 설정 하는 `Accept-Encoding` 요청 헤더 및 응답 헤더, 크기 및 본문을 검토 합니다.

요청 하지 않고 샘플 응용 프로그램을 제출는 `Accept-Encoding` 헤더 응답을 압축 하지 않을 것을 확인 합니다. `Content-Encoding` 및 `Vary` 헤더 응답에는 표시 되지 합니다.

![Fiddler 창 Accept-encoding 헤더 없이 요청의 결과 표시 합니다. 응답을 압축 되지 않습니다.](response-compression/_static/request-uncompressed.png)

요청을 사용한 샘플 앱을 제출는 `Accept-Encoding: gzip` 헤더 응답 압축을 관찰 합니다. `Content-Encoding` 및 `Vary` 헤더가 응답에 있는 합니다.

![Accept-encoding 헤더로 요청의 결과 및 gzip의 값을 보여 주는 fiddler 창입니다. Vary 및 Content-encoding 헤더 응답에 추가 됩니다. 응답 압축 됩니다.](response-compression/_static/request-compressed.png)

## <a name="providers"></a>공급자
### <a name="gzipcompressionprovider"></a>GzipCompressionProvider
사용 하 여 `GzipCompressionProvider` gzip으로 응답을 압축 하 합니다. 지정 하지 않으면 기본 압축 공급자입니다. 압축을 수준으로 설정할 수 있습니다는 `GzipCompressionProviderOptions`합니다. 

Gzip 압축 공급자 기본적으로 가장 빠른 압축 수준 (`CompressionLevel.Fastest`), 가장 효율적인 압축을 생성 하지 수입니다. 가장 효율적인 압축을 사용할 경우 최적의 압축에 대 한 미들웨어를 구성할 수 있습니다.

| 압축 수준                | 설명                                                                                                   |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `CompressionLevel.Fastest`       | 결과 출력은 최적으로 압축 되지 않은 경우에 압축을 가능한 한 빨리 완료 해야 합니다. |
| `CompressionLevel.NoCompression` | 압축 하지 않고 수행 되어야 합니다.                                                                           |
| `CompressionLevel.Optimal`       | 응답 최적으로 압축 된 압축을 완료 하는 데 시간이 더에 사용 하는 경우에 합니다.                |


# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 2.x 핵심](#tab/aspnetcore2x)

[!code-csharp[Main](response-compression/samples/2.x/Program.cs?name=snippet1&highlight=3,8-11)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5,10-13)]

---

## <a name="mime-types"></a>MIME 형식
미들웨어 압축에 대 한 MIME 형식의 기본 집합을 지정합니다.
* `text/plain`
* `text/css`
* `application/javascript`
* `text/html`
* `application/xml`
* `text/xml`
* `application/json`
* `text/json`

대체 하거나 응답 압축 미들웨어 옵션으로 MIME 형식을 추가할 수 있습니다. 해당 와일드 카드 MIME 참고와 같은 형식은 `text/*` 지원 되지 않습니다. 샘플 응용 프로그램에 대 한 MIME 형식을 추가 `image/svg+xml` 압축 하 고 ASP.NET Core 배너 이미지를 사용 하 고 (*banner.svg*).

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 2.x 핵심](#tab/aspnetcore2x)

[!code-csharp[Main](response-compression/samples/2.x/Program.cs?name=snippet1&highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=7)]

---

### <a name="custom-providers"></a>사용자 지정 공급자
사용자 지정 압축 구현으로 만들 수 있습니다 `ICompressionProvider`합니다. `EncodingName` 인코딩이 콘텐츠를 나타내는 `ICompressionProvider` 을 생성 합니다. 에 지정 된 목록을 기반으로 하는 공급자를 선택 하 여이 정보를 사용 하는 미들웨어는 `Accept-Encoding` 요청 헤더입니다.

클라이언트에서 요청을 제출 샘플 응용 프로그램을 사용 하 여 `Accept-Encoding: mycustomcompression` 헤더입니다. 미들웨어 사용자 지정 압축 구현을 사용 하 고 있는 응답을 반환는 `Content-Encoding: mycustomcompression` 헤더입니다. 클라이언트에서 실행 되도록 사용자 지정 압축 구현에 대 한 순서에서 사용자 지정 인코딩 압축을 풀 수 있어야 합니다.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET 2.x 핵심](#tab/aspnetcore2x)

[!code-csharp[Main](response-compression/samples/2.x/Program.cs?name=snippet1&highlight=4)]

[!code-csharp[Main](response-compression/samples/2.x/CustomCompressionProvider.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=6)]

[!code-csharp[Main](response-compression/samples/1.x/CustomCompressionProvider.cs?name=snippet1)]

---

요청을 사용한 샘플 앱을 제출는 `Accept-Encoding: mycustomcompression` 헤더 응답 헤더를 관찰 합니다. `Vary` 및 `Content-Encoding` 헤더가 응답에 있는 합니다. (표시 되지 않음) 하는 응답 본문은 샘플에서 압축 되지 않습니다. 압축 구현 하지 않으므로 `CustomCompressionProvider` 샘플의 클래스. 그러나 보여 압축 알고리즘을 구현 합니다.

![Accept-encoding 헤더로 요청의 결과 및 mycustomcompression의 값을 보여 주는 fiddler 창입니다. Vary 및 Content-encoding 헤더 응답에 추가 됩니다.](response-compression/_static/request-custom-compression.png)

## <a name="compression-with-secure-protocol"></a>보안 프로토콜에 대 한 압축
보안 연결을 통해 압축 된 응답으로 제어할 수 있습니다는 `EnableForHttps` 옵션을 기본적으로 비활성화 되어 있습니다. 와 같은 보안 문제를 일으킬 수 동적으로 생성 된 페이지와 압축을 사용 하 여 [범죄](https://en.wikipedia.org/wiki/CRIME_(security_exploit)) 및 [위반](https://en.wikipedia.org/wiki/BREACH_(security_exploit)) 공격입니다.

## <a name="adding-the-vary-header"></a>Vary 헤더를 추가합니다.
경우에 따라 응답을 압축 된 `Accept-Encoding` 헤더로, 잠재적으로 여러 개의 압축 된 버전의 응답 및 압축 되지 않은 버전입니다. 여러 버전 존재 하 고 저장 해야 캐시 클라이언트와 프록시 캐시 하도록 지시 하려면는 `Vary` 헤더와 함께 추가 됩니다 한 `Accept-Encoding` 값입니다. ASP.NET Core에서 1.x 추가 `Vary` 헤더를 응답은 수동으로 수행 됩니다. ASP.NET Core에서 2.x 미들웨어 추가 `Vary` 헤더 응답 압축 되어 있을 때 자동으로 합니다.

**ASP.NET Core 1.x만**

[!code-csharp[Main](response-compression/samples/1.x/Startup.cs?name=snippet1)]

## <a name="middlware-issue-when-behind-an-nginx-reverse-proxy"></a>Nginx 역방향 프록시 뒤에 있을 때는 Middlware 문제
요청에서 Nginx, 프록시 되 고 `Accept-Encoding` 헤더를 제거 합니다. 이렇게 하면 미들웨어를에서 응답을 압축지 않습니다. 자세한 내용은 참조 [NGINX: 압축 및 압축 풀기](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)합니다. 이 문제를 추적 하 여 [nginx (BasicMiddleware #123)에 대 한 통과 압축 파악](https://github.com/aspnet/BasicMiddleware/issues/123)합니다.

## <a name="working-with-iis-dynamic-compression"></a>IIS 동적 압축이 사용
활성 IIS 동적 압축이 모듈을 응용 프로그램에 대해 사용 하지 않도록 설정 하려는 서버 수준에서 구성 하는 경우 그렇게 할 수 있습니다에 대 한 추가 된 프로그램 *web.config* 파일입니다. 자세한 내용은 참조 [비활성화 IIS 모듈](xref:hosting/iis-modules#disabling-iis-modules)합니다.

## <a name="troubleshooting"></a>문제 해결
와 같은 도구를 사용 하 여 [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), 또는 [우체부](https://www.getpostman.com/)를 설정할 수 있도록는 `Accept-Encoding` 요청 헤더 및 응답 헤더, 크기 및 본문을 학습 합니다. 응답 압축 미들웨어 다음 조건을 충족 하는 응답을 압축 합니다.
* `Accept-Encoding` 헤더가 값이 있으면 `gzip`, `*`를 설정 하는 사용자 지정 압축 공급자와 일치 하는 사용자 지정 인코딩 또는 합니다. 값 되지 않아야 `identity` 품질 값이 있는 (qvalue, `q`) 0 (영)을 설정 합니다.
* MIME 형식 (`Content-Type`) 설정 해야 하며에 구성 된 MIME 형식과 일치 해야 합니다는 `ResponseCompressionOptions`합니다.
* 요청을 포함 하지 않아야는 `Content-Range` 헤더입니다.
* 요청 응답 압축 미들웨어 옵션에서 보안 프로토콜 (https)를 구성 하지 않으면 안전 하지 않은 프로토콜 (http)를 사용 해야 합니다. *위험을 유의 [위에서 설명한](#compression-with-secure-protocol) 보안 콘텐츠 압축을 사용 하도록 설정할 때.*

## <a name="additional-resources"></a>추가 리소스
* [응용 프로그램 시작](xref:fundamentals/startup)
* [미들웨어](xref:fundamentals/middleware)
* [Mozilla Developer Network: 허용 인코딩](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 섹션 3.1.2.1: 콘텐츠 구분을 사용](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 섹션 4.2.3: Gzip 코딩](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [GZIP 파일 형식 사양 버전 4.3](http://www.ietf.org/rfc/rfc1952.txt)
