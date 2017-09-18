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
# <a name="security"></a><span data-ttu-id="04aad-103">보안</span><span class="sxs-lookup"><span data-stu-id="04aad-103">Security</span></span>

*   [<span data-ttu-id="04aad-104">인증</span><span class="sxs-lookup"><span data-stu-id="04aad-104">Authentication</span></span>](authentication/index.md)
    *   [<span data-ttu-id="04aad-105">ID 소개</span><span class="sxs-lookup"><span data-stu-id="04aad-105">Introduction to Identity</span></span>](authentication/identity.md)
    *   [<span data-ttu-id="04aad-106">Facebook, Google 및 기타 외부 공급자를 통해 인증 사용</span><span class="sxs-lookup"><span data-stu-id="04aad-106">Enabling authentication using Facebook, Google and other external providers</span></span>](authentication/social/index.md)
    * [<span data-ttu-id="04aad-107">Windows 인증 구성</span><span class="sxs-lookup"><span data-stu-id="04aad-107">Configure Windows Authentication</span></span>](authentication/windowsauth.md)
    *   [<span data-ttu-id="04aad-108">계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="04aad-108">Account Confirmation and Password Recovery</span></span>](authentication/accconfirm.md)
    *   [<span data-ttu-id="04aad-109">SMS를 사용한 2단계 인증</span><span class="sxs-lookup"><span data-stu-id="04aad-109">Two-factor authentication with SMS</span></span>](authentication/2fa.md) 
    *   [<span data-ttu-id="04aad-110">ASP.NET Core ID 없이 쿠키 인증 사용</span><span class="sxs-lookup"><span data-stu-id="04aad-110">Using Cookie Authentication without ASP.NET Core Identity</span></span>](authentication/cookie.md)
    *   [<span data-ttu-id="04aad-111">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="04aad-111">Azure Active Directory</span></span>](authentication/azure-active-directory/index.md)
        *   [<span data-ttu-id="04aad-112">ASP.NET Core 웹앱에 Azure AD 통합</span><span class="sxs-lookup"><span data-stu-id="04aad-112">Integrating Azure AD Into an ASP.NET Core Web App</span></span>](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-openidconnect-aspnetcore/)
        *   [<span data-ttu-id="04aad-113">Azure AD를 사용하여 WPF 응용 프로그램에서 ASP.NET Core Web API 호출</span><span class="sxs-lookup"><span data-stu-id="04aad-113">Calling a ASP.NET Core Web API From a WPF Application Using Azure AD</span></span>](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-native-aspnetcore/)
        *   [<span data-ttu-id="04aad-114">Azure AD를 사용하여 ASP.NET Core 웹 응용 프로그램에서 Web API 호출</span><span class="sxs-lookup"><span data-stu-id="04aad-114">Calling a Web API in an ASP.NET Core Web Application Using Azure AD</span></span>](https://azure.microsoft.com/documentation/samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore/)
        *   [<span data-ttu-id="04aad-115">Azure AD B2C를 사용하여 ASP.NET Core 웹앱</span><span class="sxs-lookup"><span data-stu-id="04aad-115">An ASP.NET Core web app with Azure AD B2C</span></span>](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapp/)
    *   [<span data-ttu-id="04aad-116">IdentityServer4를 사용하여 ASP.NET Core 앱 보호</span><span class="sxs-lookup"><span data-stu-id="04aad-116">Securing ASP.NET Core apps with IdentityServer4</span></span>](https://identityserver4.readthedocs.io)
*   [<span data-ttu-id="04aad-117">권한 부여</span><span class="sxs-lookup"><span data-stu-id="04aad-117">Authorization</span></span>](authorization/index.md)
    *   [<span data-ttu-id="04aad-118">소개</span><span class="sxs-lookup"><span data-stu-id="04aad-118">Introduction</span></span>](authorization/introduction.md)
    *   [<span data-ttu-id="04aad-119">권한 부여로 보호된 사용자 데이터를 사용하여 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="04aad-119">Create an app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
    *   [<span data-ttu-id="04aad-120">단순 권한 부여</span><span class="sxs-lookup"><span data-stu-id="04aad-120">Simple Authorization</span></span>](authorization/simple.md)
    *   [<span data-ttu-id="04aad-121">역할 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="04aad-121">Role based Authorization</span></span>](authorization/roles.md)
    *   [<span data-ttu-id="04aad-122">클레임 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="04aad-122">Claims-Based Authorization</span></span>](authorization/claims.md)
    *   [<span data-ttu-id="04aad-123">사용자 지정 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="04aad-123">Custom Policy-Based Authorization</span></span>](authorization/policies.md)
    *   [<span data-ttu-id="04aad-124">요구 사항 처리기의 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="04aad-124">Dependency Injection in requirement handlers</span></span>](authorization/dependencyinjection.md)
    *   [<span data-ttu-id="04aad-125">리소스 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="04aad-125">Resource Based Authorization</span></span>](authorization/resourcebased.md)
    *   [<span data-ttu-id="04aad-126">뷰 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="04aad-126">View Based Authorization</span></span>](authorization/views.md)
    *   [<span data-ttu-id="04aad-127">구성표로 ID 제한</span><span class="sxs-lookup"><span data-stu-id="04aad-127">Limiting identity by scheme</span></span>](authorization/limitingidentitybyscheme.md)
*   [<span data-ttu-id="04aad-128">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="04aad-128">Data Protection</span></span>](data-protection/index.md)
    *   [<span data-ttu-id="04aad-129">데이터 보호 소개</span><span class="sxs-lookup"><span data-stu-id="04aad-129">Introduction to Data Protection</span></span>](data-protection/introduction.md)
    *   [<span data-ttu-id="04aad-130">데이터 보호 API 시작</span><span class="sxs-lookup"><span data-stu-id="04aad-130">Getting Started with the Data Protection APIs</span></span>](data-protection/using-data-protection.md)
    *   [<span data-ttu-id="04aad-131">소비자 API</span><span class="sxs-lookup"><span data-stu-id="04aad-131">Consumer APIs</span></span>](data-protection/consumer-apis/index.md)
        *   [<span data-ttu-id="04aad-132">소비자 API 개요</span><span class="sxs-lookup"><span data-stu-id="04aad-132">Consumer APIs Overview</span></span>](data-protection/consumer-apis/overview.md)
        *   [<span data-ttu-id="04aad-133">용도 문자열</span><span class="sxs-lookup"><span data-stu-id="04aad-133">Purpose Strings</span></span>](data-protection/consumer-apis/purpose-strings.md)
        *   [<span data-ttu-id="04aad-134">용도 계층 구조 및 다중 테넌트</span><span class="sxs-lookup"><span data-stu-id="04aad-134">Purpose hierarchy and multi-tenancy</span></span>](data-protection/consumer-apis/purpose-strings-multitenancy.md)
        *   [<span data-ttu-id="04aad-135">암호 해시</span><span class="sxs-lookup"><span data-stu-id="04aad-135">Password Hashing</span></span>](data-protection/consumer-apis/password-hashing.md)
        *   [<span data-ttu-id="04aad-136">보호된 페이로드의 수명 제한</span><span class="sxs-lookup"><span data-stu-id="04aad-136">Limiting the lifetime of protected payloads</span></span>](data-protection/consumer-apis/limited-lifetime-payloads.md)
        *   [<span data-ttu-id="04aad-137">호출된 키가 속한 페이로드 보호 해제</span><span class="sxs-lookup"><span data-stu-id="04aad-137">Unprotecting payloads whose keys have been revoked</span></span>](data-protection/consumer-apis/dangerous-unprotect.md)
    *   [<span data-ttu-id="04aad-138">구성</span><span class="sxs-lookup"><span data-stu-id="04aad-138">Configuration</span></span>](data-protection/configuration/index.md)
        *   [<span data-ttu-id="04aad-139">데이터 보호 구성</span><span class="sxs-lookup"><span data-stu-id="04aad-139">Configuring Data Protection</span></span>](data-protection/configuration/overview.md)
        *   [<span data-ttu-id="04aad-140">기본 설정</span><span class="sxs-lookup"><span data-stu-id="04aad-140">Default Settings</span></span>](data-protection/configuration/default-settings.md)
        *   [<span data-ttu-id="04aad-141">컴퓨터 수준 정책</span><span class="sxs-lookup"><span data-stu-id="04aad-141">Machine Wide Policy</span></span>](data-protection/configuration/machine-wide-policy.md)
        *   [<span data-ttu-id="04aad-142">비 DI 인식 시나리오</span><span class="sxs-lookup"><span data-stu-id="04aad-142">Non DI Aware Scenarios</span></span>](data-protection/configuration/non-di-scenarios.md)
    *   [<span data-ttu-id="04aad-143">확장성 API</span><span class="sxs-lookup"><span data-stu-id="04aad-143">Extensibility APIs</span></span>](data-protection/extensibility/index.md)
        *   [<span data-ttu-id="04aad-144">Core 암호화 확장성</span><span class="sxs-lookup"><span data-stu-id="04aad-144">Core cryptography extensibility</span></span>](data-protection/extensibility/core-crypto.md)
        *   [<span data-ttu-id="04aad-145">키 관리 확장성</span><span class="sxs-lookup"><span data-stu-id="04aad-145">Key management extensibility</span></span>](data-protection/extensibility/key-management.md)
        *   [<span data-ttu-id="04aad-146">기타 API</span><span class="sxs-lookup"><span data-stu-id="04aad-146">Miscellaneous APIs</span></span>](data-protection/extensibility/misc-apis.md)
    *   [<span data-ttu-id="04aad-147">구현</span><span class="sxs-lookup"><span data-stu-id="04aad-147">Implementation</span></span>](data-protection/implementation/index.md)
        *   [<span data-ttu-id="04aad-148">인증된 암호화 세부 정보.</span><span class="sxs-lookup"><span data-stu-id="04aad-148">Authenticated encryption details.</span></span>](data-protection/implementation/authenticated-encryption-details.md)
        *   [<span data-ttu-id="04aad-149">하위 키 파생 및 인증된 암호화</span><span class="sxs-lookup"><span data-stu-id="04aad-149">Subkey Derivation and Authenticated Encryption</span></span>](data-protection/implementation/subkeyderivation.md)
        *   [<span data-ttu-id="04aad-150">컨텍스트 헤더</span><span class="sxs-lookup"><span data-stu-id="04aad-150">Context headers</span></span>](data-protection/implementation/context-headers.md)
        *   [<span data-ttu-id="04aad-151">키 관리</span><span class="sxs-lookup"><span data-stu-id="04aad-151">Key Management</span></span>](data-protection/implementation/key-management.md)
        *   [<span data-ttu-id="04aad-152">키 저장소 공급자</span><span class="sxs-lookup"><span data-stu-id="04aad-152">Key Storage Providers</span></span>](data-protection/implementation/key-storage-providers.md)
        *   [<span data-ttu-id="04aad-153">미사용 키 암호화</span><span class="sxs-lookup"><span data-stu-id="04aad-153">Key Encryption At Rest</span></span>](data-protection/implementation/key-encryption-at-rest.md)
        *   [<span data-ttu-id="04aad-154">키 불변성 및 설정 변경</span><span class="sxs-lookup"><span data-stu-id="04aad-154">Key Immutability and Changing Settings</span></span>](data-protection/implementation/key-immutability.md)
        *   [<span data-ttu-id="04aad-155">키 저장소 형식</span><span class="sxs-lookup"><span data-stu-id="04aad-155">Key Storage Format</span></span>](data-protection/implementation/key-storage-format.md)
        *   [<span data-ttu-id="04aad-156">삭제되는 데이터 보호 공급자</span><span class="sxs-lookup"><span data-stu-id="04aad-156">Ephemeral data protection providers</span></span>](data-protection/implementation/key-storage-ephemeral.md)
    *   [<span data-ttu-id="04aad-157">호환성</span><span class="sxs-lookup"><span data-stu-id="04aad-157">Compatibility</span></span>](data-protection/compatibility/index.md)
        *   [<span data-ttu-id="04aad-158">응용프로그램 쿠키 공유</span><span class="sxs-lookup"><span data-stu-id="04aad-158">Sharing cookies between applications</span></span>](data-protection/compatibility/cookie-sharing.md)
        *   [<span data-ttu-id="04aad-159">ASP.NET에서 <machineKey> 바꾸기</span><span class="sxs-lookup"><span data-stu-id="04aad-159">Replacing <machineKey> in ASP.NET</span></span>](data-protection/compatibility/replacing-machinekey.md)
*   [<span data-ttu-id="04aad-160">권한 부여로 보호된 사용자 데이터를 사용하여 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="04aad-160">Create an app with user data protected by authorization</span></span>](xref:security/authorization/secure-data)
*   [<span data-ttu-id="04aad-161">개발 중 안전한 앱 비밀 저장소</span><span class="sxs-lookup"><span data-stu-id="04aad-161">Safe storage of app secrets during development</span></span>](app-secrets.md)
*   [<span data-ttu-id="04aad-162">Azure Key Vault 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="04aad-162">Azure Key Vault configuration provider</span></span>](key-vault-configuration.md)
*   [<span data-ttu-id="04aad-163">SSL 적용</span><span class="sxs-lookup"><span data-stu-id="04aad-163">Enforcing SSL</span></span>](enforcing-ssl.md)
*   [<span data-ttu-id="04aad-164">개발용 HTTPS 설정</span><span class="sxs-lookup"><span data-stu-id="04aad-164">Setting up HTTPS for development</span></span>](https.md)
*   [<span data-ttu-id="04aad-165">요청 위조 방지</span><span class="sxs-lookup"><span data-stu-id="04aad-165">Anti-Request Forgery</span></span>](anti-request-forgery.md)
*   [<span data-ttu-id="04aad-166">오픈 리디렉션(Open Redirect) 공격 방지</span><span class="sxs-lookup"><span data-stu-id="04aad-166">Preventing Open Redirect Attacks</span></span>](preventing-open-redirects.md)
*   [<span data-ttu-id="04aad-167">교차 사이트 스크립팅 방지</span><span class="sxs-lookup"><span data-stu-id="04aad-167">Preventing Cross-Site Scripting</span></span>](cross-site-scripting.md)
*   [<span data-ttu-id="04aad-168">원본 간 요청(CORS) 사용</span><span class="sxs-lookup"><span data-stu-id="04aad-168">Enabling Cross-Origin Requests (CORS)</span></span>](cors.md)
