---
title: "Facebook, Google 및 기타 외부 공급자를 통해 인증 사용"
author: rick-anderson
description: 
keywords: "ASP.NET Core, 인증, 소셜, 인증 공급자, google, facebook, twitter, microsoft 계정"
ms.author: riande
manager: wpickett
ms.date: 11/01/2016
ms.topic: article
ms.assetid: eda7ee17-f38c-462e-8d1d-63f459901cf3
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/social/index
ms.openlocfilehash: 7b72421f703db919daf640c54b725fc1497d2481
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="enabling-authentication-using-facebook-google-and-other-external-providers"></a>Facebook, Google 및 기타 외부 공급자를 통해 인증 사용

<a name="security-authentication-social-logins"></a>

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에는 사용자가 외부 인증 공급자의 자격 증명으로 OAuth 2.0을 사용하여 로그인할 수 있도록 ASP.NET Core 2.x 앱을 빌드하는 방법을 보여줍니다.

[Facebook](facebook-logins.md), [Twitter](twitter-logins.md), [Google](google-logins.md) 및 [Microsoft](microsoft-logins.md) 공급자는 다음 섹션에서 다룹니다. 다른 공급자는 [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) 및 [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers)와 같은 타사 패키지에서 사용할 수 있습니다.

![Facebook, Twitter, Google Plus 및 Windows용 소셜 미디어 아이콘](index/_static/social.png)

사용자가 자신의 기존 자격 증명을 사용하여 로그인할 수 있게 되면 사용자에게 편리하고 로그인 프로세스를 관리하는 복잡성을 타사에 양도합니다. 소셜 로그인이 트래픽 및 고객 변환을 제공할 수 있는 방법에 대한 예제는 [Facebook](https://www.facebook.com/unsupportedbrowser) 및 [Twitter](https://dev.twitter.com/resources/case-studies)의 사례 연구를 참조하세요.

참고: 여기에 제공된 패키지는 OAuth 인증 흐름의 복잡성을 상당히 추상화하지만 문제를 해결하는 경우 자세히 이해해야 할 수도 있습니다. 많은 리소스를 사용할 수 있습니다. 예를 들어 [OAuth 2 소개](https://www.digitalocean.com/community/tutorials/an-introduction-to-oauth-2) 또는 [OAuth 2 이해](http://www.bubblecode.net/2016/01/22/understanding-oauth2/)를 참조하세요. [공급자 패키지의 ASP.NET Core 소스 코드](https://github.com/aspnet/Security/tree/dev/src)를 확인하여 몇 가지 문제를 해결할 수 있습니다.

## <a name="create-a-new-aspnet-core-project"></a>새 ASP.NET Core 프로젝트 만들기

* Visual Studio 2017의 시작 페이지에서 또는 **파일 > 새로 만들기 > 프로젝트**를 통해 새 프로젝트를 만듭니다.

* **Visual C# >.NET Core** 범주에서 사용할 수 있는 **ASP.NET Core 웹 응용 프로그램** 템플릿을 선택합니다.

![새 프로젝트 대화 상자](index/_static/new-project.png)

* **웹 응용 프로그램**을 누르고 **인증**이 **개별 사용자 계정**으로 설정되었는지 확인합니다.

![새 웹 응용 프로그램 대화 상자](index/_static/select-project.png)

참고: 이 자습서에서는 마법사의 위쪽에서 선택할 수 있는 ASP.NET Core 2.0 SDK 버전에 적용됩니다.

## <a name="require-ssl"></a>SSL 필요

OAuth 2.0은 HTTPS 프로토콜을 통한 인증을 위해 SSL을 사용해야 합니다.

참고: 위에 표시된 대로 프로젝트 마법사의 **인증 변경 대화**에서 **개별 사용자 계정** 옵션을 선택한 경우 ASP.NET Core 2.x에 대한 **웹 응용 프로그램** 또는 **Web API** 프로젝트 템플릿를 사용하여 만든 프로젝트는 자동으로 SSL을 사용하고 https URL으로 시작하도록 구성됩니다.

* [ASP.NET Core 앱에서 SSL 적용](xref:security/enforcing-ssl) 항목의 단계를 수행하여 사이트에서 SSL을 가져옵니다.

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>SecretManager를 사용하여 로그인 공급자에 의해 할당된 토큰 저장

소셜 로그인 공급자는 등록 프로세스 중에 **응용 프로그램 ID** 및 **응용 프로그램 암호** 토큰을 할당합니다(공급자에 따라 정확한 이름이 다름).

이러한 값은 해당 API에 액세스하기 위해 응용 프로그램이 사용하는 *사용자 이름* 및 *암호*를 효과적으로 지정하고 직접 구성 파일에 저장하거나 하드 코딩하는 대신 **암호 관리자**를 사용하여 응용 프로그램 구성에 연결될 수 있는 "비밀"을 구성합니다.

[ASP.NET Core에서 개발 중에 앱 암호의 안전한 저장소](xref:security/app-secrets) 항목의 단계에 따라 아래의 각 로그인 공급자에 의해 할당된 토큰을 저장할 수 있습니다.

## <a name="setup-login-providers-required-by-your-application"></a>응용 프로그램에 필요한 로그인 공급자 설정

다음 항목을 사용하여 해당 공급자를 사용하도록 응용 프로그램을 구성합니다.

* [Facebook](facebook-logins.md) 지침
* [Twitter](twitter-logins.md) 지침
* [Google](google-logins.md) 지침
* [Microsoft](microsoft-logins.md) 지침
* [다른 공급자](other-logins.md) 지침

## <a name="optionally-set-password"></a>필요에 따라 암호 설정

외부 로그인 공급자를 등록하는 경우 앱에 암호를 등록하지 않은 상태입니다. 그러면 사이트에 암호를 만들고 기억하지 않아도 되지만 외부 로그인 공급자에 따라 다르게 적용해야 합니다. 외부 로그인 공급자를 사용할 수 없는 경우 웹 사이트에 로그인할 수 없습니다.

외부 공급자로 로그인하는 프로세스 중에 설정한 전자 메일을 사용하여 암호를 만들고 로그인하려면:

* 오른쪽 위 모서리에 있는 **Hello<email alias>** 링크를 눌러 **관리** 뷰로 이동합니다.

![웹 응용 프로그램 관리 뷰](index/_static/pass1a.png)

* **만들기** 탭

![암호 페이지 설정](index/_static/pass2a.png)

* 올바른 암호를 설정하고 사용자의 전자 메일을 사용하여 로그인하는 데 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 이 문서는 외부 인증을 고새하고 ASP.NET Core 앱에 외부 로그인을 추가하는 데 필요한 필수 구성 요소를 설명합니다.

* 앱에 필요한 공급자에 대한 로그인을 구성하기 위해 공급자 관련 페이지를 참조합니다.
