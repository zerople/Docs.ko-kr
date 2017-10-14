---
title: "사이트 간 스크립팅 방지"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 95790927-2bfe-445e-b1fd-429c2c7030ce
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/cross-site-scripting
ms.openlocfilehash: d0880fda4ee726bd30a48cce0907a3887f2a4545
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="preventing-cross-site-scripting"></a><span data-ttu-id="d0362-103">사이트 간 스크립팅 방지</span><span class="sxs-lookup"><span data-stu-id="d0362-103">Preventing Cross-Site Scripting</span></span>

<a name="security-cross-site-scripting"></a>

<span data-ttu-id="d0362-104">사이트 간 스크립팅 (XSS)은 공격자가 클라이언트 쪽 스크립트 (일반적으로 JavaScript) 웹 페이지에 배치할 수 있는 보안 취약점입니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-104">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="d0362-105">공격자가 스크립트를 실행할 영향을 받는 페이지를 로드 하는 다른 사용자가 DOM 조작을 통해 웹 페이지의 내용을 변경 하거나 다른 페이지를 브라우저를 리디렉션하여 쿠키 및 세션 토큰을 도용 하는 공격자가 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-105">When other users load affected pages the attackers scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="d0362-106">XSS 취약점은 일반적으로 응용 프로그램 사용자 입력을 사용 하 고 유효성 검사, 인코딩 또는 이스케이프 것 없이 페이지에서 출력 하는 경우에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-106">XSS vulnerabilities generally occur when an application takes user input and outputs it in a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="d0362-107">XSS에 대 한 응용 프로그램 보호</span><span class="sxs-lookup"><span data-stu-id="d0362-107">Protecting your application against XSS</span></span>

<span data-ttu-id="d0362-108">에 기본 수준 XSS는 방식으로 작동 하면 응용 프로그램 삽입을 속이는 행위는 `<script>` 렌더링 된 페이지에 또는 삽입 하 여 태그는 `On*` 요소에는 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-108">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="d0362-109">개발자의 응용 프로그램에 XSS 시 키 지 않도록 하려면 다음과 같은 예방 단계를 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-109">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="d0362-110">나머지 아래 단계를 수행 하지 않으면 HTML 입력을으로 신뢰할 수 없는 데이터를 저장 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-110">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="d0362-111">신뢰할 수 없는 데이터는 공격자, 입력을 구성 하는 HTML, 쿼리 문자열, HTTP 헤더는 공격자가 응용 프로그램를 위반할 수 없는 경우에 데이터베이스를 위반할 수 있습니다 때 데이터베이스에서 제공 하는 데이터도 사용할 수 있는 모든 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-111">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="d0362-112">HTML 요소 내부의 신뢰할 수 없는 데이터를 넣기 전에 HTML로 인코딩하지는 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-112">Before putting untrusted data inside an HTML element ensure it is HTML encoded.</span></span> <span data-ttu-id="d0362-113">와 같은 문자는 HTML 인코딩 &lt; 와 같은 안전한 형식으로 변경 하 고 &amp;lt;</span><span class="sxs-lookup"><span data-stu-id="d0362-113">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="d0362-114">HTML 특성을 신뢰할 수 없는 데이터를 전환 하기 전에 인코딩된 HTML 특성을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-114">Before putting untrusted data into an HTML attribute ensure it is HTML attribute encoded.</span></span> <span data-ttu-id="d0362-115">HTML 인코딩을의 상위 집합 및와 같은 추가 문자를 인코딩하고 HTML 특성 인코딩입니다 "및 '.</span><span class="sxs-lookup"><span data-stu-id="d0362-115">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="d0362-116">JavaScript를 신뢰할 수 없는 데이터를 전환 하기 전에 데이터를 런타임에 검색할 내용이 HTML 요소에 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-116">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="d0362-117">이 불가능 한 다음 데이터를 확인 하는 경우 JavaScript이 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-117">If this is not possible then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="d0362-118">JavaScript에 대 한 위험한 문자는 JavaScript 인코딩 및 예를 들어 해당 16 진수도 바꿔 &lt; 로 인코딩해야 `\u003C`합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-118">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="d0362-119">URL 쿼리 문자열을 신뢰할 수 없는 데이터를 전환 하기 전에 인코딩된 URL을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-119">Before putting untrusted data into a URL query string ensure it is URL encoded.</span></span>

## <a name="html-encoding-using-razor"></a><span data-ttu-id="d0362-120">Razor를 사용 하 여 HTML 인코딩</span><span class="sxs-lookup"><span data-stu-id="d0362-120">HTML Encoding using Razor</span></span>

<span data-ttu-id="d0362-121">Razor 엔진 MVC에서 자동으로 사용 되는 모든 인코딩합니다 이렇게 하기만 해도 실제로 하드 작업 하지 않는 출력 변수에서 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-121">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="d0362-122">HTML 특성을 사용할 때마다 인코딩 규칙을 사용 하 여는  *@*  지시문입니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-122">It uses HTML Attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="d0362-123">HTML로 인코딩 특성은 HTML 인코딩 즉, 걱정 HTML 인코딩 또는 HTML 특성 인코딩입니다를 사용 해야 할지 없는 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-123">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="d0362-124">만 사용 하는 HTML 컨텍스트에서 하지 JavaScript에 직접 신뢰할 수 없는 입력을 삽입 하려고 할 때 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-124">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="d0362-125">태그 도우미는 또한 입력을 매개 변수 태그에서에서 사용 하 여를 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-125">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="d0362-126">다음 Razor 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-126">Take the following Razor view;</span></span>

```none
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="d0362-127">이 보기의 내용을 출력는 *untrustedInput* 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-127">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="d0362-128">즉 XSS 공격에 사용 되는 일부 문자를 포함 하는이 변수 &lt;, "및 &gt;합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-128">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="d0362-129">소스 검사로 인코딩된 렌더링된 된 출력을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-129">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="d0362-130">ASP.NET Core MVC 제공는 `HtmlString` 클래스는 자동으로 출력 시 인코딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-130">ASP.NET Core MVC provides an `HtmlString` class which is not automatically encoded upon output.</span></span> <span data-ttu-id="d0362-131">이것은 사용 되지 함께 신뢰할 수 없는 입력이 XSS 취약성에 노출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-131">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-razor"></a><span data-ttu-id="d0362-132">Razor를 사용 하 여 Javascript 인코딩</span><span class="sxs-lookup"><span data-stu-id="d0362-132">Javascript Encoding using Razor</span></span>

<span data-ttu-id="d0362-133">보기에서 처리 하는 JavaScript에 값을 삽입 하려는 경우가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-133">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="d0362-134">이렇게 하는 데는 두 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-134">There are two ways to do this.</span></span> <span data-ttu-id="d0362-135">간단한 값을 삽입 하는 가장 안전한 방법은 하는 태그의 데이터 특성에 값을 배치 하 고 JavaScript에서 검색 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-135">The safest way to insert simple values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="d0362-136">예:</span><span class="sxs-lookup"><span data-stu-id="d0362-136">For example:</span></span>

```none
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="d0362-137">다음 HTML 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-137">This will produce the following HTML</span></span>

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

<span data-ttu-id="d0362-138">실행 될 때 렌더링 됩니다; 다음</span><span class="sxs-lookup"><span data-stu-id="d0362-138">Which, when it runs, will render the following;</span></span>

```none
<"123">
   <"123">
   ```

<span data-ttu-id="d0362-139">JavaScript 인코더를를 직접 호출 또한 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-139">You can also call the JavaScript encoder directly,</span></span>

```none
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
   ```

<span data-ttu-id="d0362-140">다음과 같이; 브라우저에서 렌더링 됩니다이</span><span class="sxs-lookup"><span data-stu-id="d0362-140">This will render in the browser as follows;</span></span>

```html
<script>
       document.write("\u003C\u0022123\u0022\u003E");
   </script>
   ```

>[!WARNING]
> <span data-ttu-id="d0362-141">DOM 요소를 만드는 JavaScript에서 신뢰할 수 없는 입력을 연결 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="d0362-141">Do not concatenate untrusted input in JavaScript to create DOM elements.</span></span> <span data-ttu-id="d0362-142">사용 해야 `createElement()` 와 같은 적절 하 게 속성 값을 할당 하 고 `node.TextContent=`, 사용 또는 `element.SetAttribute()` / `element[attribute]=` 그렇지 않은 경우에 노출 있습니다 XSS DOM 기반 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-142">You should use `createElement()` and assign property values appropriately such as `node.TextContent=`, or use `element.SetAttribute()`/`element[attribute]=` otherwise you expose yourself to DOM-based XSS.</span></span>

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="d0362-143">코드에서 인코더에 액세스</span><span class="sxs-lookup"><span data-stu-id="d0362-143">Accessing encoders in code</span></span>

<span data-ttu-id="d0362-144">HTML, JavaScript 및 URL 인코더는 두 가지 방법으로 코드에서 사용할 수 있습니다 수는 이러한 구성 요소를 통해, [종속성 주입](../fundamentals/dependency-injection.md#fundamentals-dependency-injection) 또는에 포함 된 기본 인코더를 사용할 수는 `System.Text.Encodings.Web` 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-144">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](../fundamentals/dependency-injection.md#fundamentals-dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="d0362-145">문자 범위에 적용 되는 모든 기본 인코더를 사용 하는 경우에 안전한 것으로 처리 되도록 하려면 내용이 적용 되지 않습니다-기본 인코더 가능한 가장 안전한 인코딩 규칙을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-145">If you use the default encoders then any  you applied to character ranges to be treated as safe will not take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="d0362-146">이렇게 하려면 생성자 취해야 DI 사용 하는 구성 가능한 인코더가 사용 하는 *HtmlEncoder*, *JavaScriptEncoder* 및 *UrlEncoder* 매개 변수를 적절 하 게 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-146">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="d0362-147">예를 들어</span><span class="sxs-lookup"><span data-stu-id="d0362-147">For example;</span></span>

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a><span data-ttu-id="d0362-148">URL 매개 변수 인코딩</span><span class="sxs-lookup"><span data-stu-id="d0362-148">Encoding URL Parameters</span></span>

<span data-ttu-id="d0362-149">값 사용으로 신뢰할 수 없는 입력을 사용 하는 URL 쿼리 문자열을 빌드 하려는 경우는 `UrlEncoder` 를 인코딩하는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-149">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="d0362-150">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-150">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="d0362-151">변수에 포함 됩니다는 encodedValue 인코딩 후 `%22Quoted%20Value%20with%20spaces%20and%20%26%22`합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-151">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="d0362-152">공백, 따옴표, 문장 부호 및 기타 안전 하지 않은 문자 인코드될 것인지 백분율을 16 진수 값으로, 예를 들어 공백 문자가 20% 될 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-152">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="d0362-153">URL 경로의 일부로 신뢰할 수 없는 입력을 사용 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="d0362-153">Do not use untrusted input as part of a URL path.</span></span> <span data-ttu-id="d0362-154">항상 신뢰할 수 없는 입력 쿼리 문자열 값으로 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-154">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="d0362-155">인코더를 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="d0362-155">Customizing the Encoders</span></span>

<span data-ttu-id="d0362-156">기본적으로 인코더 기본 라틴어 유니코드 범위를 제한 하는 수신 허용 목록 사용 및 해당 문자 코드 형식으로 해당 범위 외부의 모든 문자를 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-156">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="d0362-157">이 동작은 또한 인코더를 사용 하는 문자열에 출력 하려면과 Razor TagHelper HtmlHelper 렌더링을 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-157">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="d0362-158">이 이유 (이전 브라우저 버그 영어가 아닌 문자 처리에 따라 구문 분석을 중단점이 있는) 알 수 없거나 향후 브라우저 버그를 방지 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-158">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="d0362-159">웹 사이트가 중국어와 같은 비 라틴 문자를 많이 사용 하는 경우 키릴 자모 또는 다른 이것이 하지 않을 원하는 동작입니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-159">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="d0362-160">범위에서 시작 하는 동안 응용 프로그램에 적합 한 유니코드를 포함 하도록 인코더 수신 허용 목록에 사용자 지정할 수 있습니다 `ConfigureServices()`합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-160">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="d0362-161">예를 들어 Razor HtmlHelper를 사용할 수 있습니다 기본 구성을 사용 하 여 같이;</span><span class="sxs-lookup"><span data-stu-id="d0362-161">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="d0362-162">웹 페이지의 소스를 볼 때 암호화 됩니다.; 중국어 텍스트가 다음과 같이 렌더링 된가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-162">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="d0362-163">으로 처리 하는 문자 범위를 넓히려면 인코더에 의해 안전 삽입 하 여 다음 줄으로 된 `ConfigureServices()` 메서드에서 `startup.cs`;</span><span class="sxs-lookup"><span data-stu-id="d0362-163">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="d0362-164">이 예에서는 유니코드 범위 CjkUnifiedIdeographs 포함 하도록 수신 허용 목록을 넓어집니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-164">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="d0362-165">렌더링된 된 출력은 이제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-165">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="d0362-166">수신 허용 목록 범위는 유니코드 코드 차트, 하지 언어로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-166">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="d0362-167">[유니코드 표준](http://unicode.org/) 에 목록이 [차트 코드](http://www.unicode.org/charts/index.html) 문자가 포함 된 차트를 찾는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-167">The [Unicode standard](http://unicode.org/) has a list of [code charts](http://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="d0362-168">Html, JavaScript 및 Url을 각 인코더는 개별적으로 구성 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-168">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="d0362-169">수신 허용 목록에 대 한 사용자 지정 인코더를 DI 통해 소싱에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-169">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="d0362-170">통해 인코더에 직접 액세스할 `System.Text.Encodings.Web.*Encoder.Default` 다음 기본값, 기본 라틴어만 수신 허용 목록 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-170">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="d0362-171">인코딩 take 여기서 배치 해야 합니까?</span><span class="sxs-lookup"><span data-stu-id="d0362-171">Where should encoding take place?</span></span>

<span data-ttu-id="d0362-172">일반 허용 연습 출력 시점에서 일어나 인코딩 및 데이터베이스에 인코딩된 값 저장 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-172">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="d0362-173">출력 시 인코딩 쿼리 문자열 값으로 HTML에서에서 예를 들어 데이터의 사용을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-173">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="d0362-174">또한 검색 하기 전에 값을 인코딩할 필요 없이 데이터를 쉽게 검색할 수 있습니다 및의 변경 또는 인코더에 대 한 버그 수정 이용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-174">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="d0362-175">XSS 방지 기법으로 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="d0362-175">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="d0362-176">유효성 검사 제한 XSS 공격에 유용한 도구 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-176">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="d0362-177">예를 들어 0-9를 포함 하는 단순한 숫자 문자열은 XSS 공격을 트리거하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-177">For example, a simple numeric string containing only the characters 0-9 will not trigger an XSS attack.</span></span> <span data-ttu-id="d0362-178">유효성 검사 불가능 하지 않다면 어려울은 HTML 입력을 구문 분석할-사용자 입력의 HTML을 허용 하려는 경우 문제가 더 복잡해 집니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-178">Validation becomes more complicated should you wish to accept HTML in user input - parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="d0362-179">MarkDown 및 기타 텍스트 형식이 안전한 옵션이 풍부한 입력에 대 한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-179">MarkDown and other text formats would be a safer option for rich input.</span></span> <span data-ttu-id="d0362-180">유효성 검사에 단독에 의존 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0362-180">You should never rely on validation alone.</span></span> <span data-ttu-id="d0362-181">항상 수행 했는지 유효성에 관계 없이 출력 전에 신뢰할 수 없는 입력을 인코딩하십시오.</span><span class="sxs-lookup"><span data-stu-id="d0362-181">Always encode untrusted input before output, no matter what validation you have performed.</span></span>
