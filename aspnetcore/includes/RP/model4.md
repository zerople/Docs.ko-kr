<span data-ttu-id="aa767-101">다음 표에서는 ASP.NET Core 코드 생성기의 매개 변수를 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="aa767-101">The following table details the ASP.NET Core code generators\` parameters:</span></span>

| <span data-ttu-id="aa767-102">매개 변수</span><span class="sxs-lookup"><span data-stu-id="aa767-102">Parameter</span></span>               | <span data-ttu-id="aa767-103">설명</span><span class="sxs-lookup"><span data-stu-id="aa767-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="aa767-104">-m</span><span class="sxs-lookup"><span data-stu-id="aa767-104">-m</span></span>  | <span data-ttu-id="aa767-105">모델의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="aa767-105">The name of the model.</span></span> |
| <span data-ttu-id="aa767-106">-dc</span><span class="sxs-lookup"><span data-stu-id="aa767-106">-dc</span></span>  | <span data-ttu-id="aa767-107">데이터 컨텍스트입니다.</span><span class="sxs-lookup"><span data-stu-id="aa767-107">The data context.</span></span> |
| <span data-ttu-id="aa767-108">-udl</span><span class="sxs-lookup"><span data-stu-id="aa767-108">-udl</span></span> | <span data-ttu-id="aa767-109">기본 레이아웃을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="aa767-109">Use the default layout.</span></span> |
| <span data-ttu-id="aa767-110">-outDir</span><span class="sxs-lookup"><span data-stu-id="aa767-110">-outDir</span></span> | <span data-ttu-id="aa767-111">뷰를 만들기 위한 상태 출력 폴더 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="aa767-111">The relative output folder path to create the views.</span></span> |
| <span data-ttu-id="aa767-112">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="aa767-112">--referenceScriptLibraries</span></span> | <span data-ttu-id="aa767-113">편집 및 만들기 페이지에 `_ValidationScriptsPartial`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="aa767-113">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="aa767-114">`h` 스위치를 사용하여 `aspnet-codegenerator razorpage` 명령에 대한 도움말을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aa767-114">Use the `h` switch to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```console
dotnet aspnet-codegenerator razorpage -h
```
<a name="test"></a>
### <a name="test-the-app"></a><span data-ttu-id="aa767-115">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="aa767-115">Test the app</span></span>

* <span data-ttu-id="aa767-116">앱을 실행하고 브라우저에서 `/Movies`를 URL에 추가합니다(`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="aa767-116">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>
* <span data-ttu-id="aa767-117">**만들기** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="aa767-117">Test the **Create** link.</span></span>

 ![페이지 만들기](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* <span data-ttu-id="aa767-119">**편집**, **세부 정보** 및 **삭제** 링크를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="aa767-119">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="aa767-120">다음과 비슷한 오류가 표시되면 마이그레이션을 실행했는지와 데이터베이스를 업데이트했는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aa767-120">If you get the error similar to the following, verify you have run migrations and updated the database:</span></span>

```
An unhandled exception occurred while processing the request.
'no such table: Movie'.
```
