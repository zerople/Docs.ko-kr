# <a name="prefix-key-vault-configuration-provider-sample-application-aspnet-core-1x"></a>키 자격 증명 모음 구성 공급자 샘플 응용 프로그램을 접두사 (ASP.NET Core 1.x)

이 예제에서는 ASP.NET Core에 대 한 Azure 키 자격 증명 모음 구성 공급자를 사용 1.x 키 이름 접두사를 사용 하 여 합니다. ASP.NET Core 2.x 샘플을 보려면 [접두사 키 자격 증명 모음 구성 공급자 샘플 응용 프로그램 (ASP.NET Core 2.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/samples/key-name-prefix-sample/2.x)합니다.

> [!NOTE]
> 구성 공급자 ASP.NET Core 1.0에 대 한 사용할 수 없습니다. 구성 공급자를 구현 하 고 앱이 ASP.NET Core 1.0 응용 프로그램 경우 1.1 이상 첫 응용 프로그램을 업그레이드 합니다.

이 샘플의 작동 방법에 대 한 자세한 내용은 참조는 [Azure 키 자격 증명 모음 구성 공급자](xref:security/key-vault-configuration) 항목입니다.

## <a name="using-the-sample"></a>샘플 사용
1. 키 자격 증명 모음 만들기 및 Azure Active Directory (Azure AD)의 지침에 따라 응용 프로그램에 대 한 설정 [Azure 키 자격 증명 모음 시작](https://azure.microsoft.com/documentation/articles/key-vault-get-started/)합니다.
  * Azure PowerShell 모듈, Azure 관리 API 또는 Azure 포털을 사용 하 여 주요 자격 증명 모음에 암호를 추가 합니다. 비밀 하나로 만들어집니다 *수동* 또는 *인증서* 비밀 정보입니다. *인증서* 비밀 앱 및 서비스에서 사용 하기 위해 인증서가 있지만 구성 공급자에서 지원 되지 않습니다. 사용 해야는 *수동* 구성 공급자와 함께 사용 하기 위해 암호 이름-값 쌍을 만드는 옵션을 합니다.
    * 계층 값 (구성 섹션) 사용 하 여 `--` (대시 두 개)를 구분 합니다.
    * 샘플 응용 프로그램에 대 한 두 개를 만든 *수동* 다음 이름-값 쌍이 있는 비밀 정보:
      * `5000-AppSecret`: `5.0.0.0_secret_value`
      * `5100-AppSecret`: `5.1.0.0_secret_value`
  * Azure Active Directory와 샘플 응용 프로그램을 등록 합니다.
  * 응용 프로그램에서 주요 자격 증명 모음에 액세스 권한을 부여 합니다. 사용 하는 경우는 `Set-AzureRmKeyVaultAccessPolicy` 앱 키 자격 증명 모음에 액세스할 수 권한을 부여 하는 PowerShell cmdlet을 제공 `List` 및 `Get` 와 비밀 정보에 대 한 액세스 `-PermissionsToKeys list,get`합니다.
2. 응용 프로그램의 업데이트 *appsettings.json* 파일의 값을 가진 `Vault`, `ClientId`, 및 `ClientSecret`합니다.
3. 구성 값을 가져오는 샘플 응용 프로그램을 실행 `IConfigurationRoot` 접두사가 붙은 비밀 이름으로 같은 이름의 합니다. 이 샘플에서는 접두사는 응용 프로그램의 버전에 제공 된 `PrefixKeyVaultSecretManager` Azure 키 자격 증명 모음 구성 공급자를 추가 하는 경우. 에 대 한 값 `AppSecret` 사용 하 여 얻은 `config["AppSecret"]`합니다.
4. 프로젝트 파일에서 응용 프로그램 어셈블리의 버전을 변경 `5.0.0.0` 를 `5.1.0.0` 응용 프로그램을 다시 실행 합니다. 이 이번에 반환 된 보안 값은 `5.1.0.0_secret_value`합니다.
