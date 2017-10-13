---
title: "ASP.NET Core에서 개발 하는 동안 앱 암호의 안전한 저장소"
author: rick-anderson
description: "안전 하 게 개발 하는 동안 암호를 저장 하는 방법을 보여 줍니다."
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 09/15/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/app-secrets
ms.openlocfilehash: 280819a6a0afb72311f0d50f7d3b83a942e9fcc3
ms.sourcegitcommit: e3b1726cc04e80dc28464c35259edbd3bc39a438
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2017
---
# <a name="safe-storage-of-app-secrets-during-development-in-aspnet-core"></a>ASP.NET Core에서 개발 하는 동안 앱 암호의 안전한 저장소

여 [Rick Anderson](https://twitter.com/RickAndMSFT), [김 Roth](https://github.com/danroth27), 및 [Scott Addie](https://scottaddie.com) 

이 문서 코드 나가기를 비밀을 유지 하려면 개발에서 암호 관리자 도구를 사용 하는 방법을 보여줍니다. 가장 중요 한 점은 소스 코드에서 암호 또는 기타 중요 한 데이터를 저장 하지 않으며 해야 하 고 개발 및 테스트 모드에서 프로덕션 암호를 사용 하지 않아야 합니다. 대신 사용할 수 있습니다는 [구성](../fundamentals/configuration.md) 시스템 환경 변수에서 이러한 값을 읽거나 암호 관리자를 사용 하 여 저장 된 값에서 도구입니다. 암호 관리자 도구에서 소스 제어에 체크 인 되 고 중요 한 데이터를 방지할 수 있습니다. [구성](../fundamentals/configuration.md) 시스템이이 문서에 설명 된 비밀 관리자 도구와 함께 저장 된 암호를 읽을 수 있습니다.

암호 관리자 도구 개발에만 사용 됩니다. 와 Azure 테스트 및 프로덕션 비밀을 보호할 수는 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 구성 공급자입니다. 참조 [Azure 키 자격 증명 모음 구성 공급자](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration) 자세한 정보에 대 한 합니다.

## <a name="environment-variables"></a>환경 변수

에 로컬 구성 파일 또는 코드에서 앱 암호를 저장 하지 않도록 하려면 환경 변수에서 암호를 저장 합니다. 설정할 수 있습니다는 [구성](../fundamentals/configuration.md) 프레임 워크를 호출 하 여 환경 변수에서 값을 읽을 `AddEnvironmentVariables`합니다. 그런 다음 환경 변수를 사용 하 여 모든 이전에 지정 된 구성 소스에 대 한 구성 값을 재정의 합니다.

예를 들어, 개별 사용자 계정으로 새 ASP.NET Core 웹 앱을 만들면 기본 연결 문자열을 추가 되는 *appsettings.json* 키를 사용 하 여 프로젝트의 파일 `DefaultConnection`합니다. 기본 연결 문자열에는 사용자 모드에서 실행 되 고 암호가 필요 하지 않습니다는 LocalDB를 사용 하도록 설정 되어 있습니다. 테스트 또는 프로덕션 서버에 응용 프로그램을 배포할 때 재정의할 수 있습니다는 `DefaultConnection` 테스트 또는 프로덕션 데이터베이스에 대 한 연결 문자열 (잠재적으로 중요 한 자격 증명)을 포함 하는 환경 변수 설정을 사용 하 여 키 값 서버입니다.

>[!WARNING]
> 환경 변수는 일반적으로 일반 텍스트로 저장 되며 암호화 되지 않습니다. 컴퓨터 또는 프로세스가 손상 된 환경 변수 신뢰할 수 없는 당사자가 액세스할 수 있습니다. 여전히 사용자 비밀 정보를 공개 하지 않도록 추가 조치가 필요할 수 있습니다.

## <a name="secret-manager"></a>암호 관리자

암호 관리자 도구는 프로젝트 트리 외부의 개발 작업에 대 한 중요 한 데이터를 저장합니다. 암호 관리자 도구는에 대 한 기밀 정보를 사용할 수 있는 프로젝트 도구는 [.NET Core](https://www.microsoft.com/net/core) 개발 중 프로젝트. 암호 관리자 도구를 사용 특정 프로젝트와 앱 암호를 연결 하 고 여러 프로젝트 간에 공유할 수 있습니다.

>[!WARNING]
> 암호 관리자 도구 저장 된 암호를 암호화 하지 않습니다 하 고 신뢰할 수 있는 저장소로 처리 되지 해야 합니다. 개발 용도로입니다. 키와 값은 사용자 프로필 디렉터리에는 JSON 구성 파일에 저장 됩니다.

## <a name="installing-the-secret-manager-tool"></a>암호 관리자 도구를 설치합니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **편집 \<project_name\>.csproj** 상황에 맞는 메뉴입니다. 강조 표시 된 줄을 추가 *.csproj* 파일과 연결 된 NuGet 패키지를 복원 하려면 저장:

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-before.csproj?highlight=10)]

솔루션 탐색기에서 프로젝트를 다시 마우스 오른쪽 단추로 클릭 하 고 선택 **관리 사용자의 비밀** 상황에 맞는 메뉴입니다. 이 제스처를 새로 추가 `UserSecretsId` 내에서 노드는 `PropertyGroup` 의 *.csproj* 다음 샘플에 강조 표시 된 대로 파일:

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-after.csproj?highlight=4)]

수정 된 저장 *.csproj* 파일도 열립니다는 `secrets.json` 파일 텍스트 편집기에서. 내용을 대체는 `secrets.json` 를 다음 코드로 파일:

```json
{
    "MySecret": "ValueOfMySecret"
}
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

추가 `Microsoft.Extensions.SecretManager.Tools` 에 *.csproj* 파일을 실행 `dotnet restore`합니다. 명령줄을 사용 하 여 암호 관리자 도구를 설치 하려면 동일한 단계를 사용할 수 있습니다.

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-before.csproj?highlight=10)]

다음 명령을 실행 하 여 암호 관리자 도구를 테스트 합니다.

```console
dotnet user-secrets -h
```

암호 관리자 도구는 사용, 옵션 및 명령 도움말 표시 됩니다.

> [!NOTE]
> 와 같은 디렉터리에 있어야는 *.csproj* 파일에 정의 된 도구를 실행 하는 *.csproj* 파일의 `DotNetCliToolReference` 노드.

암호 관리자 도구는 사용자 프로필에 저장 되어 있는 프로젝트 관련 구성 설정에서 작동 합니다. 사용자 암호를 사용 하려면 프로젝트 지정 해야 합니다는 `UserSecretsId` 에서 값을 해당 *.csproj* 파일입니다. 값 `UserSecretsId` 은 선택적 요소 이지만 프로젝트에 일반적으로 고유 합니다. 에 대 한 GUID를 일반적으로 생성 하는 개발자는 `UserSecretsId`합니다.

추가 `UserSecretsId` 에서 프로젝트에 대 한는 *.csproj* 파일:

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets-after.csproj?highlight=4)]

암호 관리자 도구를 사용 하 여 암호를 설정할 수 있습니다. 예를 들어 프로젝트 디렉터리에서 명령 창에서 다음을 입력 합니다.

```console
dotnet user-secrets set MySecret ValueOfMySecret
```

다른 디렉터리에서 암호 관리자 도구를 실행할 수 있지만 사용 해야 합니다는 `--project` 옵션에 대 한 경로 전달 하는 *.csproj* 파일:
 
```console
dotnet user-secrets set MySecret ValueOfMySecret --project c:\work\WebApp1\src\webapp1
```

또한 나열, 제거 및 앱 암호를 지울 수는 암호 관리자 도구를 사용할 수 있습니다.

-----

## <a name="accessing-user-secrets-via-configuration"></a>구성을 통해 사용자의 비밀 정보에 액세스

구성 시스템을 통해 보안 관리자 암호에 액세스 합니다. 추가 `Microsoft.Extensions.Configuration.UserSecrets` 패키지 및 실행 `dotnet restore`합니다.

사용자 암호 구성 소스를 추가 `Startup` 메서드:

[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=16-19)]

구성 API 통해 사용자의 비밀을 액세스할 수 있습니다.

[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=26-29)]

## <a name="how-the-secret-manager-tool-works"></a>암호 관리자 도구 작동 방식

암호 관리자 도구 값을 저장 하는 위치와 방법을 같은 구현 세부 정보를 추상화 합니다. 이러한 구현 정보를 알 필요 없이 도구를 사용할 수 있습니다. 현재 버전의 값에 저장 됩니다는 [JSON](http://json.org/) 사용자 프로필 디렉터리에 구성 파일:

* Windows:`%APPDATA%\microsoft\UserSecrets\<userSecretsId>\secrets.json`

* Linux:`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

* Mac:`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

값 `userSecretsId` 에 지정 된 값에서 가져온 *.csproj* 파일입니다.

이러한 구현 정보 변경 될 수 있으므로 위치 또는 보안 관리자 도구와 함께 저장 된 데이터의 형식에 의존 하는 코드를 작성 하지 말아야 합니다. 예를 들어 비밀 값은 현재 *하지* 오늘 암호화 되지만 언젠가 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [구성](../fundamentals/configuration.md)
