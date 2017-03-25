# <a name="key-vault-configuration-provider-sample-application"></a>키 자격 증명 모음 구성 공급자 샘플 응용 프로그램

이 샘플에서는 Azure 키 자격 증명 모음 구성 공급자를 사용 합니다.

## <a name="using-the-sample"></a>샘플 사용
1. 지침에 따라 [Azure 키 자격 증명 모음 시작](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-get-started):
  * 키 자격 증명 모음 만들기
  * 키 자격 증명 모음 암호 만들기
    - `MySecret`: `secret_value_1`
    - `Section--MySecret`: `secret_value_2`
  * Azure Active Directory에 응용 프로그램 등록
  * 암호를 사용 하 여 응용 프로그램
2. 구성 데이터를 제공 된 `appsettings.json` 샘플의 파일:
  * `Vault`: Azure 키 자격 증명 모음 이름
  * `ClientId`: Azure AD 응용 프로그램 Id
  * `ClientSecret`: Azure AD 응용 프로그램 키
3. 복원, 빌드 및 응용 프로그램 실행
