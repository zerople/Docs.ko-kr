---
title: "Azure 키 자격 증명 모음 구성 공급자 | Microsoft 문서"
author: guardrex
description: "Azure 키 자격 증명 모음 구성 공급자를 사용 하 여 런타임에 로드 하는 이름-값 쌍을 사용 하 여 응용 프로그램을 구성 하는 방법."
keywords: "ASP.NET Core, 구성, Azure 키 자격 증명 모음"
ms.author: riande
manager: wpickett
ms.date: 02/11/2017
ms.topic: article
ms.assetid: 0292bdae-b3ed-4637-bd67-19b9bb8b65cb
ms.prod: asp.net-core
uid: security/key-vault-configuration
translationtype: Machine Translation
ms.sourcegitcommit: bff0c6a3f481fa4c6ec73c98d5048fb82e4eacc3
ms.openlocfilehash: 321fbeb80efd18c09d86a3aa98aa2f30f9e97976
ms.lasthandoff: 03/23/2017

---
# <a name="azure-key-vault-configuration-provider"></a>Azure 키 자격 증명 모음 구성 공급자

여 [Luke Latham](https://github.com/GuardRex) 및 [Andrew Stanton 간호사](https://github.com/anurse)

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/sample)

이 문서에서는 사용 하는 방법에 설명 된 [Microsoft Azure 키 자격 증명 모음](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 Azure 키 자격 증명 모음에서 응용 프로그램 구성 값을 로드 합니다. Azure 키 자격 증명 모음에는 암호화 키 및 응용 프로그램 및 서비스에서 사용 하는 비밀을 보호 하는 데 도움이 되는 클라우드 기반 서비스입니다. 일반적인 시나리오는 중요 한 구성 데이터에 대 한 액세스 제어를 포함 하 고 FIPS 140-2에 대 한 요구 사항이 충족 Level 2 유효성 검사가 하드웨어 보안 모듈 (HSM의) 구성 데이터를 저장할 때.

## <a name="package"></a>패키지
공급자를 사용 하려면 추가에 대 한 참조는 [ `Microsoft.Extensions.Configuration.AzureKeyVault` ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 패키지 합니다. 공급자는.NET Framework 4.5.1 또는 1.5 이상이.NET 표준에 따라 다릅니다. 이 기능은 ASP.NET 핵심 1.1.0을 대상으로 하는 경우에 사용할 수는 있습니다.

## <a name="application-configuration"></a>응용 프로그램 구성
사용 하 여 공급자를 탐색할 수는 [샘플 응용 프로그램](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/sample)합니다. 키 자격 증명 모음을 설정 하 고 하 여 자격 증명 모음에 암호 쌍을 만들 되 면 [아래 지침을 따라](#creating-key-vault-secrets-and-loading-configuration-values), 샘플 응용 프로그램에서 안전 하 게 비밀 값을 해당 구성에 로드 하 고 웹 페이지에 표시 합니다.

공급자에 추가 되는 `ConfigurationBuilder` 와 `AddAzureKeyVault` 확장 합니다. 확장 샘플 응용 프로그램에서 세 가지 구성 값에서 로드를 사용 하 여 *appsettings.json* 파일입니다.

앱 설정 | 설명 | 예제
--- | --- | ---
`Vault` | Azure 키 자격 증명 모음 이름 | contosovault
`ClientId` | Azure Active Directory 응용 프로그램 Id | 627e911e-43cc-61d4-992e-12db9c81b413
`ClientSecret` | Azure Active Directory 응용 프로그램 키 | g58K3dtg59o1Pa e59v2Tx829w6VxTB2yv9sv/101di + =

[!code-csharp[주](key-vault-configuration/sample/Startup.cs?name=snippet1&highlight=5,10-13)]

`AddAzureKeyVault`또한의 구현을 허용 하는 오버 로드를 제공 `IKeyVaultSecretManager`, 구성 키로 변환 됩니다 주요 자격 증명 모음 암호를 제어할 수 있습니다. 예를 들어 구성 비밀 키 자격 증명 모음에 저장 한에 응용 프로그램 이름 앞에 있는 응용 프로그램에서 구성 값을 로드 하는 인터페이스를 구현할 수 있습니다. 이 옵션을 사용 하면 하나의 키 자격 증명 모음에 여러 앱에 대 한 비밀을 유지 관리할 수 있습니다.

여러 있다고 가정 `ConnectionString` 접두사가 응용 프로그램 이름으로 자격 증명 모음 암호 키입니다. 샘플 응용 프로그램에 대 한 키에 대 한 자격 증명 모음에서 암호를 만듭니다 `KeyVaultConfigProviderSample-ConnectionString` 및 해당 값입니다. 두 번째 응용 프로그램에 대 한 암호를 만들어 `SomeOtherApplicationName-ConnectionString` 및 해당 값입니다. 원하는 각 응용 프로그램을 로드 하는 자체 `ConnectionString` 으로 해당 구성에 비밀 `ConnectionString`합니다. 이 구현의 예는 다음과 같습니다.

```csharp
public class EnvironmentSecretManager : IKeyVaultSecretManager
{
    private readonly string _appNamePrefix;

    public EnvironmentSecretManager(string appName)
    {
        _appNamePrefix = appName + "-";
    }

    public bool Load(SecretItem secret)
    {
        return secret.Identifier.Name.StartsWith(_appNamePrefix);
    }

    public string GetKey(SecretBundle secret)
    {
        return secret.SecretIdentifier.Name.Substring(_appNamePrefix.Length);
    }
}
```

```csharp
builder.AddAzureKeyVault(
    $"https://{config["Vault"]}.vault.azure.net/",
    config["ClientId"],
    config["ClientSecret"],
    new EnvironmentSecretManager(env.ApplicationName));
    
Configuration = builder.Build();
```

`Load` 비밀의 이름에 접두사와 응용 프로그램 이름이 일치 하는 것을 찾으려고 비밀을 통해 반복 하는 공급자 알고리즘에서 메서드를 호출 합니다. 와 일치 하는 항목이 발견 되는 경우 `Load`, 알고리즘이 사용 하는 `GetKey` 메서드를 보안 이름의 구성 이름을 반환 합니다. 암호의 이름에서 앱 이름 접두사를 제거 하 고 이름을 반환 하는 로드에 대 한 응용 프로그램의 구성에 이름-값 쌍입니다.

이 방법을 사용 합니다 구현 하는 경우:

1. 키 자격 증명 모음 암호 로드 됩니다.
2. 에 대 한 문자열 암호 `KeyVaultConfigProviderSample-ConnectionString` 일치 합니다.
3. 응용 프로그램 이름을 `KeyVaultConfigProviderSample` (대시)를 제거 하 고 로드 하는 데 사용 됩니다 `ConnectionString` 응용 프로그램의 구성에 해당 값입니다.

제공할 수도 있습니다 고유한 `KeyVaultClient` 구현을 `AddAzureKeyVault`합니다. 사용자 지정 클라이언트를 제공 합니다. 구성 공급자 및 응용 프로그램의 다른 부분 간에 클라이언트의 단일 인스턴스를 공유할 수 있습니다.

## <a name="controlling-access-to-the-clientsecret"></a>ClientSecret에 대 한 액세스를 제어합니다.
사용 하는 [암호 관리자 도구](xref:security/app-secrets) 유지 관리 하는 `ClientSecret` 프로젝트 소스 트리에 외부에서. 암호 관리자와 응용 프로그램 암호 특정 프로젝트와 연결 여러 프로젝트에서 공유할 합니다.

인증서를 지 원하는 환경에서.NET Framework 앱을 개발 하는 경우 X.509 인증서와 함께 Azure 키 자격 증명 모음에 인증할 수 있습니다. X.509 인증서의 개인 키 OS에 의해 관리 됩니다. 자세한 내용은 참조 [클라이언트 암호 대신 인증서로 인증](https://docs.microsoft.com/azure/key-vault/key-vault-use-from-web-application#authenticate-with-a-certificate-instead-of-a-client-secret)합니다. 사용 된 `AddAzureKeyVault` 를 받아들이는 오버 로드는 `X509Certificate2`합니다.

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

## <a name="creating-key-vault-secrets-and-loading-configuration-values"></a>비밀 키 자격 증명 모음 만들기 및 구성 값을 로드 합니다.
1. 키 자격 증명 모음을 만들고 Azure Active Directory (Azure AD)의 지침에 따라 응용 프로그램에 대 한 설정 [Azure 키 자격 증명 모음 시작](https://azure.microsoft.com/documentation/articles/key-vault-get-started/)합니다.
  * Azure PowerShell 모듈, Azure 관리 API 또는 Azure 포털을 사용 하 여 키 자격 증명 모음에 암호를 추가 합니다. 기밀 정보로 업로드할지 *수동* 또는 *인증서* 암호입니다. *인증서* 비밀 앱 및 서비스에서 사용할 인증서가 있지만 구성 공급자에서 지원 되지 않습니다. 이 방법을 사용할지는 *수동* 구성 공급자와 함께 사용 하기 위해 이름-값 쌍 암호를 만드는 옵션을 합니다.
    * 계층 값 (구성 섹션)를 사용 하 여 `--` (두 개의 대시)를 구분 합니다.
    * 샘플 응용 프로그램에 대 한 두 개를 만들어 *수동* 다음 이름-값 쌍으로 비밀 정보:
      * `MySecret`: `secret_value_1`
      * `Section--MySecret`: `secret_value_2`
  * Azure Active Directory와 샘플 앱을 등록 합니다.
  * 앱 키 자격 증명 모음에 액세스할 수 있는 권한을 부여 합니다. 사용 하는 경우는 `Set-AzureRmKeyVaultAccessPolicy` 앱 키 자격 증명 모음에 액세스할 수 있는 권한을 부여 하는 PowerShell cmdlet을 제공 `List` 및 `Get` 으로 비밀 정보에 대 한 액세스 `-PermissionsToKeys list,get`합니다.
2. 응용 프로그램의 업데이트 *appsettings.json* 파일의 값으로 `Vault`, `ClientId`, 및 `ClientSecret`합니다.
3. 구성 값을 가져오는 샘플 응용 프로그램을 실행 `IConfigurationRoot` 비밀 이름과 동일한 이름을 사용 합니다.
  * 비 계층적 값: 값에 대 한 `MySecret` 사용 하 여 가져오는 `config["MySecret"]`합니다.
  * 계층 값 (섹션): 사용 하 여 `:` (콜론) 표기법 또는 `GetSection` 확장 메서드.
    * `config["Section:MySecret"]`
    * `config.GetSection("Section")["MySecret"]`

![Azure 키 자격 증명 모음 구성 공급자를 통해 로드 하는 비밀 값을 표시 된 브라우저 창](key-vault-configuration/_static/sample-output.png)

## <a name="reloading-secrets"></a>암호를 다시 로드
비밀 될 때까지 캐시 됩니다 `IConfigurationRoot.Reload()` 호출 됩니다. 사용 안 함, 만료 및 될 때까지 응용 프로그램에 의해 업데이트 된 암호 키 자격 증명 모음에 준수 하지 않을 `Reload` 실행 됩니다.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>사용 안 함 및 만료 된 암호
사용 안 함 및 만료 된 암호를 throw 한 `KeyVaultClientException`합니다. 응용 프로그램에서 throw를 방지 하려면 응용 프로그램 바꾸거나 사용 안 함/만료 된 암호를 업데이트 합니다.

## <a name="troubleshooting"></a>문제 해결
공급자를 사용 하는 구성을 로드 실패 하면 응용 프로그램에 오류 메시지가 기록 됩니다는 [ASP.NET 로깅 인프라](xref:fundamentals/logging)합니다. 다음과 같은 구성을 로드에서 하지 것입니다.
* 응용 프로그램은 Azure Active Directory에 올바르게 구성 되지 않았습니다.
* Azure 키 자격 증명 모음에 키 자격 증명 모음 존재 하지 않습니다.
* 앱 키 자격 증명 모음에 액세스할 권한이 없는 합니다.
* 액세스 정책을 포함 하지 않습니다 `Get` 및 `List` 사용 권한입니다.
* 키 자격 증명 모음에 구성 데이터 (이름-값 쌍)가 이름이 잘못 지정, 누락, 비활성화 되었거나 만료 되었습니다.
* 앱에 잘못 된 키 자격 증명 모음 이름 (`Vault`), Azure AD 응용 프로그램 Id (`ClientId`), 또는 Azure AD 키 (`ClientSecret`).
* Azure AD 키 (`ClientSecret`)가 만료 되었습니다.
* 구성 키 (이름)를 로드 하려고 하는 값에 대 한 응용 프로그램에서 올바르지 않습니다.

`System.TypeLoadException`다음을 사용 하 여 시작 하는 동안 예외가 `.NET 4.6.x`:
* NuGet 패키지에 대 한 참조를 추가 `System.Net.Http` 4.3.1 버전 이상.

## <a name="additional-resources"></a>추가 리소스
* <xref:fundamentals onfiguration=""></xref:fundamentals>
* [: Microsoft Azure 키 자격 증명 모음](https://azure.microsoft.com/services/key-vault/)
* [: Microsoft Azure 키 자격 증명 모음 설명서](https://docs.microsoft.com/azure/key-vault/)
* [Azure 키 자격 증명 모음에 대 한 키 생성 및 HSM 보호 된 전송 하는 방법](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient 클래스](https://msdn.microsoft.com/library/microsoft.azure.keyvault.keyvaultclient.aspx)

