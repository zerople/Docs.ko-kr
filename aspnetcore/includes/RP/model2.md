<span data-ttu-id="6a213-101">`Movie` 클래스에 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6a213-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieNoEF.cs?name=snippet_MovieNoEF)]

<span data-ttu-id="6a213-102">`ID` 필드는 데이터베이스에서 기본 키 대신 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="6a213-102">The `ID` field is required by the database for the primary key.</span></span>

<a name="dc"></a>
### <a name="add-a-database-context-class"></a><span data-ttu-id="6a213-103">데이터베이스 컨텍스트 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="6a213-103">Add a database context class</span></span>

<span data-ttu-id="6a213-104">*MovieContext.cs*라는 `DbContext` 파생 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6a213-104">Add a `DbContext` derived class named *MovieContext.cs* to the *Models* folder.</span></span>

[!code-csharp[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Models/MovieContext.cs?range=1-12,14-17,19-21)]

<span data-ttu-id="6a213-105">이전 코드에서는 엔터티 집합에 대한 `DbSet` 속성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6a213-105">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="6a213-106">Entity Framework 용어에서 엔터티 집합은 일반적으로 데이터베이스 테이블에 해당하고 엔터티는 테이블의 행에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="6a213-106">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="6a213-107">`DbSet` 속성 이름은 `Movies`입니다.</span><span class="sxs-lookup"><span data-stu-id="6a213-107">The `DbSet` property name is `Movies`.</span></span> <span data-ttu-id="6a213-108">데이터베이스는 단수 이름을 사용하므로 샘플은 `OnModelCreating`을 재정의하여 테이블 이름에 대해 단수 형식(`Movie`)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6a213-108">Since the database uses singular names, the sample overrides `OnModelCreating` to use the singular form (`Movie`) for the table name.</span></span>
