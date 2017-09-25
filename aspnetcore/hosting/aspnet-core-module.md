---
title: "ASP.NET Core 모듈 구성 참조"
author: guardrex
description: "ASP.NET Core 응용 프로그램 호스팅에 대 한 ASP.NET Core 모듈을 구성 하는 방법."
keywords: "ASP.NET Core ancm, 핵심 모듈, iis, stdout 로깅, 환경 변수, env var, subapplication, subapp, appoffline, app_offline, 502, 스키마"
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: article
ms.assetid: 5de0c8f7-50ce-4e2c-b3d4-a1bd9fdfcff5
ms.technology: aspnet
ms.prod: asp.net-core
uid: hosting/aspnet-core-module
ms.openlocfilehash: ac52b791e02ce52da35fe8d599465076d251b4da
ms.sourcegitcommit: 8005eb4051e568d88ee58d48424f39916052e6e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2017
---
# <a name="aspnet-core-module-configuration-reference"></a><span data-ttu-id="45f91-104">ASP.NET Core 모듈 구성 참조</span><span class="sxs-lookup"><span data-stu-id="45f91-104">ASP.NET Core Module configuration reference</span></span>

<span data-ttu-id="45f91-105">여 [Luke Latham](https://github.com/guardrex), [Rick Anderson](https://twitter.com/RickAndMSFT), 및 [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="45f91-105">By [Luke Latham](https://github.com/guardrex), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="45f91-106">이 문서 ASP.NET Core 응용 프로그램 호스팅에 대 한 ASP.NET Core 모듈을 구성 하는 방법에 자세히 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-106">This document provides details on how to configure the ASP.NET Core Module for hosting ASP.NET Core applications.</span></span> <span data-ttu-id="45f91-107">ASP.NET Core 모듈 및 설치 지침에 대 한 소개를 참조 하십시오.는 [ASP.NET Core 모듈 개요](xref:fundamentals/servers/aspnet-core-module)합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-107">For an introduction to the ASP.NET Core Module and installation instructions, see the [ASP.NET Core Module overview](xref:fundamentals/servers/aspnet-core-module).</span></span>

## <a name="configuration-via-webconfig"></a><span data-ttu-id="45f91-108">Web.config 통해 구성</span><span class="sxs-lookup"><span data-stu-id="45f91-108">Configuration via web.config</span></span>

<span data-ttu-id="45f91-109">ASP.NET Core 모듈 사이트 또는 응용 프로그램을 통해 구성 된 *web.config* 파일을 자체 `aspNetCore` 내 구성 섹션 `system.webServer`합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-109">The ASP.NET Core Module is configured via a site or application *web.config* file and has its own `aspNetCore` configuration section within `system.webServer`.</span></span> <span data-ttu-id="45f91-110">다음은 예제 *web.config* 파일는 `Microsoft.NET.Sdk.Web` SDK는 프로젝트에 대 한 게시 될 때 제공는 [프레임 워크 종속 배포](https://docs.microsoft.com/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) 에 대 한 자리 표시자와는 `processPath` 및 `arguments`:</span><span class="sxs-lookup"><span data-stu-id="45f91-110">Here's an example *web.config* file that the `Microsoft.NET.Sdk.Web` SDK will provide when the project is published for a [framework-dependent deployment](https://docs.microsoft.com/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) with placeholders for the `processPath` and `arguments`:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="%LAUNCHER_PATH%" 
        arguments="%LAUNCHER_ARGS%" 
        stdoutLogEnabled="false" 
        stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="45f91-111">*web.config* 입니다. 아래 예제는 [자체 포함된 배포](https://docs.microsoft.com/dotnet/articles/core/deploying/#self-contained-deployments-scd) 에 [Azure 앱 서비스](https://azure.microsoft.com/services/app-service/)합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-111">The *web.config* example below is for a [self-contained deployment](https://docs.microsoft.com/dotnet/articles/core/deploying/#self-contained-deployments-scd) to the [Azure App Service](https://azure.microsoft.com/services/app-service/).</span></span> <span data-ttu-id="45f91-112">자세한 내용은 참조 [를 IIS에 게시](xref:publishing/iis)합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-112">For more information, see [Publishing to IIS](xref:publishing/iis).</span></span> <span data-ttu-id="45f91-113">참조 [하위 응용 프로그램의 구성을](xref:publishing/iis#configuration-of-sub-applications) 의 구성에 관련 된 중요 정보에 대 한 *web.config* 하위 응용 프로그램의에서 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-113">See [Configuration of sub-applications](xref:publishing/iis#configuration-of-sub-applications) for an important note pertaining to the configuration of *web.config* files in sub-applications.</span></span>

```xml
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe" 
        stdoutLogEnabled="false" 
        stdoutLogFile="\\?\%home%\LogFiles\stdout" />
  </system.webServer>
</configuration>
```

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="45f91-114">AspNetCore 요소의 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-114">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="45f91-115">특성</span><span class="sxs-lookup"><span data-stu-id="45f91-115">Attribute</span></span> | <span data-ttu-id="45f91-116">설명</span><span class="sxs-lookup"><span data-stu-id="45f91-116">Description</span></span> |
| --- | --- |
| <span data-ttu-id="45f91-117">processPath</span><span class="sxs-lookup"><span data-stu-id="45f91-117">processPath</span></span> | <p><span data-ttu-id="45f91-118">필수 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-118">Required string attribute.</span></span></p><p><span data-ttu-id="45f91-119">HTTP 요청을 수신 하는 프로세스를 시작 하는 실행 파일 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-119">Path to the executable that will launch a process listening for HTTP requests.</span></span> <span data-ttu-id="45f91-120">상대 경로 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-120">Relative paths are supported.</span></span> <span data-ttu-id="45f91-121">로 시작 하는 경로 경우 '.'를 경로 사이트 루트 기준 것으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-121">If the path begins with '.', the path is considered to be relative to the site root.</span></span></p><p><span data-ttu-id="45f91-122">기본값은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-122">There is no default value.</span></span></p> |
| <span data-ttu-id="45f91-123">인수</span><span class="sxs-lookup"><span data-stu-id="45f91-123">arguments</span></span> | <p><span data-ttu-id="45f91-124">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-124">Optional string attribute.</span></span></p><p><span data-ttu-id="45f91-125">에 지정 된 실행 파일에 대 한 인수 **processPath**합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-125">Arguments to the executable specified in **processPath**.</span></span></p><p><span data-ttu-id="45f91-126">기본값은 빈 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-126">The default value is an empty string.</span></span></p> |
| <span data-ttu-id="45f91-127">startupTimeLimit</span><span class="sxs-lookup"><span data-stu-id="45f91-127">startupTimeLimit</span></span> | <p><span data-ttu-id="45f91-128">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-128">Optional integer attribute.</span></span></p><p><span data-ttu-id="45f91-129">포트에서 수신 하는 프로세스가 시작 되는 파일에 대 한 모듈에서 대기 하는 시간 (초) 기간입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-129">Duration in seconds that the module will wait for the executable to start a process listening on the port.</span></span> <span data-ttu-id="45f91-130">이 시간 제한을 초과 하는 모듈 프로세스를 중단 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-130">If this time limit is exceeded, the module will kill the process.</span></span> <span data-ttu-id="45f91-131">모듈에서 새 요청을 수신 하 고 계속 시도 응용 프로그램이 시작 되지 않는 한 이후 들어오는 요청에서 프로세스를 다시 시작 하는 경우 프로세스를 다시 시작 하려고 **rapidFailsPerMinute** 번호 마지막 롤링 분에 한 번입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-131">The module will attempt to launch the process again when it receives a new request and will continue to attempt to restart the process on subsequent incoming requests unless the application fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="45f91-132">기본값은 120입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-132">The default value is 120.</span></span></p> |
| <span data-ttu-id="45f91-133">shutdownTimeLimit</span><span class="sxs-lookup"><span data-stu-id="45f91-133">shutdownTimeLimit</span></span> | <p><span data-ttu-id="45f91-134">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-134">Optional integer attribute.</span></span></p><p><span data-ttu-id="45f91-135">기간 (초) 모듈 기다릴 정상적으로 종료 되는 파일에 대 한 경우는 *app_offline.htm* 파일이 검색 됩니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-135">Duration in seconds for which the module will wait for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p><p><span data-ttu-id="45f91-136">기본값은 10입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-136">The default value is 10.</span></span></p> |
| <span data-ttu-id="45f91-137">rapidFailsPerMinute</span><span class="sxs-lookup"><span data-stu-id="45f91-137">rapidFailsPerMinute</span></span> | <p><span data-ttu-id="45f91-138">선택적 정수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-138">Optional integer attribute.</span></span></p><p><span data-ttu-id="45f91-139">에 지정 된 프로세스의 수를 지정 **processPath** 분당 충돌을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-139">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="45f91-140">이 제한을 초과 하는 나머지 분에서 프로세스 시작 모듈 중지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-140">If this limit is exceeded, the module will stop launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="45f91-141">기본값은 10입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-141">The default value is 10.</span></span></p> |
| <span data-ttu-id="45f91-142">requestTimeout</span><span class="sxs-lookup"><span data-stu-id="45f91-142">requestTimeout</span></span> | <p><span data-ttu-id="45f91-143">선택적 timespan 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-143">Optional timespan attribute.</span></span></p><p><span data-ttu-id="45f91-144">ASP.NET Core 모듈 기다릴 ASPNETCORE_PORT % %를 수신 하는 프로세스에서 응답에 대 한 기간을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-144">Specifies the duration for which the ASP.NET Core Module will wait for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="45f91-145">기본값은 "00:02:00"입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-145">The default value is "00:02:00".</span></span></p> |
| <span data-ttu-id="45f91-146">stdoutLogEnabled</span><span class="sxs-lookup"><span data-stu-id="45f91-146">stdoutLogEnabled</span></span> | <p><span data-ttu-id="45f91-147">선택적 부울 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-147">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="45f91-148">True 이면 **stdout** 및 **stderr** 에 지정 된 프로세스에 대 한 **processPath** 에 지정 된 파일에 리디렉션됩니다 **가 stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="45f91-148">If true, **stdout** and **stderr** for the process specified in **processPath** will be redirected to the file specified in **stdoutLogFile**.</span></span></p><p><span data-ttu-id="45f91-149">기본값은 false입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-149">The default value is false.</span></span></p> |
| <span data-ttu-id="45f91-150">가 stdoutLogFile</span><span class="sxs-lookup"><span data-stu-id="45f91-150">stdoutLogFile</span></span> | <p><span data-ttu-id="45f91-151">선택적 문자열 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-151">Optional string attribute.</span></span></p><p><span data-ttu-id="45f91-152">상대 또는 절대 파일 경로를 지정 **stdout** 및 **stderr** 에 지정 된 프로세스에서 **processPath** 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-152">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** will be logged.</span></span> <span data-ttu-id="45f91-153">상대 경로 사이트의 루트를 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-153">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="45f91-154">로 시작 하는 경로 '.'을 사이트 루트 기준 되며 다른 모든 경로 절대 경로로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-154">Any path starting with '.' will be relative to the site root and all other paths will be treated as absolute paths.</span></span> <span data-ttu-id="45f91-155">모든 폴더 경로에 제공 된 로그 파일을 만들 모듈에 대 한 순서에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-155">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="45f91-156">프로세스 ID, 타임 스탬프 (*yyyyMdhms*), 파일 확장명 (*.log*) 밑줄로 구분 기호가 추가 됩니다 마지막 세그먼트는 **가 stdoutLogFile** 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-156">The process ID, timestamp (*yyyyMdhms*), and file extension (*.log*) with underscore delimiters are added to the last segment of the **stdoutLogFile** provided.</span></span></p><p><span data-ttu-id="45f91-157">기본값은 `aspnetcore-stdout`입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-157">The default value is `aspnetcore-stdout`.</span></span></p> |
| <span data-ttu-id="45f91-158">forwardWindowsAuthToken</span><span class="sxs-lookup"><span data-stu-id="45f91-158">forwardWindowsAuthToken</span></span> | <span data-ttu-id="45f91-159">참 또는 거짓입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-159">true or false.</span></span></p><p><span data-ttu-id="45f91-160">True 이면 토큰 요청당 헤더로 ' MS ASPNETCORE WINAUTHTOKEN'ASPNETCORE_PORT % %를 수신 하는 자식 프로세스에 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-160">If true, the token will be forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="45f91-161">것은 해당 프로세스를 요청에 따라이 토큰에 CloseHandle 호출의 책임입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-161">It is the responsibility of that process to call CloseHandle on this token per request.</span></span></p><p><span data-ttu-id="45f91-162">기본값은 true입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-162">The default value is true.</span></span></p> |
| <span data-ttu-id="45f91-163">disableStartUpErrorPage</span><span class="sxs-lookup"><span data-stu-id="45f91-163">disableStartUpErrorPage</span></span> | <span data-ttu-id="45f91-164">참 또는 거짓입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-164">true or false.</span></span></p><p><span data-ttu-id="45f91-165">True 이면는 **502.5-프로세스 오류** 페이지는 표시 되지 않습니다, 그리고 및 502 상태 코드 페이지에 구성 된 프로그램 *web.config* 우선 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-165">If true, the **502.5 - Process Failure** page will be suppressed, and the 502 status code page configured in your *web.config* will take precedence.</span></span></p><p><span data-ttu-id="45f91-166">기본값은 false입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-166">The default value is false.</span></span></p> |

### <a name="setting-environment-variables"></a><span data-ttu-id="45f91-167">환경 변수 설정</span><span class="sxs-lookup"><span data-stu-id="45f91-167">Setting environment variables</span></span>

<span data-ttu-id="45f91-168">ASP.NET Core 모듈에 지정 된 프로세스에 대 한 환경 변수를 지정할 수 있습니다는 `processPath` 하나 이상 지정 하 여 특성 `environmentVariable` 의 자식 요소는 `environmentVariables` 아래 컬렉션 요소는 `aspNetCore` 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-168">The ASP.NET Core Module allows you specify environment variables for the process specified in the `processPath` attribute by specifying them in one or more `environmentVariable` child elements of an `environmentVariables` collection element under the `aspNetCore` element.</span></span> <span data-ttu-id="45f91-169">이 섹션에 설정 된 환경 변수 보다 우선 시스템 환경 변수는 프로세스에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-169">Environment variables set in this section take precedence over system environment variables for the process.</span></span>

<span data-ttu-id="45f91-170">다음 예제에서는 두 개의 환경 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-170">The example below sets two environment variables.</span></span> <span data-ttu-id="45f91-171">`ASPNETCORE_ENVIRONMENT`응용 프로그램의 환경을 구성 합니다 `Development`합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-171">`ASPNETCORE_ENVIRONMENT` will configure the application's environment to `Development`.</span></span> <span data-ttu-id="45f91-172">개발자에 일시적으로이 값을 설정할 수 있습니다는 *web.config* 강제 파일은 [개발자 예외 페이지](xref:fundamentals/error-handling) 를 응용 프로그램 예외를 디버깅 하는 경우 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-172">A developer may temporarily set this value in the *web.config* file in order to force the [developer exception page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="45f91-173">`CONFIG_DIR`사용자 정의 환경 변수의 예 개발자는 경로 구성 하는 응용 프로그램의 구성 파일을 로드 하는 시작 값을 읽을 코드를 기록 했습니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-173">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that will read the value on startup to form a path in order to load the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

## <a name="appofflinehtm"></a><span data-ttu-id="45f91-174">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="45f91-174">app_offline.htm</span></span>

<span data-ttu-id="45f91-175">이름으로 파일을 배치 하는 경우 *app_offline.htm* 웹 응용 프로그램 디렉터리의 루트에 ASP.NET Core 모듈 하려고 응용 프로그램을 정상적으로 종료 되며 들어오는 요청 처리를 중지 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-175">If you place a file with the name *app_offline.htm* at the root of a web application directory, the ASP.NET Core Module will attempt to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="45f91-176">응용 프로그램 실행 되는 경우 `shutdownTimeLimit` 수 ASP.NET Core 모듈 시간 (초)을 실행 중인 프로세스를 중단 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-176">If the app is still running after `shutdownTimeLimit` number of seconds, the ASP.NET Core Module will kill the running process.</span></span>

<span data-ttu-id="45f91-177">반면는 *app_offline.htm* 파일이 없으면 ASP.NET Core 모듈에서 요청에 응답의 내용을 다시 전송 하 여는 *app_offline.htm* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-177">While the *app_offline.htm* file is present, the ASP.NET Core Module will respond to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="45f91-178">한 번의 *app_offline.htm* 다음 요청 로드 한 다음 요청에 응답 하는 응용 프로그램, 파일은 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-178">Once the *app_offline.htm* file is removed, the next request loads the application, which then responds to requests.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="45f91-179">시작 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="45f91-179">Start-up error page</span></span>

<span data-ttu-id="45f91-180">ASP.NET Core 모듈에 백 엔드 프로세스 또는 백 엔드 프로세스 시작 하지만 구성된 된 포트에서 수신 하도록 실패를 시작 하지 못하는 경우 502.5 HTTP 상태 코드 페이지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-180">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, you will see an HTTP 502.5 status code page.</span></span> <span data-ttu-id="45f91-181">이 페이지를 표시 하지 않는 하 여 기본 IIS 502 상태 코드 페이지로 되돌리려면 사용은 `disableStartUpErrorPage` 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-181">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="45f91-182">사용자 정의 오류 메시지를 구성 하는 방법에 대 한 자세한 내용은 참조 하십시오. [HTTP 오류 `<httpErrors>` ](https://docs.microsoft.com/iis/configuration/system.webServer/httpErrors/)합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-182">For more information on configuring custom error messages, see [HTTP Errors `<httpErrors>`](https://docs.microsoft.com/iis/configuration/system.webServer/httpErrors/).</span></span>

![502 상태 페이지](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="45f91-184">로그 만들기 및 리디렉션</span><span class="sxs-lookup"><span data-stu-id="45f91-184">Log creation and redirection</span></span>

<span data-ttu-id="45f91-185">ASP.NET Core 모듈 리디렉션합니다 `stdout` 및 `stderr` 설정 하는 경우 디스크에 로그는 `stdoutLogEnabled` 및 `stdoutLogFile` 의 특성은 `aspNetCore` 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-185">The ASP.NET Core Module redirects `stdout` and `stderr` logs to disk if you set the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element.</span></span> <span data-ttu-id="45f91-186">에 있는 폴더는 `stdoutLogFile` 경로 로그 파일을 만들 모듈에 대 한 순서에 존재 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-186">Any folders in the `stdoutLogFile` path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="45f91-187">타임 스탬프 및 파일 확장명은 로그 파일을 만들 때 자동으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-187">A timestamp and file extension will be added automatically when the log file is created.</span></span> <span data-ttu-id="45f91-188">프로세스 재활용/를 다시 시작이 발생 하지 않으면 로그 회전 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-188">Logs are not rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="45f91-189">것은 로그 사용할 디스크 공간을 제한 하는 호스팅 서비스 공급자의 책임입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-189">It is the responsibility of the hoster to limit the disk space the logs consume.</span></span> <span data-ttu-id="45f91-190">사용 하 여 `stdout` 로그만 일반 응용 프로그램 로깅 목적으로 아닌와 응용 프로그램 시작 문제 해결을 위한 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-190">Using the `stdout` log is only recommended for troubleshooting application startup issues and not for general application logging purposes.</span></span>

<span data-ttu-id="45f91-191">프로세스 ID (PID), 타임 스탬프를 추가 하 여 로그 파일 이름이 구성 됩니다 (*yyyyMdhms*), 파일 확장명 (*.log*)의 마지막 세그먼트에는 `stdoutLogFile` 경로 (일반적으로 *stdout *) 밑줄로 구분 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-191">The log file name is composed by appending the process ID (PID), timestamp (*yyyyMdhms*), and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="45f91-192">예를 들어 경우는 `stdoutLogFile` 로 끝나는 경로 *stdout*, 12시 05분: 02에서 8/10/2017에 만든 10652의 PID 사용 하 여 앱에 대 한 로그 파일 이름이 *stdout_10652_20178101252.log*합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-192">For example if the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 10652 created on 8/10/2017 at 12:05:02 has the file name *stdout_10652_20178101252.log*.</span></span>

<span data-ttu-id="45f91-193">다음은 샘플 `aspNetCore` 구성 요소 `stdout` 로깅.</span><span class="sxs-lookup"><span data-stu-id="45f91-193">Here's a sample `aspNetCore` element that configures `stdout` logging.</span></span> <span data-ttu-id="45f91-194">`stdoutLogFile` 예제에 표시 된 경로 Azure 앱 서비스에 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-194">The `stdoutLogFile` path shown in the example is appropriate for the Azure App Service.</span></span> <span data-ttu-id="45f91-195">로컬 경로 또는 네트워크 공유 경로 로컬 로깅 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-195">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="45f91-196">AppPool 사용자 id 제공 된 경로에 쓸 수 있는 권한이 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-196">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="45f91-197">IIS 사용 하 여 ASP.NET Core 모듈 구성 공유</span><span class="sxs-lookup"><span data-stu-id="45f91-197">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="45f91-198">ASP.NET Core 모듈 설치 관리자의 권한으로 실행 되는 **시스템** 계정.</span><span class="sxs-lookup"><span data-stu-id="45f91-198">The ASP.NET Core Module installer runs with the privileges of the **SYSTEM** account.</span></span> <span data-ttu-id="45f91-199">설치 관리자가 액세스 거부 오류가에서 모듈 설정을 구성 하는 동안 적중지 것입니다 로컬 시스템 계정 IIS 공유 구성에 사용 되는 공유 경로 대 한 권한을 수정지 않습니다, 때문에 * applicationHost.config* 공유에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-199">Because the local system account does not have modify permission for the share path which is used by the IIS Shared Configuration, the installer will hit an access denied error when attempting to configure the module settings in *applicationHost.config* on the share.</span></span>

<span data-ttu-id="45f91-200">지원 되지 않는 문제를 해결 하 고 설치 프로그램을 실행 IIS 공유 구성을 사용 하지 않도록 설정, 업데이트 된 내보내기는 *applicationHost.config* 는 공유에 파일을 다시 IIS 공유 구성을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-200">The unsupported workaround is to disable the IIS Shared Configuration, run the installer, export the updated *applicationHost.config* file to the share, and re-enable the IIS Shared Configuration.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="45f91-201">모듈, 스키마 및 구성 파일 위치</span><span class="sxs-lookup"><span data-stu-id="45f91-201">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="45f91-202">모듈</span><span class="sxs-lookup"><span data-stu-id="45f91-202">Module</span></span>

<span data-ttu-id="45f91-203">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="45f91-203">**IIS (x86/amd64):**</span></span>

   * <span data-ttu-id="45f91-204">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="45f91-204">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

   * <span data-ttu-id="45f91-205">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="45f91-205">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="45f91-206">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="45f91-206">**IIS Express (x86/amd64):**</span></span>

   * <span data-ttu-id="45f91-207">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="45f91-207">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

   * <span data-ttu-id="45f91-208">%ProgramFiles (x86) %\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="45f91-208">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="45f91-209">스키마</span><span class="sxs-lookup"><span data-stu-id="45f91-209">Schema</span></span>

<span data-ttu-id="45f91-210">**IIS**</span><span class="sxs-lookup"><span data-stu-id="45f91-210">**IIS**</span></span>

   * <span data-ttu-id="45f91-211">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="45f91-211">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="45f91-212">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="45f91-212">**IIS Express**</span></span>

   * <span data-ttu-id="45f91-213">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="45f91-213">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="45f91-214">구성</span><span class="sxs-lookup"><span data-stu-id="45f91-214">Configuration</span></span>

<span data-ttu-id="45f91-215">**IIS**</span><span class="sxs-lookup"><span data-stu-id="45f91-215">**IIS**</span></span>

   * <span data-ttu-id="45f91-216">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="45f91-216">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="45f91-217">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="45f91-217">**IIS Express**</span></span>

   * <span data-ttu-id="45f91-218">.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="45f91-218">.vs\config\applicationHost.config</span></span>

<span data-ttu-id="45f91-219">검색할 수 있습니다 *aspnetcore.dll* 에 *applicationHost.config* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-219">You can search for *aspnetcore.dll* in the *applicationHost.config* file.</span></span> <span data-ttu-id="45f91-220">IIS express는 *applicationHost.config* 파일은 기본적으로 존재 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-220">For IIS Express, the *applicationHost.config* file won't exist by default.</span></span> <span data-ttu-id="45f91-221">파일을 만들 *{응용 프로그램 루트}\.vs\config* Visual Studio 솔루션에서 모든 웹 응용 프로그램 프로젝트를 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="45f91-221">The file is created at *{application root}\.vs\config* when you start any web application project in the Visual Studio solution.</span></span>
