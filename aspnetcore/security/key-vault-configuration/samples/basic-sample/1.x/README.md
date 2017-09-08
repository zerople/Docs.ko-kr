# <a name="key-vault-configuration-provider-sample-application-aspnet-core-1x"></a>키 자격 증명 모음 구성 공급자 샘플 응용 프로그램 (ASP.NET Core 1.x)

이 예제에서는 ASP.NET Core에 대 한 Azure 키 자격 증명 모음 구성 공급자를 사용 1.x 합니다. ASP.NET Core 2.x 샘플을 보려면 [샘플 응용 프로그램 키 자격 증명 모음 구성 공급자 (ASP.NET Core 2.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/key-vault-configuration/samples/basic-sample/2.x)합니다.

> [!NOTE]
> 구성 공급자 ASP.NET Core 1.0에 대 한 사용할 수 없습니다. 구성 공급자를 구현 하 고 앱이 ASP.NET Core 1.0 응용 프로그램 경우 1.1 이상 첫 응용 프로그램을 업그레이드 합니다.

이 샘플의 작동 방법에 대 한 자세한 내용은 참조는 [Azure 키 자격 증명 모음 구성 공급자](xref:security/key-vault-configuration) 항목입니다.

## <a name="using-the-sample"></a>샘플 사용
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
