---
title: "보안 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: a8fb7eb7-e0e5-4394-84f3-1f1dbe012345
ms.technology: aspnet
ms.prod: asp.net-core
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 7e6dc2a5e8382f26c09375037c767265582bee70
ms.lasthandoff: 03/23/2017

---
# <a name="security"></a>보안

*   [인증](authentication/index.md)
    *   [Id 소개](authentication/identity.md)
    *   [Facebook, Google 및 다른 외부 공급자를 사용 하 여 인증을 사용 하도록 설정](authentication/social/index.md)
    *   [계정 확인 및 암호 복구](authentication/accconfirm.md)
    *   [SMS와&2; 단계 인증](authentication/2fa.md)
    *   [🔧OAuth 2.0을 사용 하 여 제 3 자 클라이언트 지원](authentication/oauth2.md)
    *   [ASP.NET Core Identity 없이 쿠키 미들웨어를 사용 하 여](authentication/cookie.md)
    *   [Azure Active Directory](authentication/azure-active-directory/index.md)
        *   [ASP.NET Core 웹 응용 프로그램에 Azure AD 통합](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-openidconnect-aspnetcore)
        *   [Azure AD를 사용 하 여 WPF 응용 프로그램에서 ASP.NET 핵심 웹 API를 호출 합니다.](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-native-aspnetcore)
        *   [Azure AD를 사용 하 여 ASP.NET 핵심 웹 응용 프로그램에서 웹 API를 호출 합니다.](https://azure.microsoft.com/en-us/documentation/samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore/)
        *   [Azure AD B2C를 사용 하 여 ASP.NET 핵심 웹 앱](https://azure.microsoft.com/en-us/documentation/samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c/)
    *   [ASP.NET Core IdentityServer4 사용 하 여 앱 보안 유지](https://identityserver4.readthedocs.io)
*   [권한 부여](authorization/index.md)
    *   [소개](authorization/introduction.md)
    *   [간단한 권한 부여](authorization/simple.md)
    *   [역할 기반 권한 부여](authorization/roles.md)
    *   [클레임 기반 권한 부여](authorization/claims.md)
    *   [사용자 지정 정책 기반 권한 부여](authorization/policies.md)
    *   [요구 사항 처리기에서 종속성 주입](authorization/dependencyinjection.md)
    *   [리소스 기반 권한 부여](authorization/resourcebased.md)
    *   [보기 기반 권한 부여](authorization/views.md)
    *   [스키마에 의해 identity를 제한합니다.](authorization/limitingidentitybyscheme.md)
*   [데이터 보호](data-protection/index.md)
    *   [데이터 보호 소개](data-protection/introduction.md)
    *   [데이터 보호 Api를 시작 하기](data-protection/using-data-protection.md)
    *   [소비자 Api](data-protection/consumer-apis/index.md)
        *   [소비자 Api 개요](data-protection/consumer-apis/overview.md)
        *   [목적은 문자열](data-protection/consumer-apis/purpose-strings.md)
        *   [목적은 계층 구조 및 다중 테 넌 트](data-protection/consumer-apis/purpose-strings-multitenancy.md)
        *   [암호 해시](data-protection/consumer-apis/password-hashing.md)
        *   [보호 된 페이로드의 수명을 제한](data-protection/consumer-apis/limited-lifetime-payloads.md)
        *   [보호 해제 페이로드 키를 갖는 해지 되었습니다.](data-protection/consumer-apis/dangerous-unprotect.md)
    *   [구성](data-protection/configuration/index.md)
        *   [데이터 보호를 구성합니다.](data-protection/configuration/overview.md)
        *   [기본 설정](data-protection/configuration/default-settings.md)
        *   [컴퓨터 단위 정책](data-protection/configuration/machine-wide-policy.md)
        *   [비 DI 인식 시나리오](data-protection/configuration/non-di-scenarios.md)
    *   [확장성 Api](data-protection/extensibility/index.md)
        *   [핵심 암호화 확장성](data-protection/extensibility/core-crypto.md)
        *   [키 관리 확장성](data-protection/extensibility/key-management.md)
        *   [기타 Api](data-protection/extensibility/misc-apis.md)
    *   [구현](data-protection/implementation/index.md)
        *   [암호화 정보를 인증합니다.](data-protection/implementation/authenticated-encryption-details.md)
        *   [하위 키 파생 및 인증 된 암호화](data-protection/implementation/subkeyderivation.md)
        *   [컨텍스트 헤더](data-protection/implementation/context-headers.md)
        *   [키 관리](data-protection/implementation/key-management.md)
        *   [키 저장소 공급자](data-protection/implementation/key-storage-providers.md)
        *   [상태일 때 키 암호화](data-protection/implementation/key-encryption-at-rest.md)
        *   [키 불변성과 설정 변경](data-protection/implementation/key-immutability.md)
        *   [키 저장소 형식](data-protection/implementation/key-storage-format.md)
        *   [임시 데이터 보호 기능 공급자](data-protection/implementation/key-storage-ephemeral.md)
    *   [호환성](data-protection/compatibility/index.md)
        *   [응용 프로그램 간에 쿠키를 공유합니다.](data-protection/compatibility/cookie-sharing.md)
        *   [대체 <machineKey> asp.net](data-protection/compatibility/replacing-machinekey.md)
*   [개발 하는 동안 앱 암호의 안전한 저장소](app-secrets.md)
*   [Azure 키 자격 증명 모음 구성 공급자](key-vault-configuration.md)
*   [🔧SSL을 적용합니다.](enforcing-ssl.md)
*   [🔧백신 요청 위조 방지](anti-request-forgery.md)
*   [🔧열린 리디렉션 공격을 방지](open-redirect.md)
*   [사이트 간 스크립팅 방지](cross-site-scripting.md)
*   [크로스-원본 요청 (CORS)를 사용 하도록 설정](cors.md)

