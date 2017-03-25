---
title: "개발 하는 동안 앱 암호의 안전한 저장소 | Microsoft 문서"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: f85371b7-44cc-4592-ac8b-7029bdced911
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/app-secrets
translationtype: Machine Translation
ms.sourcegitcommit: 0ebb9b63931ccb26126740de08270eda9b1ea486
ms.openlocfilehash: 210e04ee0fabbe255de75856dad3db9265ff67d9
ms.lasthandoff: 03/23/2017

---
# <a name="safe-storage-of-app-secrets-during-development"></a>개발 하는 동안 앱 암호의 안전한 저장소

<a name=security-app-secrets></a>

여 [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Daniel Roth](https://github.com/danroth27)

이 문서에서는 암호 관리자 도구를 사용 하 여 코드에서 암호를 유지 하는 방법을 보여 줍니다. 가장 중요 한 점은 개발 및 테스트 모드에서 프로덕션 비밀 정보를 사용해 서는 안 하 고 소스 코드에서 암호 또는 기타 중요 한 데이터를 저장 하지 마십시오. 대신 사용할 수는 [구성](../fundamentals/configuration.md) 에 환경 변수에서 이러한 값을 읽는 또는 보안 관리자를 사용 하 여 저장 된 값에서 도구는 시스템입니다. 암호 관리자 도구 하면 중요 한 데이터 소스 제어에 체크 인 되는 것을 방지할 수 있습니다. [구성](../fundamentals/configuration.md) 시스템에서이 문서에 설명 된 비밀 관리자 도구와 함께 저장 된 암호를 읽을 수 있습니다.

## <a name="environment-variables"></a>환경 변수

코드 또는 로컬 구성 파일에 앱 암호를 저장 하지 않도록 하려면 환경 변수에서 비밀을 저장 합니다. 설정할 수는 [구성](../fundamentals/configuration.md) 프레임 워크를 호출 하 여 환경 변수에서 값을 읽을 `AddEnvironmentVariables`합니다. 그런 다음 모든 이전에 지정한 구성 소스에 대 한 구성 값을 재정의 하도록 환경 변수를 사용할 수 있습니다.

예를 들어, 개별 사용자 계정으로 새로운 ASP.NET 핵심 웹 응용 프로그램을 만들면 기본 연결 문자열을 추가 됩니다는 *appsettings.json* 키를 사용 하 여 프로젝트에서 파일 `DefaultConnection`합니다. 기본 연결 문자열에는 사용자 모드에서 실행 하 고 암호를 요구 하지 않습니다 LocalDB를 사용 하도록 설정 되어 있습니다. 재정의할 수는 테스트 또는 프로덕션 서버 응용 프로그램을 배포 하는 시기는 `DefaultConnection` 테스트 또는 프로덕션 데이터베이스 서버에 대 한 연결 문자열 (잠재적으로 중요 한 자격 증명)를 포함 하는 환경 변수 설정 된 키 값입니다.

>[!WARNING]
> 환경 변수는 일반적으로 일반 텍스트로 저장 되며 암호화 되지 않습니다. 컴퓨터 또는 프로세스가 손상 되 면 신뢰할 수 없는 당사자가 환경 변수를 액세스할 수 있습니다. 사용자의 비밀을 공개 하지 않도록 하 여 추가적인 조치 여전히 필요할 수 있습니다.

## <a name="secret-manager"></a>암호 관리자

암호 관리자 도구는 프로젝트 트리에서 외부에서 개발 작업에 대 한 중요 한 데이터를 저장합니다. 암호 관리자 도구는에 대 한 비밀 정보를 저장 하는 사용할 수 있는 프로젝트 도구는 [.NET Core](https://microsoft.com/net/core) 개발 중 프로젝트입니다. 암호 관리자 도구로 앱 암호는 특정 프로젝트에 연결할 수 있으며 여러 프로젝트에서 공유할 수 있습니다.

>[!WARNING]
> 암호 관리자 도구 저장 된 암호를 암호화 하지 않습니다 및 신뢰할 수 있는 저장소 처리 되어서는 안 됩니다. 개발용 으로만 제공 됩니다. 키와 값은 사용자 프로필 디렉터리에서 JSON 구성 파일에 저장 됩니다.

### <a name="installing-the-secret-manager-tool"></a>암호 관리자 도구를 설치합니다. 

추가 `Microsoft.Extensions.SecretManager.Tools` 에 *.csproj* 파일을 실행 `dotnet restore`합니다. 

[!code-xml[주](app-secrets/sample/UserSecrets/UserSecrets.csproj?highlight=17)]

다음 명령을 실행 하 여 암호 관리자 도구를 테스트 합니다.

```
dotnet user-secrets -h
     ```

The Secret Manager tool will display usage, options and command help.

> [!NOTE]
> You must be in the same directory of the *.csproj* file to run tools defined in the *.csproj* file.

The Secret Manager tool operates on project specific configuration settings that are stored in your user profile. To use user secrets the project must specify a `UserSecretsId` value in its *.csproj* file. The value of `UserSecretsId` is arbitrary, but is generally unique to the project. Developers typically generate a GUID for the `UserSecretsId`.

Add a `UserSecretsId` for your project in the  *.csproj* file :

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets.csproj?range=7-9&highlight=2)]

Use the Secret Manager tool to set a secret. For example, in a command window from the project directory enter the following:

```
dotnet 사용자 비밀 MySecret ValueOfMySecret 설정
     ```

다른 디렉터리에서 보안 관리자 도구를 실행할 수는 있지만 사용 해야는 `--project` 옵션에 대 한 경로 전달 하는 *.csproj* 파일:
 
```
dotnet user-secrets set MySecret ValueOfMySecret --project c:\work\WebApp1\src\webapp1
   ```

또한 나열, 제거 및 앱 암호의 선택을 취소 하는 보안 관리자 도구를 사용할 수 있습니다.

## <a name="accessing-user-secrets-via-configuration"></a>구성을 통해 사용자의 비밀에 액세스

구성 시스템을 통해 보안 관리자 암호에 액세스 합니다. 추가 된 `Microsoft.Extensions.Configuration.UserSecrets` 패키지 및 실행 `dotnet restore`합니다.

사용자 암호 구성 소스를 추가 `Startup` 메서드:

[!code-csharp[주](app-secrets/sample/UserSecrets/Startup.cs?highlight=16-19)]

구성 API 통해 사용자의 비밀에 액세스할 수 있습니다.

[!code-csharp[주](app-secrets/sample/UserSecrets/Startup.cs?highlight=26-29)]

## <a name="how-the-secret-manager-tool-works"></a>암호 관리자 도구 작동 원리

암호 관리자 도구 값을 저장 하는 위치와 방법을 같은 구현 세부 정보를 추상화 합니다. 이러한 구현 세부 사항을 알지 못해도 도구를 사용할 수 있습니다. 현재 버전에서는 값에 저장 되는 [JSON](http://json.org/) 사용자 프로필 디렉터리의 구성 파일:

* Windows의 경우:`%APPDATA%\microsoft\UserSecrets\<userSecretsId>\secrets.json`

* Linux:`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

* Mac:`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

값 `userSecretsId` 에 지정 된 값에서 가져온 *.csproj* 파일입니다.

이러한 구현 정보 변경 될 수 있습니다 위치 또는 보안 관리자 도구와 함께 저장 된 데이터의 형식에 의존 하는 코드를 작성 하지 말아야 합니다. 예를 들어 암호 값은 현재 *하지* 오늘날 암호화 되지만 언젠가 될 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [구성](../fundamentals/configuration.md)

