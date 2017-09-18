---
title: "보안"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a8fb7eb7-e0e5-4394-84f3-1f1dbe012345
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/index
ms.openlocfilehash: e8045b8804d1e7915cd81d697d43a173e33a7119
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2017
---
# <a name="security"></a>보안

*   [인증](authentication/index.md)
    *   [ID 소개](authentication/identity.md)
    *   [Facebook, Google 및 기타 외부 공급자를 통해 인증 사용](authentication/social/index.md)
    * [Windows 인증 구성](authentication/windowsauth.md)
    *   [계정 확인 및 암호 복구](authentication/accconfirm.md)
    *   [SMS를 사용한 2단계 인증](authentication/2fa.md) 
    *   [ASP.NET Core ID 없이 쿠키 인증 사용](authentication/cookie.md)
    *   [Azure Active Directory](authentication/azure-active-directory/index.md)
        *   [ASP.NET Core 웹앱에 Azure AD 통합](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-openidconnect-aspnetcore/)
        *   [Azure AD를 사용하여 WPF 응용 프로그램에서 ASP.NET Core Web API 호출](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-native-aspnetcore/)
        *   [Azure AD를 사용하여 ASP.NET Core 웹 응용 프로그램에서 Web API 호출](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore/)
        *   [Azure AD B2C를 사용하여 ASP.NET Core 웹앱](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapp/)
    *   [IdentityServer4를 사용하여 ASP.NET Core 앱 보호](https://identityserver4.readthedocs.io)
*   [권한 부여](authorization/index.md)
    *   [소개](authorization/introduction.md)
    *   [권한 부여로 보호된 사용자 데이터를 사용하여 앱 만들기](xref:security/authorization/secure-data)
    *   [단순 권한 부여](authorization/simple.md)
    *   [역할 기반 권한 부여](authorization/roles.md)
    *   [클레임 기반 권한 부여](authorization/claims.md)
    *   [사용자 지정 정책 기반 권한 부여](authorization/policies.md)
    *   [요구 사항 처리기의 종속성 주입](authorization/dependencyinjection.md)
    *   [리소스 기반 권한 부여](authorization/resourcebased.md)
    *   [뷰 기반 권한 부여](authorization/views.md)
    *   [구성표로 ID 제한](authorization/limitingidentitybyscheme.md)
*   [데이터 보호](data-protection/index.md)
    *   [데이터 보호 소개](data-protection/introduction.md)
    *   [데이터 보호 API 시작](data-protection/using-data-protection.md)
    *   [소비자 API](data-protection/consumer-apis/index.md)
        *   [소비자 API 개요](data-protection/consumer-apis/overview.md)
        *   [용도 문자열](data-protection/consumer-apis/purpose-strings.md)
        *   [용도 계층 구조 및 다중 테넌트](data-protection/consumer-apis/purpose-strings-multitenancy.md)
        *   [암호 해시](data-protection/consumer-apis/password-hashing.md)
        *   [보호된 페이로드의 수명 제한](data-protection/consumer-apis/limited-lifetime-payloads.md)
        *   [호출된 키가 속한 페이로드 보호 해제](data-protection/consumer-apis/dangerous-unprotect.md)
    *   [구성](data-protection/configuration/index.md)
        *   [데이터 보호 구성](data-protection/configuration/overview.md)
        *   [기본 설정](data-protection/configuration/default-settings.md)
        *   [컴퓨터 수준 정책](data-protection/configuration/machine-wide-policy.md)
        *   [비 DI 인식 시나리오](data-protection/configuration/non-di-scenarios.md)
    *   [확장성 API](data-protection/extensibility/index.md)
        *   [Core 암호화 확장성](data-protection/extensibility/core-crypto.md)
        *   [키 관리 확장성](data-protection/extensibility/key-management.md)
        *   [기타 API](data-protection/extensibility/misc-apis.md)
    *   [구현](data-protection/implementation/index.md)
        *   [인증된 암호화 세부 정보.](data-protection/implementation/authenticated-encryption-details.md)
        *   [하위 키 파생 및 인증된 암호화](data-protection/implementation/subkeyderivation.md)
        *   [컨텍스트 헤더](data-protection/implementation/context-headers.md)
        *   [키 관리](data-protection/implementation/key-management.md)
        *   [키 저장소 공급자](data-protection/implementation/key-storage-providers.md)
        *   [미사용 키 암호화](data-protection/implementation/key-encryption-at-rest.md)
        *   [키 불변성 및 설정 변경](data-protection/implementation/key-immutability.md)
        *   [키 저장소 형식](data-protection/implementation/key-storage-format.md)
        *   [삭제되는 데이터 보호 공급자](data-protection/implementation/key-storage-ephemeral.md)
    *   [호환성](data-protection/compatibility/index.md)
        *   [응용프로그램 쿠키 공유](data-protection/compatibility/cookie-sharing.md)
        *   [ASP.NET에서 <machineKey> 바꾸기](data-protection/compatibility/replacing-machinekey.md)
*   [권한 부여로 보호된 사용자 데이터를 사용하여 앱 만들기](xref:security/authorization/secure-data)
*   [개발 중 안전한 앱 비밀 저장소](app-secrets.md)
*   [Azure Key Vault 구성 공급자](key-vault-configuration.md)
*   [SSL 적용](enforcing-ssl.md)
*   [개발용 HTTPS 설정](https.md)
*   [요청 위조 방지](anti-request-forgery.md)
*   [오픈 리디렉션(Open Redirect) 공격 방지](preventing-open-redirects.md)
*   [교차 사이트 스크립팅 방지](cross-site-scripting.md)
*   [원본 간 요청(CORS) 사용](cors.md)
