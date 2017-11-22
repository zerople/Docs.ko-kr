<span data-ttu-id="2c814-101"><a name="codegenerator"></a> 다음 표에서는 ASP.NET Core 코드 생성기의 매개 변수를 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2c814-101"><a name="codegenerator"></a> The following table details the ASP.NET Core code generators\` parameters:</span></span>

| <span data-ttu-id="2c814-102">매개 변수</span><span class="sxs-lookup"><span data-stu-id="2c814-102">Parameter</span></span>               | <span data-ttu-id="2c814-103">설명</span><span class="sxs-lookup"><span data-stu-id="2c814-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="2c814-104">-m</span><span class="sxs-lookup"><span data-stu-id="2c814-104">-m</span></span>  | <span data-ttu-id="2c814-105">모델의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="2c814-105">The name of the model.</span></span> |
| <span data-ttu-id="2c814-106">-dc</span><span class="sxs-lookup"><span data-stu-id="2c814-106">-dc</span></span>  | <span data-ttu-id="2c814-107">데이터 컨텍스트입니다.</span><span class="sxs-lookup"><span data-stu-id="2c814-107">The data context.</span></span> |
| <span data-ttu-id="2c814-108">-udl</span><span class="sxs-lookup"><span data-stu-id="2c814-108">-udl</span></span> | <span data-ttu-id="2c814-109">기본 레이아웃을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2c814-109">Use the default layout.</span></span> |
| <span data-ttu-id="2c814-110">-outDir</span><span class="sxs-lookup"><span data-stu-id="2c814-110">-outDir</span></span> | <span data-ttu-id="2c814-111">뷰를 만들기 위한 상태 출력 폴더 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="2c814-111">The relative output folder path to create the views.</span></span> |
| <span data-ttu-id="2c814-112">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="2c814-112">--referenceScriptLibraries</span></span> | <span data-ttu-id="2c814-113">편집 및 만들기 페이지에 `_ValidationScriptsPartial`을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2c814-113">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="2c814-114">`h` 스위치를 사용하여 `aspnet-codegenerator razorpage` 명령에 대한 도움말을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2c814-114">Use the `h` switch to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```console
dotnet aspnet-codegenerator razorpage -h
```
