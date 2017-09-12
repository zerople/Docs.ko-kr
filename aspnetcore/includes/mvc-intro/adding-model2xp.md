<a name="cli"></a>
## <a name="perform-initial-migration"></a><span data-ttu-id="4df0e-101">초기 마이그레이션 수행</span><span class="sxs-lookup"><span data-stu-id="4df0e-101">Perform initial migration</span></span>

<span data-ttu-id="4df0e-102">명령줄에서 다음 .NET Core CLI 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4df0e-102">From the command line, run the following .NET Core CLI commands:</span></span>

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

<span data-ttu-id="4df0e-103">`dotnet ef migrations add InitialCreate` 명령은 초기 데이터베이스 스키마를 만드는 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="4df0e-103">The `dotnet ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="4df0e-104">스키마는 `DbContext`에 지정된 모델을 기반으로 합니다(*Models/MovieContext.cs* 파일에서).</span><span class="sxs-lookup"><span data-stu-id="4df0e-104">The schema is based on the model specified in the `DbContext` (In the *Models/MovieContext.cs* file).</span></span> <span data-ttu-id="4df0e-105">`Initial` 인수는 마이그레이션 이름을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="4df0e-105">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="4df0e-106">모든 이름을 사용할 수 있지만 일반적으로 마이그레이션을 설명하는 이름을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="4df0e-106">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="4df0e-107">자세한 내용은 [마이그레이션 소개](xref:data/ef-mvc/migrations#introduction-to-migrations)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4df0e-107">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="4df0e-108">`dotnet ef database update` 명령은 데이터베이스를 만드는 *Migrations/\<time-stamp>_InitialCreate.cs* 파일에서 `Up` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4df0e-108">The `dotnet ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>