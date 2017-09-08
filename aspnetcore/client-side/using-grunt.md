---
title: "ASP.NET Core에서 Grunt를 사용 하 여"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 471112e9-2c33-454b-96fc-32916102ce73
ms.technology: aspnet
ms.prod: asp.net-core
uid: client-side/using-grunt
ms.openlocfilehash: df20c3a31fce81ab039ef2f63bf38ed9943c7c6c
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2017
---
# <a name="using-grunt-in-aspnet-core"></a><span data-ttu-id="09e5d-103">ASP.NET Core에서 Grunt를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="09e5d-103">Using Grunt in ASP.NET Core</span></span> 

<span data-ttu-id="09e5d-104">으로 [Noel 밥](http://blog.falafel.com/author/noel-rice/)</span><span class="sxs-lookup"><span data-stu-id="09e5d-104">By [Noel Rice](http://blog.falafel.com/author/noel-rice/)</span></span>

<span data-ttu-id="09e5d-105">Grunt은 스크립트 축소, TypeScript 컴파일, 코드 품질 "중요" 도구, CSS 사전 프로세서 및 클라이언트 개발을 지원 하기 위해 수행 하는 모든 반복적인 작업에 대 한 작업을 자동화 하는 JavaScript task runner입니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-105">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="09e5d-106">ASP.NET 프로젝트 템플릿에서 기본적으로 Gulp를 사용 하는 경우 Visual Studio에서 grunt 완전히 지원 됩니다 (참조 [Gulp를 사용 하 여](using-gulp.md)).</span><span class="sxs-lookup"><span data-stu-id="09e5d-106">Grunt is fully supported in Visual Studio, though the ASP.NET project templates use Gulp by default (see [Using Gulp](using-gulp.md)).</span></span>

<span data-ttu-id="09e5d-107">이 예제를 기준으로 빈 ASP.NET Core 프로젝트를 사용 하 여 처음부터 클라이언트 빌드 프로세스를 자동화 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-107">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="09e5d-108">완성 된 예제 대상 배포 디렉터리를 정리, JavaScript 파일을 결합, 코드 품질을 확인, JavaScript 파일 콘텐츠를 압축 및 웹 응용 프로그램의 루트에 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-108">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="09e5d-109">다음 패키지를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-109">We will use the following packages:</span></span>

* <span data-ttu-id="09e5d-110">**grunt**: The Grunt 작업 runner 패키지 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-110">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="09e5d-111">**grunt contrib 정리**: 파일 또는 디렉터리를 제거 하는 플러그 인 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-111">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="09e5d-112">**grunt-contrib-jshint**: JavaScript 코드의 품질을 검토 하는 플러그 인 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-112">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="09e5d-113">**grunt-contrib-concat**: 단일 파일로 파일을 연결 하는 플러그 인 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-113">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="09e5d-114">**grunt contrib uglify**: 크기를 줄이는 JavaScript 축소 하는 플러그 인 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-114">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="09e5d-115">**grunt contrib 감시**: 파일 작업을 감시 하는 플러그 인 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-115">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="09e5d-116">응용 프로그램 준비</span><span class="sxs-lookup"><span data-stu-id="09e5d-116">Preparing the application</span></span>

<span data-ttu-id="09e5d-117">를 시작 하려면 새 빈 웹 응용 프로그램을 설정 하 고 TypeScript 예제 파일을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-117">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="09e5d-118">TypeScript 파일 자동으로 기본 Visual Studio 설정을 사용 하 여 JavaScript로 컴파일되는 및 우리의 원자재 Grunt를 사용 하 여 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-118">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1.  <span data-ttu-id="09e5d-119">Visual Studio에서 만드는 새 `ASP.NET Web Application`합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-119">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2.  <span data-ttu-id="09e5d-120">에 **새 ASP.NET 프로젝트** 대화 상자에서 ASP.NET Core 선택 **빈** 템플릿을 확인 단추를 클릭 하 고 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-120">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3.  <span data-ttu-id="09e5d-121">솔루션 탐색기에서 프로젝트 구조를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-121">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="09e5d-122">`\src` 폴더에 빈 포함 `wwwroot` 및 `Dependencies` 노드.</span><span class="sxs-lookup"><span data-stu-id="09e5d-122">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![빈 웹 솔루션](using-grunt/_static/grunt-solution-explorer.png)

4.  <span data-ttu-id="09e5d-124">라는 새 폴더 추가 `TypeScript` 프로젝트 디렉터리에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-124">Add a new folder named `TypeScript` to your project directory.</span></span>

5.  <span data-ttu-id="09e5d-125">모든 파일을 추가 하기 전에 보겠습니다 가지도록 Visual Studio 옵션 ' 컴파일 저장할 때 ' TypeScript 파일 확인에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-125">Before adding any files, let’s make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="09e5d-126">*도구 > 옵션 > 텍스트 편집기 > Typescript > 프로젝트*</span><span class="sxs-lookup"><span data-stu-id="09e5d-126">*Tools > Options > Text Editor > Typescript > Project*</span></span>

    ![TypeScript 파일의 자동 compliation 설정 옵션](using-grunt/_static/typescript-options.png)

6.  <span data-ttu-id="09e5d-128">마우스 오른쪽 단추로 클릭는 `TypeScript` 디렉터리 및 선택 **추가 > 새 항목** 상황에 맞는 메뉴입니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-128">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="09e5d-129">선택 된 **JavaScript 파일** 항목 및 파일 이름을 *Tastes.ts* (참고는 \*.ts 확장명).</span><span class="sxs-lookup"><span data-stu-id="09e5d-129">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="09e5d-130">TypeScript 코드 아래 줄을 파일에 복사 (저장할 때, 새 *Tastes.js* JavaScript 소스 파일이 표시 됩니다).</span><span class="sxs-lookup"><span data-stu-id="09e5d-130">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>
    
    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7.  <span data-ttu-id="09e5d-131">두 번째 파일을 추가 **TypeScript** 디렉터리 이름을 지정 `Food.ts`합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-131">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="09e5d-132">아래 코드를 파일에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-132">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }
    
      private _name: string;
      get Name() {
        return this._name;
      }
    
      private _calories: number;
      get Calories() {
        return this._calories;
      }
    
      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="09e5d-133">NPM 구성</span><span class="sxs-lookup"><span data-stu-id="09e5d-133">Configuring NPM</span></span>

<span data-ttu-id="09e5d-134">다음으로 NPM 다운로드 grunt 및 grunt 작업을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-134">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="09e5d-135">솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **추가 > 새 항목** 상황에 맞는 메뉴입니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-135">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="09e5d-136">선택 된 **NPM 구성 파일** 항목에서 기본 이름을 *package.json*, 클릭 하 고는 **추가** 단추 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-136">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="09e5d-137">에 *package.json* 파일 내부는 `devDependencies` 중괄호 개체, "grunt"를 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-137">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="09e5d-138">선택 `grunt` Intellisense에서 나열 하 고 Enter 키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-138">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="09e5d-139">Visual Studio grunt 패키지 이름을 따옴표를 콜론을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-139">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="09e5d-140">Intellisense 목록 맨 위부터 콜론 오른쪽에 패키지의 안정적인 최신 버전을 선택 (키를 눌러 `Ctrl-Space` Intellisense 표시 되지 않는 경우).</span><span class="sxs-lookup"><span data-stu-id="09e5d-140">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense does not appear).</span></span>

    ![grun Intellisense](using-grunt/_static/devdependencies-grunt.png)
    
    > [!NOTE]
    > <span data-ttu-id="09e5d-142">NPM 사용 하 여 [의미 체계 버전 관리](http://semver.org/) 종속성을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-142">NPM uses [semantic versioning](http://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="09e5d-143">의미 체계 버전 관리로도 알려져 SemVer 번호 매기기 구성표를 사용 하 여 패키지를 식별 <major>.<minor>합니다. <patch>. Intellisense는 일반적인 몇 가지 옵션에만 표시 하 여 의미 체계 버전 관리를 간소화 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-143">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme <major>.<minor>.<patch>. Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="09e5d-144">맨 위 항목 (위의 예에서 0.4.5) Intellisense 목록에는 패키지의 안정적인 최신 버전으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="09e5d-145">캐럿 (^) 기호가 일치 하는 가장 최근의 주 버전 및 물결표 (~) 최신 부 버전 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="09e5d-146">참조는 [NPM semver 버전 파서 참조](https://www.npmjs.com/package/semver) SemVer 제공 하는 전체 표현 하는 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="09e5d-147">로드에 대 한 더 많은 종속성 grunt 추가-contrib-\* 패키지로 *클린*, *jshint*, *concat*, *uglify*, 및 *조사식* 아래 예에 나와 있는 것 처럼 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="09e5d-148">버전의 예제와 일치 하도록 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-148">The versions do not need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="09e5d-149">저장 된 *package.json* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-149">Save the *package.json* file.</span></span>

<span data-ttu-id="09e5d-150">각 패키지에 필요한 모든 파일 뿐만 아니라 각 devDependencies 항목에 필요한 패키지를 다운로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-150">The packages for each devDependencies item will download, along with any files that each package requires.</span></span> <span data-ttu-id="09e5d-151">패키지 파일을 찾을 수는 `node_modules` 사용 하 여 디렉터리는 **모든 파일 표시** 솔루션 탐색기에서 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-151">You can find the package files in the `node_modules` directory by enabling the **Show All Files** button in the Solution Explorer.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="09e5d-153">솔루션 탐색기에서 종속성 마우스 오른쪽 단추로 클릭 하 여 수동으로 복원할 수 해야 할 경우 `Dependencies\NPM` 선택 하 고는 **패키지 복원** 메뉴 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-153">If you need to, you can manually restore dependencies in Solution Explorer by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![패키지를 복원](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="09e5d-155">Grunt를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-155">Configuring Grunt</span></span>

<span data-ttu-id="09e5d-156">Grunt 이라는 매니페스트를 사용 하 여 구성 된 *Gruntfile.js* 정의 하는, 로드 하 고 Visual Studio에서 이벤트에 따라 자동으로 실행 되도록 구성 하거나 수동으로 실행할 수 있는 작업을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1.  <span data-ttu-id="09e5d-157">프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **추가 > 새 항목**합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-157">Right-click the project and select **Add > New Item**.</span></span> <span data-ttu-id="09e5d-158">선택 된 **Grunt 구성 파일** 옵션에서 기본 이름을 *Gruntfile.js*를 클릭 하 고는 **추가** 단추 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-158">Select the **Grunt Configuration file** option, leave the default name, *Gruntfile.js*, and click the **Add** button.</span></span>

    <span data-ttu-id="09e5d-159">모듈 정의 포함 하는 초기 코드 및 `grunt.initConfig()` 메서드.</span><span class="sxs-lookup"><span data-stu-id="09e5d-159">The initial code includes a module definition and the `grunt.initConfig()` method.</span></span> <span data-ttu-id="09e5d-160">`initConfig()` 각 패키지에 대 한 옵션을 설정 하는 데 사용 되는 모듈의 나머지 부분을 로드 하 고 작업을 등록 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-160">The `initConfig()` is used to set options for each package, and the remainder of the module will load and register tasks.</span></span>
    
    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
      });
    };
    ```

2. <span data-ttu-id="09e5d-161">내에서 `initConfig()` 메서드를 옵션에 대 한 추가 `clean` 예제에서와 같이 작업 *Gruntfile.js* 아래 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-161">Inside the `initConfig()` method, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="09e5d-162">Clean 작업이 디렉터리 문자열의 배열을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-162">The clean task accepts an array of directory strings.</span></span> <span data-ttu-id="09e5d-163">이 작업에서 wwwroot/lib 파일을 제거 하 고 전체/임시 디렉터리를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-163">This task removes files from wwwroot/lib and removes the entire /temp directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

3. <span data-ttu-id="09e5d-164">InitConfig() 메서드 아래에 대 한 호출 추가 `grunt.loadNpmTasks()`합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-164">Below the initConfig() method, add a call to `grunt.loadNpmTasks()`.</span></span> <span data-ttu-id="09e5d-165">이렇게 하면 작업 실행 가능한 Visual Studio가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

4. <span data-ttu-id="09e5d-166">저장 *Gruntfile.js*합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="09e5d-167">파일은 아래 스크린샷에서 비슷해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-167">The file should look something like the screenshot below.</span></span>

    ![초기 gruntfile](using-grunt/_static/gruntfile-js-initial.png)

5. <span data-ttu-id="09e5d-169">마우스 오른쪽 단추로 클릭 *Gruntfile.js* 선택 **Task Runner 탐색기** 상황에 맞는 메뉴입니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="09e5d-170">작업 러너 탐색기 창이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-170">The Task Runner Explorer window will open.</span></span>

    ![작업 러너 탐색기 메뉴](using-grunt/_static/task-runner-explorer-menu.png)

6. <span data-ttu-id="09e5d-172">되어 있는지 확인 `clean` 아래 표시 **작업** Task Runner 탐색기에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-172">Verify that `clean` shows under **Tasks** in the Task Runner Explorer.</span></span>

    ![작업 러너 탐색기 작업 목록](using-grunt/_static/task-runner-explorer-tasks.png)

7. <span data-ttu-id="09e5d-174">정리 작업을 마우스 오른쪽 단추로 클릭 하 고 선택 **실행** 상황에 맞는 메뉴입니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="09e5d-175">명령 창 작업의 진행률을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-175">A command window displays progress of the task.</span></span>

    ![작업 러너 탐색기 실행 정리 작업](using-grunt/_static/task-runner-explorer-run-clean.png)
    
    > [!NOTE]
    > <span data-ttu-id="09e5d-177">파일 또는 디렉터리가 아직 정리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="09e5d-178">원하는 경우 수동으로 만들려면 솔루션 탐색기에서 하 고 테스트로 정리 작업을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>
    
8. <span data-ttu-id="09e5d-179">InitConfig() 방법에서에 대 한 항목 추가 `concat` 아래 코드를 사용 하 여 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-179">In the initConfig() method, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="09e5d-180">`src` 속성 배열은 결합 해야 하는 순서로 결합 하는 파일을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="09e5d-181">`dest` 속성 생성 되는 결합된 된 파일에 경로 할당 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-181">The `dest` property assigns the path to the combined file that is produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```
    
    > [!NOTE]
    > <span data-ttu-id="09e5d-182">`all` 위의 코드에서 속성은 대상의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="09e5d-183">대상이 여러 빌드 환경 수 있도록 몇 가지 Grunt 작업에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="09e5d-184">Intellisense를 사용 하 여 기본 제공 대상 보거나 직접 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-184">You can view the built-in targets using Intellisense or assign your own.</span></span>
    
9. <span data-ttu-id="09e5d-185">추가 `jshint` 아래 코드를 사용 하 여 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="09e5d-186">Jshint 코드 품질 유틸리티는 임시 디렉터리에 있는 모든 JavaScript 파일에 대해 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-186">The jshint code-quality utility is run against every JavaScript file found in the temp directory.</span></span>
    
    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="09e5d-187">옵션 "-W069" 오류가 발생 하 여 만들어집니다 jshint JavaScript 사용 하 여 대괄호 즉, 점 표기법 대신 속성을 할당 하려면 다음 구문을 사용 하는 경우 `Tastes["Sweet"]` 대신 `Tastes.Sweet`합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="09e5d-188">옵션은 나머지 프로세스를 계속할 수 있도록 경고를 해제 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

10.  <span data-ttu-id="09e5d-189">추가 `uglify` 아래 코드를 사용 하 여 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="09e5d-190">축소 작업은 *combined.js* 파일의 임시 디렉터리에서 찾은 wwwroot/lib 표준 명명 규칙에 결과 파일을 만듭니다  *\<파일 이름\>. min.js*.</span><span class="sxs-lookup"><span data-stu-id="09e5d-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>
    
    ```javascript
    uglify: {
      all: {
        src: ['temp/combined.js'],
        dest: 'wwwroot/lib/combined.min.js'
      }
    },
    ```

11. <span data-ttu-id="09e5d-191">Grunt contrib 정리를 로드 하는 호출 grunt.loadNpmTasks() jshint, concat에 대 한 동일한 호출을 포함 하 고 아래 코드를 사용 하 여 uglify 하십시오.</span><span class="sxs-lookup"><span data-stu-id="09e5d-191">Under the call grunt.loadNpmTasks() that loads grunt-contrib-clean, include the same call for jshint, concat and uglify using the code below.</span></span>
    
    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

12. <span data-ttu-id="09e5d-192">저장 *Gruntfile.js*합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="09e5d-193">파일은 아래 예제에서는 비슷해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-193">The file should look something like the example below.</span></span>

    ![전체 grunt 파일 예](using-grunt/_static/gruntfile-js-complete.png)

13. <span data-ttu-id="09e5d-195">작업 러너 탐색기 작업 목록에 포함 됩니다는 `clean`, `concat`, `jshint` 및 `uglify` 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-195">Notice that the Task Runner Explorer Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="09e5d-196">순서로 각 태스크를 실행 하 고 솔루션 탐색기에서 결과 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-196">Run each task in order and observe the results in Solution Explorer.</span></span> <span data-ttu-id="09e5d-197">각 작업은 오류 없이 실행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-197">Each task should run without errors.</span></span>
    
    ![작업 러너 탐색기가 각 작업 실행](using-grunt/_static/task-runner-explorer-run-each-task.png)
    
    <span data-ttu-id="09e5d-199">Concat 작업에서는 새 *combined.js* 파일을 임시 디렉터리에 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="09e5d-200">단순히 jshint 작업 실행 하 고 출력을 생성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-200">The jshint task simply runs and doesn’t produce output.</span></span> <span data-ttu-id="09e5d-201">Uglify 작업에서는 새 *combined.min.js* 파일 하거나 wwwroot/lib에 배치 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-201">The uglify task creates a new *combined.min.js* file and places it into wwwroot/lib.</span></span> <span data-ttu-id="09e5d-202">완료 되 면 솔루션 아래 스크린샷에서 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-202">On completion, the solution should look something like the screenshot below:</span></span>
    
    ![솔루션 탐색기에서 모든 작업](using-grunt/_static/solution-explorer-after-all-tasks.png)
    
    > [!NOTE]
    > <span data-ttu-id="09e5d-204">각 패키지에 대 한 옵션에 대 한 자세한 내용은 방문 [https://www.npmjs.com/](https://www.npmjs.com/) 및 기본 페이지에서 검색 상자에 패키지 이름 조회 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="09e5d-205">예를 들어 grunt contrib 정리 패키지를 모든 해당 매개 변수를 설명 하는 문서 링크를 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="09e5d-206">모두 통합</span><span class="sxs-lookup"><span data-stu-id="09e5d-206">All together now</span></span>

<span data-ttu-id="09e5d-207">Grunt를 사용 하 여 `registerTask()` 메서드를 특정 시퀀스에서 일련의 작업을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="09e5d-208">예를 들어 위의 클린 순서에 단계-> 예를 실행 하려면 concat-> jshint-> uglify, 모듈에 아래 코드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="09e5d-209">InitConfig 외부 loadNpmTasks() 호출와 동일한 수준에는 코드를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="09e5d-210">새 작업 별칭 작업에서 작업 러너 탐색기에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="09e5d-211">마우스 오른쪽 단추로 클릭 하 고 다른 태스크는 것 처럼 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="09e5d-212">`all` 작업은 실행 `clean`, `concat`, `jshint` 및 `uglify`, 순서로 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![별칭 grunt 작업](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="09e5d-214">변경 내용에 대 한 감시</span><span class="sxs-lookup"><span data-stu-id="09e5d-214">Watching for changes</span></span>

<span data-ttu-id="09e5d-215">A `watch` 작업 프로그램은 파일 및 디렉터리에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="09e5d-216">시계 변경 내용을 감지한 경우 자동으로 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="09e5d-217">아래 코드에 대 한 변경 내용을 initConfig를 추가 \*TypeScript 디렉터리에서.js 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="09e5d-218">JavaScript 파일이 변경 되 면 `watch` 실행할지는 `all` 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="09e5d-219">호출을 추가 `loadNpmTasks()` 표시 하는 `watch` Task Runner 탐색기에서 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="09e5d-220">작업 러너 탐색기에서 조사식 작업을 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 실행을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="09e5d-221">"대기 중 …" 실행 중인 조사식 작업을 보여 주는 명령 창에 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-221">The command window that shows the watch task running will display a "Waiting…"</span></span> <span data-ttu-id="09e5d-222">반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-222">message.</span></span> <span data-ttu-id="09e5d-223">TypeScript 파일 중 하나를 열려면, 공백을 추가 하 고 파일을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-223">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="09e5d-224">조사식 작업 트리거 되 고 순서로 실행 하는 다른 작업을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-224">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="09e5d-225">아래 스크린샷에서 실행을 하는 예를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-225">The screenshot below shows a sample run.</span></span>

![작업 출력을 실행](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="09e5d-227">Visual Studio 이벤트에 바인딩</span><span class="sxs-lookup"><span data-stu-id="09e5d-227">Binding to Visual Studio events</span></span>

<span data-ttu-id="09e5d-228">Visual Studio에서 작업할 때마다 작업을 수동으로 시작 하려는 경우가 아니면 작업을 바인딩할 수 있습니다 **하기 전에 빌드**, **빌드 후**, **Clean**, 및  **프로젝트 열기** 이벤트입니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-228">Unless you want to manually start your tasks every time you work in Visual Studio, you can bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="09e5d-229">바인딩 하겠습니다 `watch` Visual Studio가 열릴 때마다 실행 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-229">Let’s bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="09e5d-230">작업 러너 탐색기에서 조사식 작업을 마우스 오른쪽 단추로 클릭 하 고 선택 **바인딩 > 프로젝트 열기** 상황에 맞는 메뉴입니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-230">In Task Runner Explorer, right-click the watch task and select **Bindings > Project Open** from the context menu.</span></span>

![프로젝트 열기 작업을 바인딩합니다](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="09e5d-232">프로젝트를 다시 로드를 언로드하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-232">Unload and reload the project.</span></span> <span data-ttu-id="09e5d-233">프로젝트 다시 로드 될 때 자동으로 실행 조사식 작업이 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-233">When the project loads again, the watch task will start running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="09e5d-234">요약</span><span class="sxs-lookup"><span data-stu-id="09e5d-234">Summary</span></span>

<span data-ttu-id="09e5d-235">Grunt 대부분 클라이언트 빌드 작업을 자동화 하는 데 사용할 수 있는 강력한 작업 runner입니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-235">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="09e5d-236">Grunt NPM 배달의 패키지 및 Visual Studio와의 통합 도구 기능을 활용 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-236">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="09e5d-237">작업 러너 탐색기 visual Studio의 구성 파일에 변경 내용을 검색 하 고 작업 실행, 실행 중인 작업 보기 및 작업을 Visual Studio 이벤트에 바인딩 편리한 인터페이스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="09e5d-237">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="09e5d-238">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="09e5d-238">Additional resources</span></span>

   * [<span data-ttu-id="09e5d-239">Gulp를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="09e5d-239">Using Gulp</span></span>](using-gulp.md)
