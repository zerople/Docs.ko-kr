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
# <a name="aspnet-core-module-configuration-reference"></a>ASP.NET Core 모듈 구성 참조

여 [Luke Latham](https://github.com/guardrex), [Rick Anderson](https://twitter.com/RickAndMSFT), 및 [Sourabh Shirhatti](https://twitter.com/sshirhatti)

이 문서 ASP.NET Core 응용 프로그램 호스팅에 대 한 ASP.NET Core 모듈을 구성 하는 방법에 자세히 설명 합니다. ASP.NET Core 모듈 및 설치 지침에 대 한 소개를 참조 하십시오.는 [ASP.NET Core 모듈 개요](xref:fundamentals/servers/aspnet-core-module)합니다.

## <a name="configuration-via-webconfig"></a>Web.config 통해 구성

ASP.NET Core 모듈 사이트 또는 응용 프로그램을 통해 구성 된 *web.config* 파일을 자체 `aspNetCore` 내 구성 섹션 `system.webServer`합니다. 다음은 예제 *web.config* 파일는 `Microsoft.NET.Sdk.Web` SDK는 프로젝트에 대 한 게시 될 때 제공는 [프레임 워크 종속 배포](https://docs.microsoft.com/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) 에 대 한 자리 표시자와는 `processPath` 및 `arguments`:

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

*web.config* 입니다. 아래 예제는 [자체 포함된 배포](https://docs.microsoft.com/dotnet/articles/core/deploying/#self-contained-deployments-scd) 에 [Azure 앱 서비스](https://azure.microsoft.com/services/app-service/)합니다. 자세한 내용은 참조 [를 IIS에 게시](xref:publishing/iis)합니다. 참조 [하위 응용 프로그램의 구성을](xref:publishing/iis#configuration-of-sub-applications) 의 구성에 관련 된 중요 정보에 대 한 *web.config* 하위 응용 프로그램의에서 파일입니다.

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

### <a name="attributes-of-the-aspnetcore-element"></a>AspNetCore 요소의 특성입니다.

| 특성 | 설명 |
| --- | --- |
| processPath | <p>필수 문자열 특성입니다.</p><p>HTTP 요청을 수신 하는 프로세스를 시작 하는 실행 파일 경로입니다. 상대 경로 지원 합니다. 로 시작 하는 경로 경우 '.'를 경로 사이트 루트 기준 것으로 간주 됩니다.</p><p>기본값은 없습니다.</p> |
| 인수 | <p>선택적 문자열 특성입니다.</p><p>에 지정 된 실행 파일에 대 한 인수 **processPath**합니다.</p><p>기본값은 빈 문자열입니다.</p> |
| startupTimeLimit | <p>선택적 정수 특성입니다.</p><p>포트에서 수신 하는 프로세스가 시작 되는 파일에 대 한 모듈에서 대기 하는 시간 (초) 기간입니다. 이 시간 제한을 초과 하는 모듈 프로세스를 중단 합니다. 모듈에서 새 요청을 수신 하 고 계속 시도 응용 프로그램이 시작 되지 않는 한 이후 들어오는 요청에서 프로세스를 다시 시작 하는 경우 프로세스를 다시 시작 하려고 **rapidFailsPerMinute** 번호 마지막 롤링 분에 한 번입니다.</p><p>기본값은 120입니다.</p> |
| shutdownTimeLimit | <p>선택적 정수 특성입니다.</p><p>기간 (초) 모듈 기다릴 정상적으로 종료 되는 파일에 대 한 경우는 *app_offline.htm* 파일이 검색 됩니다.</p><p>기본값은 10입니다.</p> |
| rapidFailsPerMinute | <p>선택적 정수 특성입니다.</p><p>에 지정 된 프로세스의 수를 지정 **processPath** 분당 충돌을 허용 합니다. 이 제한을 초과 하는 나머지 분에서 프로세스 시작 모듈 중지 됩니다.</p><p>기본값은 10입니다.</p> |
| requestTimeout | <p>선택적 timespan 특성입니다.</p><p>ASP.NET Core 모듈 기다릴 ASPNETCORE_PORT % %를 수신 하는 프로세스에서 응답에 대 한 기간을 지정 합니다.</p><p>기본값은 "00:02:00"입니다.</p> |
| stdoutLogEnabled | <p>선택적 부울 특성입니다.</p><p>True 이면 **stdout** 및 **stderr** 에 지정 된 프로세스에 대 한 **processPath** 에 지정 된 파일에 리디렉션됩니다 **가 stdoutLogFile**.</p><p>기본값은 false입니다.</p> |
| 가 stdoutLogFile | <p>선택적 문자열 특성입니다.</p><p>상대 또는 절대 파일 경로를 지정 **stdout** 및 **stderr** 에 지정 된 프로세스에서 **processPath** 기록 됩니다. 상대 경로 사이트의 루트를 기준으로 합니다. 로 시작 하는 경로 '.'을 사이트 루트 기준 되며 다른 모든 경로 절대 경로로 간주 합니다. 모든 폴더 경로에 제공 된 로그 파일을 만들 모듈에 대 한 순서에 있어야 합니다. 프로세스 ID, 타임 스탬프 (*yyyyMdhms*), 파일 확장명 (*.log*) 밑줄로 구분 기호가 추가 됩니다 마지막 세그먼트는 **가 stdoutLogFile** 제공 합니다.</p><p>기본값은 `aspnetcore-stdout`입니다.</p> |
| forwardWindowsAuthToken | 참 또는 거짓입니다.</p><p>True 이면 토큰 요청당 헤더로 ' MS ASPNETCORE WINAUTHTOKEN'ASPNETCORE_PORT % %를 수신 하는 자식 프로세스에 전달 됩니다. 것은 해당 프로세스를 요청에 따라이 토큰에 CloseHandle 호출의 책임입니다.</p><p>기본값은 true입니다.</p> |
| disableStartUpErrorPage | 참 또는 거짓입니다.</p><p>True 이면는 **502.5-프로세스 오류** 페이지는 표시 되지 않습니다, 그리고 및 502 상태 코드 페이지에 구성 된 프로그램 *web.config* 우선 적용 됩니다.</p><p>기본값은 false입니다.</p> |

### <a name="setting-environment-variables"></a>환경 변수 설정

ASP.NET Core 모듈에 지정 된 프로세스에 대 한 환경 변수를 지정할 수 있습니다는 `processPath` 하나 이상 지정 하 여 특성 `environmentVariable` 의 자식 요소는 `environmentVariables` 아래 컬렉션 요소는 `aspNetCore` 요소입니다. 이 섹션에 설정 된 환경 변수 보다 우선 시스템 환경 변수는 프로세스에 대 한 합니다.

다음 예제에서는 두 개의 환경 변수를 설정합니다. `ASPNETCORE_ENVIRONMENT`응용 프로그램의 환경을 구성 합니다 `Development`합니다. 개발자에 일시적으로이 값을 설정할 수 있습니다는 *web.config* 강제 파일은 [개발자 예외 페이지](xref:fundamentals/error-handling) 를 응용 프로그램 예외를 디버깅 하는 경우 로드 합니다. `CONFIG_DIR`사용자 정의 환경 변수의 예 개발자는 경로 구성 하는 응용 프로그램의 구성 파일을 로드 하는 시작 값을 읽을 코드를 기록 했습니다.

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

## <a name="appofflinehtm"></a>app_offline.htm

이름으로 파일을 배치 하는 경우 *app_offline.htm* 웹 응용 프로그램 디렉터리의 루트에 ASP.NET Core 모듈 하려고 응용 프로그램을 정상적으로 종료 되며 들어오는 요청 처리를 중지 합니다. 응용 프로그램 실행 되는 경우 `shutdownTimeLimit` 수 ASP.NET Core 모듈 시간 (초)을 실행 중인 프로세스를 중단 합니다.

반면는 *app_offline.htm* 파일이 없으면 ASP.NET Core 모듈에서 요청에 응답의 내용을 다시 전송 하 여는 *app_offline.htm* 파일입니다. 한 번의 *app_offline.htm* 다음 요청 로드 한 다음 요청에 응답 하는 응용 프로그램, 파일은 제거 됩니다.

## <a name="start-up-error-page"></a>시작 오류 페이지

ASP.NET Core 모듈에 백 엔드 프로세스 또는 백 엔드 프로세스 시작 하지만 구성된 된 포트에서 수신 하도록 실패를 시작 하지 못하는 경우 502.5 HTTP 상태 코드 페이지가 표시 됩니다. 이 페이지를 표시 하지 않는 하 여 기본 IIS 502 상태 코드 페이지로 되돌리려면 사용은 `disableStartUpErrorPage` 특성입니다. 사용자 정의 오류 메시지를 구성 하는 방법에 대 한 자세한 내용은 참조 하십시오. [HTTP 오류 `<httpErrors>` ](https://docs.microsoft.com/iis/configuration/system.webServer/httpErrors/)합니다.

![502 상태 페이지](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a>로그 만들기 및 리디렉션

ASP.NET Core 모듈 리디렉션합니다 `stdout` 및 `stderr` 설정 하는 경우 디스크에 로그는 `stdoutLogEnabled` 및 `stdoutLogFile` 의 특성은 `aspNetCore` 요소입니다. 에 있는 폴더는 `stdoutLogFile` 경로 로그 파일을 만들 모듈에 대 한 순서에 존재 해야 합니다. 타임 스탬프 및 파일 확장명은 로그 파일을 만들 때 자동으로 추가 됩니다. 프로세스 재활용/를 다시 시작이 발생 하지 않으면 로그 회전 되지 않습니다. 것은 로그 사용할 디스크 공간을 제한 하는 호스팅 서비스 공급자의 책임입니다. 사용 하 여 `stdout` 로그만 일반 응용 프로그램 로깅 목적으로 아닌와 응용 프로그램 시작 문제 해결을 위한 것이 좋습니다.

프로세스 ID (PID), 타임 스탬프를 추가 하 여 로그 파일 이름이 구성 됩니다 (*yyyyMdhms*), 파일 확장명 (*.log*)의 마지막 세그먼트에는 `stdoutLogFile` 경로 (일반적으로 *stdout *) 밑줄로 구분 합니다. 예를 들어 경우는 `stdoutLogFile` 로 끝나는 경로 *stdout*, 12시 05분: 02에서 8/10/2017에 만든 10652의 PID 사용 하 여 앱에 대 한 로그 파일 이름이 *stdout_10652_20178101252.log*합니다.

다음은 샘플 `aspNetCore` 구성 요소 `stdout` 로깅. `stdoutLogFile` 예제에 표시 된 경로 Azure 앱 서비스에 적합 합니다. 로컬 경로 또는 네트워크 공유 경로 로컬 로깅 있습니다. AppPool 사용자 id 제공 된 경로에 쓸 수 있는 권한이 있는지 확인 합니다.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS 사용 하 여 ASP.NET Core 모듈 구성 공유

ASP.NET Core 모듈 설치 관리자의 권한으로 실행 되는 **시스템** 계정. 설치 관리자가 액세스 거부 오류가에서 모듈 설정을 구성 하는 동안 적중지 것입니다 로컬 시스템 계정 IIS 공유 구성에 사용 되는 공유 경로 대 한 권한을 수정지 않습니다, 때문에 * applicationHost.config* 공유에 있습니다.

지원 되지 않는 문제를 해결 하 고 설치 프로그램을 실행 IIS 공유 구성을 사용 하지 않도록 설정, 업데이트 된 내보내기는 *applicationHost.config* 는 공유에 파일을 다시 IIS 공유 구성을 사용 하도록 설정 합니다.

## <a name="module-schema-and-configuration-file-locations"></a>모듈, 스키마 및 구성 파일 위치

### <a name="module"></a>모듈

**IIS (x86/amd64):**

   * %windir%\System32\inetsrv\aspnetcore.dll

   * %windir%\SysWOW64\inetsrv\aspnetcore.dll

**IIS Express (x86/amd64):**

   * %ProgramFiles%\IIS Express\aspnetcore.dll

   * %ProgramFiles (x86) %\IIS Express\aspnetcore.dll

### <a name="schema"></a>스키마

**IIS**

   * %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Express**

   * %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

### <a name="configuration"></a>구성

**IIS**

   * %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

   * .vs\config\applicationHost.config

검색할 수 있습니다 *aspnetcore.dll* 에 *applicationHost.config* 파일입니다. IIS express는 *applicationHost.config* 파일은 기본적으로 존재 하지 않습니다. 파일을 만들 *{응용 프로그램 루트}\.vs\config* Visual Studio 솔루션에서 모든 웹 응용 프로그램 프로젝트를 시작 합니다.
