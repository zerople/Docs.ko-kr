---
title: "Azure 키 자격 증명 모음 구성 공급자"
author: guardrex
description: "Azure 키 자격 증명 모음 구성 공급자를 사용 하 여 런타임에 로드 하는 이름-값 쌍을 사용 하 여 응용 프로그램을 구성 하는 방법에 알아봅니다."
keywords: "ASP.NET Core, 구성, Azure 주요 자격 증명 모음"
ms.author: riande
manager: wpickett
ms.date: 08/09/2017
ms.topic: article
ms.assetid: 0292bdae-b3ed-4637-bd67-19b9bb8b65cb
ms.prod: asp.net-core
uid: security/key-vault-configuration
ms.openlocfilehash: 5122918ff84d87dd9763d454412f83d6f2c60fcb
ms.sourcegitcommit: 8005eb4051e568d88ee58d48424f39916052e6e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2017
---
# <a name="azure-key-vault-configuration-provider"></a>Azure 키 자격 증명 모음 구성 공급자

여 [Luke Latham](https://github.com/guardrex) 및 [Andrew Stanton 간호사](https://github.com/anurse)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

보기 또는 2.x에 대 한 샘플 코드를 다운로드 합니다.

* [기본 샘플](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/samples/basic-sample/2.x) -는 응용 프로그램에 보안 값을 읽습니다.
* [키 이름 접두사 샘플](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/samples/key-name-prefix-sample/2.x) -서로 다른 각 응용 프로그램 버전에 대 한 비밀 값 집합을 로드할 수 있는 응용 프로그램의 버전을 나타내는 키 이름 접두사를 사용 하 여 읽기 비밀 값입니다.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

보기 또는 1.x에 대 한 샘플 코드를 다운로드 합니다.

* [기본 샘플](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/samples/basic-sample/1.x) -는 응용 프로그램에 보안 값을 읽습니다.
* [키 이름 접두사 샘플](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/samples/key-name-prefix-sample/1.x) -서로 다른 각 응용 프로그램 버전에 대 한 비밀 값 집합을 로드할 수 있는 응용 프로그램의 버전을 나타내는 키 이름 접두사를 사용 하 여 읽기 비밀 값입니다. 

---

이 문서에서는 사용 하는 방법에 설명 된 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 Azure 키 자격 증명 모음 암호에서 응용 프로그램 구성 값을 로드 합니다. Azure 키 자격 증명 모음에는 암호화 키와 앱 및 서비스에서 사용 하는 암호를 보호 하는 데 도움이 되는 클라우드 기반 서비스입니다. 일반적인 시나리오는 중요 한 구성 데이터에 대 한 액세스 제어를 포함 하며 FIPS 140-2에 대 한 요구 사항을 충족 수준 2 하드웨어 보안 모듈 (HSM의) 될 때 확인 구성 데이터를 저장 합니다. 이 기능은 이상의 ASP.NET Core 1.1을 대상으로 하는 응용 프로그램에 대해 사용할 수 있습니다.

## <a name="package"></a>패키지
공급자를 사용 하려면 추가에 대 한 참조는 [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 패키지 합니다.

## <a name="application-configuration"></a>응용 프로그램 구성
사용 하 여 공급자를 탐색할 수 있습니다는 [앱 샘플](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)합니다. 주요 자격 증명 모음을 설정 하 고 자격 증명 모음에 암호를 만들 샘플 앱은 안전 하 게을 로드 하는 비밀 값 해당 구성에 웹 페이지에 표시할.

공급자에 추가 되는 `ConfigurationBuilder` 와 `AddAzureKeyVault` 확장 합니다. 샘플 응용 프로그램에서 확장 프로그램은 사용에서 로드 된 세 가지 구성 값은 *appsettings.json* 파일입니다.

| 앱 설정    | 설명                    | 예제                                      |
| -------------- | ------------------------------ | -------------------------------------------- |
| `Vault`        | Azure 키 자격 증명 모음 이름           | contosovault                                 |
| `ClientId`     | Azure Active Directory 응용 프로그램 Id  | 627e911e-43cc-61d4-992e-12db9c81b413         |
| `ClientSecret` | Azure Active Directory 응용 프로그램 키 | g58K3dtg59o1Pa e59v2Tx829w6VxTB2yv9sv/101di + = |

[!code-csharp[Program](key-vault-configuration/samples/basic-sample/2.x/Program.cs?name=snippet1&highlight=2,7-10)]

## <a name="creating-key-vault-secrets-and-loading-configuration-values-basic-sample"></a>주요 자격 증명 모음 암호 만들기 및 구성 값 (basic 샘플)를 로드 합니다.
1. 키 자격 증명 모음 만들기 및 Azure Active Directory (Azure AD)의 지침에 따라 응용 프로그램에 대 한 설정 [Azure 키 자격 증명 모음 시작](https://azure.microsoft.com/documentation/articles/key-vault-get-started/)합니다.
  * 암호를 사용 하 여 주요 자격 증명 모음에 추가 된 [AzureRM 키 자격 증명 모음 PowerShell 모듈](/powershell/module/azurerm.keyvault) 에서 사용할 수는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureRM.KeyVault), [Azure 키 자격 증명 모음 REST API](/rest/api/keyvault/), 또는 [Azure 포털](https://portal.azure.com/)합니다. 비밀 하나로 만들어집니다 *수동* 또는 *인증서* 비밀 정보입니다. *인증서* 비밀 앱 및 서비스에서 사용 하기 위해 인증서가 있지만 구성 공급자에서 지원 되지 않습니다. 사용 해야는 *수동* 구성 공급자와 함께 사용 하기 위해 암호 이름-값 쌍을 만드는 옵션을 합니다.
    * 단순 암호 이름-값 쌍으로 생성 됩니다. Azure 키 자격 증명 모음 보안 이름은 영숫자, 대시로 제한 됩니다.
    * 계층 값 (구성 섹션) 사용 하 여 `--` (대시 두 개)이 샘플에서 구분 기호로 합니다. 하위 키에의 한 부분을 구분 하는 데 일반적으로 사용 되는 콜론 [ASP.NET Core 구성](xref:fundamentals/configuration), 비밀 이름에 사용할 수 없습니다. 따라서 대시 두 개 사용 되며 암호는 응용 프로그램의 구성에 로드 될 때 콜론으로 교체 됩니다.
    * 두 개 만든 *수동* 비밀 다음 이름-값 쌍을 포함 합니다. 첫 번째 암호는 단순한 이름 및 값, 및 두 번째 암호 섹션 및 비밀 이름에 하위 키와 비밀 값을 만듭니다.
      * `SecretName`: `secret_value_1`
      * `Section--SecretName`: `secret_value_2`
  * Azure Active Directory와 샘플 응용 프로그램을 등록 합니다.
  * 응용 프로그램에서 주요 자격 증명 모음에 액세스 권한을 부여 합니다. 사용 하는 경우는 `Set-AzureRmKeyVaultAccessPolicy` 앱 키 자격 증명 모음에 액세스할 수 권한을 부여 하는 PowerShell cmdlet을 제공 `List` 및 `Get` 와 비밀 정보에 대 한 액세스 `-PermissionsToKeys list,get`합니다.
2. 응용 프로그램의 업데이트 *appsettings.json* 파일의 값을 가진 `Vault`, `ClientId`, 및 `ClientSecret`합니다.
3. 구성 값을 가져오는 샘플 응용 프로그램을 실행 `IConfigurationRoot` 비밀 이름으로 같은 이름의 합니다.
  * 비 계층 값: 값에 대 한 `SecretName` 사용 하 여 얻은 `config["SecretName"]`합니다.
  * 계층 값 (섹션): 사용 `:` (콜론) 표기법 또는 `GetSection` 확장 메서드. 이러한 방법 중 하나를 사용 하 여 구성 값을 가져옵니다.
    * `config["Section:SecretName"]`
    * `config.GetSection("Section")["SecretName"]`

응용 프로그램을 실행 하면 웹 페이지 로드 된 비밀 값으로 표시 됩니다.

![브라우저 창에는 Azure 키 자격 증명 모음 구성 공급자를 통해 로드 하는 비밀 값 표시](key-vault-configuration/_static/sample1.png)

## <a name="creating-prefixed-key-vault-secrets-and-loading-configuration-values-key-name-prefix-sample"></a>접두사가 지정 된 키 자격 증명 모음 암호 만들기 및 구성 값 (키-이름-접두사-샘플)를 로드 합니다.
`AddAzureKeyVault`또한 구현을 허용 하는 오버 로드를 제공 `IKeyVaultSecretManager`, 구성 키로 변환 하는 주요 자격 증명 모음 암호를 제어할 수 있습니다. 예를 들어 앱을 시작할 때 제공 되는 접두사 값에 따라 보안 값을 로드 하는 인터페이스를 구현할 수 있습니다. 이렇게 하면 응용 프로그램의 버전에 따라 암호를 로드 예를 들어 있습니다.

> [!WARNING]
> 같은 키 자격 증명 모음에 여러 앱에 대 한 암호를 배치 하려면 또는 환경 비밀을 주요 자격 증명 모음 암호에 접두사를 사용 하지 않습니다 (예를 들어 *개발* verus *프로덕션* 비밀)를 동일한 자격 증명 모음입니다. 다른 앱과 개발/프로덕션 환경 가장 높은 보안 수준에 대 한 응용 프로그램 환경을 격리할 때 별도 주요 자격 증명 모음 사용 하는 것이 좋습니다.

두 번째 샘플 응용 프로그램을 사용 하면 만들 되는 암호에 대 한 주요 자격 증명 모음에 `5000-AppSecret` (마침표 비밀 키 자격 증명 모음 이름에 허용 되지) 5.0.0.0 응용 프로그램의 버전에 대 한 앱 암호를 나타내는입니다. 에 대 한 암호를 만들면 다른 버전의 경우, 5.1.0.0 `5100-AppSecret`합니다. 각 응용 프로그램 버전으로 해당 구성에는 자체 비밀 값 로드 `AppSecret`, 비밀 로드 된 버전에서 제거 합니다. 샘플의 구현은 다음과 같습니다.

[!code-csharp[Configuration builder](key-vault-configuration/samples/key-name-prefix-sample/2.x/Program.cs?name=snippet1&highlight=12)]

[!code-csharp[PrefixKeyVaultSecretManager](key-vault-configuration/samples/key-name-prefix-sample/2.x/Startup.cs?name=snippet1)]

`Load` 을 찾거나 버전 접두사가 있는 자격 증명 모음 암호를 반복 하는 공급자 알고리즘 메서드를 호출 합니다. 버전 접두사와 있을 때 `Load`, 알고리즘이 사용 하는 `GetKey` 메서드를 보안 이름의 구성 이름을 반환 합니다. 암호의 이름에서 버전 접두사를 제거 하 고 이름-값 쌍에 응용 프로그램의 구성에 한 로드에 대 한 비밀 이름의 나머지를 반환 합니다.

이 방법은 구현 하는 경우:

1. 주요 자격 증명 모음 암호 로드 됩니다.
2. 에 대 한 문자열 암호 `5000-AppSecret` 일치 합니다.
3. 버전, `5000` (대시로)는 하이픈이 제거 되어를 종료 하는 키 이름을 오프 `AppSecret` 비밀 값 응용 프로그램의 구성에 로드할 수 있습니다.

> [!NOTE]
> 제공할 수도 있습니다 직접 `KeyVaultClient` 구현 `AddAzureKeyVault`합니다. 사용자 지정 클라이언트를 제공 합니다. 구성 공급자와 응용 프로그램의 다른 파트 간의 클라이언트의 단일 인스턴스를 공유할 수 있습니다.

1. 키 자격 증명 모음 만들기 및 Azure Active Directory (Azure AD)의 지침에 따라 응용 프로그램에 대 한 설정 [Azure 키 자격 증명 모음 시작](https://azure.microsoft.com/documentation/articles/key-vault-get-started/)합니다.
  * 암호를 사용 하 여 주요 자격 증명 모음에 추가 된 [AzureRM 키 자격 증명 모음 PowerShell 모듈](/powershell/module/azurerm.keyvault) 에서 사용할 수는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureRM.KeyVault), [Azure 키 자격 증명 모음 REST API](/rest/api/keyvault/), 또는 [Azure 포털](https://portal.azure.com/)합니다. 비밀 하나로 만들어집니다 *수동* 또는 *인증서* 비밀 정보입니다. *인증서* 비밀 앱 및 서비스에서 사용 하기 위해 인증서가 있지만 구성 공급자에서 지원 되지 않습니다. 사용 해야는 *수동* 구성 공급자와 함께 사용 하기 위해 암호 이름-값 쌍을 만드는 옵션을 합니다.
    * 계층 값 (구성 섹션) 사용 하 여 `--` (대시 두 개)를 구분 합니다.
    * 두 개 만든 *수동* 다음 이름-값 쌍이 있는 비밀 정보:
      * `5000-AppSecret`: `5.0.0.0_secret_value`
      * `5100-AppSecret`: `5.1.0.0_secret_value`
  * Azure Active Directory와 샘플 응용 프로그램을 등록 합니다.
  * 응용 프로그램에서 주요 자격 증명 모음에 액세스 권한을 부여 합니다. 사용 하는 경우는 `Set-AzureRmKeyVaultAccessPolicy` 앱 키 자격 증명 모음에 액세스할 수 권한을 부여 하는 PowerShell cmdlet을 제공 `List` 및 `Get` 와 비밀 정보에 대 한 액세스 `-PermissionsToKeys list,get`합니다.
2. 응용 프로그램의 업데이트 *appsettings.json* 파일의 값을 가진 `Vault`, `ClientId`, 및 `ClientSecret`합니다.
3. 구성 값을 가져오는 샘플 응용 프로그램을 실행 `IConfigurationRoot` 접두사가 붙은 비밀 이름으로 같은 이름의 합니다. 이 샘플에서는 접두사는 응용 프로그램의 버전에 제공 된 `PrefixKeyVaultSecretManager` Azure 키 자격 증명 모음 구성 공급자를 추가 하는 경우. 에 대 한 값 `AppSecret` 사용 하 여 얻은 `config["AppSecret"]`합니다. 응용 프로그램에 의해 생성 된 웹 페이지 로드 된 값을 보여 줍니다.

   ![응용 프로그램의 버전을 5.0.0.0 Azure 키 자격 증명 모음 구성 공급자를 통해 로드 되는 브라우저 창에 비밀 값 표시](key-vault-configuration/_static/sample2-1.png)

4. 프로젝트 파일에서 응용 프로그램 어셈블리의 버전을 변경 `5.0.0.0` 를 `5.1.0.0` 응용 프로그램을 다시 실행 합니다. 이 이번에 반환 된 보안 값은 `5.1.0.0_secret_value`합니다. 응용 프로그램에 의해 생성 된 웹 페이지 로드 된 값을 보여 줍니다.

   ![응용 프로그램의 버전을 5.1.0.0 Azure 키 자격 증명 모음 구성 공급자를 통해 로드 되는 브라우저 창에 비밀 값 표시](key-vault-configuration/_static/sample2-2.png)

## <a name="controlling-access-to-the-clientsecret"></a>ClientSecret에 대 한 액세스를 제어합니다.
사용 하 여는 [암호 관리자 도구](xref:security/app-secrets) 유지 관리 하는 `ClientSecret` 프로젝트 소스 트리에 외부입니다. 암호 관리자와 앱 암호 특정 프로젝트와 연결 여러 프로젝트 간에 공유할 수 있습니다.

인증서를 지원 하는 환경에서.NET Framework 앱을 개발 하는 경우 X.509 인증서로 Azure 키 자격 증명 모음에 인증할 수 있습니다. X.509 인증서의 개인 키는 운영 체제에서 관리 됩니다. 자세한 내용은 참조 [클라이언트 암호 대신 인증서로 인증](https://docs.microsoft.com/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret)합니다. 사용 하 여는 `AddAzureKeyVault` 를 받아들이는 오버 로드는 `X509Certificate2`합니다.

```csharp
var store = new X509Store(StoreLocation.CurrentUser);
store.Open(OpenFlags.ReadOnly);
var cert = store.Certificates.Find(X509FindType.FindByThumbprint, config["CertificateThumbprint"], false);

builder.AddAzureKeyVault(
    config["Vault"],
    config["ClientId"],
    cert.OfType<X509Certificate2>().Single(),
    new EnvironmentSecretManager(env.ApplicationName));
store.Close();

Configuration = builder.Build();
```

## <a name="reloading-secrets"></a>암호를 다시 로드
될 때까지 캐시 된 비밀 `IConfigurationRoot.Reload()` 호출 됩니다. 만료를 사용할 수 없을 때까지 응용 프로그램 키 자격 증명 모음에 업데이트 된 비밀 정보를 준수 하지 않 및 `Reload` 실행 됩니다.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>사용 안 함 및 만료 된 암호
사용 안 함 및 만료 된 암호를 throw 한 `KeyVaultClientException`합니다. 응용 프로그램에서 throw를 방지 하려면 응용 프로그램을 교체 또는 사용 안 함/만료 된 암호를 업데이트 합니다.

## <a name="troubleshooting"></a>문제 해결
응용 프로그램에서 공급자를 사용 하 여 구성을 로드 하지 못하는에 오류 메시지가 기록 됩니다는 [ASP.NET 로깅 인프라](xref:fundamentals/logging)합니다. 다음과 같은 구성을 로드에서 하지 것입니다.
* 응용 프로그램은 Azure Active Directory에 올바르게 구성 되지 않았습니다.
* Azure 키 자격 증명 모음에 키 자격 증명 모음 존재 하지 않습니다.
* 응용 프로그램에 키 자격 증명 모음에 액세스할 권한이 부여 되지 않았습니다.
* 액세스 정책에 포함 되지 않으면 `Get` 및 `List` 사용 권한.
* 키 자격 증명 모음에 구성 데이터 (이름-값 쌍)는 이름이 잘못 지정, 누락 된 경우 비활성화 되었거나 만료 되었습니다.
* 응용 프로그램에 잘못 된 키 자격 증명 모음 이름 (`Vault`), Azure AD 응용 프로그램 Id (`ClientId`), 또는 Azure AD 키 (`ClientSecret`).
* Azure AD 키 (`ClientSecret`) 만료 되었습니다.
* 구성 키 (이름)을 로드 하려고 하는 값에 대 한 응용 프로그램에서 올바르지 않습니다.

## <a name="additional-resources"></a>추가 리소스
* <xref:fundamentals/configuration>
* [Microsoft Azure: 키 자격 증명 모음](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: 키 자격 증명 모음 설명서](https://docs.microsoft.com/azure/key-vault/)
* [Azure 키 자격 증명 모음에 대 한 키 생성 및 HSM 보호 된 전송 하는 방법](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient 클래스](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
