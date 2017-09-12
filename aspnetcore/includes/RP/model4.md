<span data-ttu-id="08eab-101">다음 표에서는 ASP.NET Core 코드 생성기의 매개 변수를 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="08eab-101">The following table details the ASP.NET Core code generators\` parameters:</span></span>

| <span data-ttu-id="08eab-102">매개 변수</span><span class="sxs-lookup"><span data-stu-id="08eab-102">Parameter</span></span>               | <span data-ttu-id="08eab-103">설명</span><span class="sxs-lookup"><span data-stu-id="08eab-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="08eab-104">-m</span><span class="sxs-lookup"><span data-stu-id="08eab-104">-m</span></span>  | <span data-ttu-id="08eab-105">모델의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="08eab-105">The name of the model.</span></span> |
| <span data-ttu-id="08eab-106">-dc</span><span class="sxs-lookup"><span data-stu-id="08eab-106">-dc</span></span>  | <span data-ttu-id="08eab-107">데이터 컨텍스트입니다.</span><span class="sxs-lookup"><span data-stu-id="08eab-107">The data context.</span></span> |
| <span data-ttu-id="08eab-108">-udl</span><span class="sxs-lookup"><span data-stu-id="08eab-108">-udl</span></span> | <span data-ttu-id="08eab-109">기본 레이아웃을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="08eab-109">Use the default layout.</span></span> |
| <span data-ttu-id="08eab-110">-outDir</span><span class="sxs-lookup"><span data-stu-id="08eab-110">-outDir</span></span> | <span data-ttu-id="08eab-111">뷰를 만들기 위한 상태 출력 폴더 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="08eab-111">The relative output folder path to create the views.</span></span> |
| <span data-ttu-id="08eab-112">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="08eab-112">--referenceScriptLibraries</span></span> | <span data-ttu-id="08eab-113">편집 및 만들기 페이지에 `_ValidationScriptsPartial`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="08eab-113">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="08eab-114">`h` 스위치를 사용하여 `aspnet-codegenerator razorpage` 명령에 대한 도움말을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="08eab-114">Use the `h` switch to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```console
dotnet aspnet-codegenerator razorpage -h
```
<a name="test"></a>
### <a name="test-the-app"></a><span data-ttu-id="08eab-115">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="08eab-115">Test the app</span></span>

* <span data-ttu-id="08eab-116">앱을 실행하고 브라우저에서 `/Movies`를 URL에 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="08eab-116">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>
* <span data-ttu-id="08eab-117">**만들기** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="08eab-117">Test the **Create** link.</span></span>

 ![페이지 만들기](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* <span data-ttu-id="08eab-119">**편집**, **세부 정보** 및 **삭제** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="08eab-119">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="08eab-120">다음 오류가 표시되면 마이그레이션을 실행했고 데이터베이스를 업데이트했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="08eab-120">If you get the following error, verify you have run migrations and updated the database:</span></span>

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)
```