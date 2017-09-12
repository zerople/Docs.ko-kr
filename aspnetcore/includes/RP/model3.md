<a name="cli"></a>
## <a name="add-scaffold-tooling-and-perform-initial-migration"></a>스캐폴드 도구 추가 및 초기 마이그레이션 수행

명령줄에서 다음 .NET Core CLI 명령을 실행합니다.

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet ef migrations add InitialCreate
dotnet ef database update
```

`add package` 명령은 스캐폴딩 엔진을 실행하는 데 필요한 도구를 설치합니다.

`ef migrations add InitialCreate` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다. 스키마는 `DbContext`에 지정된 모델을 기반으로 합니다(*Models/MovieContext.cs* 파일에서). `Initial` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다. 모든 이름을 사용할 수 있지만 일반적으로 마이그레이션을 설명하는 이름을 선택합니다. 자세한 내용은 [마이그레이션 소개](xref:data/ef-mvc/migrations#introduction-to-migrations)를 참조하세요.

`ef database update` 명령은 데이터베이스를 만드는 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.