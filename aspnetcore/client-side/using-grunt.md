---
title: "ASP.NET Core에서 Grunt를 사용 하 여 | Microsoft 문서"
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
ms.translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 3ceb0ef7dfbf597857153cad54c8ed725f364787
ms.contentlocale: ko-kr
ms.lasthandoff: 03/23/2017

---
# <a name="using-grunt-in-aspnet-core"></a>ASP.NET Core에서 Grunt를 사용 하 여 

[Noel 밥](http://blog.falafel.com/author/noel-rice/)

Grunt은 스크립트 축소, TypeScript 컴파일, 코드 품질 lint 도구, CSS 전 프로세서 및 거의 모든 반복적인 번거로운 작업이 클라이언트 개발을 지원 하기 위해 수행 하는 자동화 하는 JavaScript 작업 러너입니다. ASP.NET 프로젝트 템플릿은 기본적으로 Gulp 사용 하지만 Visual Studio에서 grunt 완전히 지원 됩니다 (참조 [Gulp 사용 하 여](using-gulp.md)).

이 예제에서는 그 출발점으로 빈 ASP.NET 핵심 프로젝트를 사용 하 여 처음부터 클라이언트 빌드 프로세스를 자동화 하는 방법을 보여 줍니다.

완성 된 예제 대상 배포 디렉터리를 정리, JavaScript 파일 결합 하 여, 코드 품질을 확인, JavaScript 파일의 내용을 압축 및 웹 응용 프로그램의 루트에 배포 합니다. 다음 패키지를 사용 합니다.

* **grunt**: The 성가신 작업 러너 패키지 합니다.

* **grunt contrib 정리**: 파일 또는 디렉터리를 제거 하는 플러그 인입니다.

* **grunt-contrib-jshint**: JavaScript 코드의 품질을 검토 하는 플러그 인입니다.

* **concat-contrib 기능 grunt**: 단일 파일에 파일을 조인 하는 플러그 인입니다.

* **grunt contrib uglify**: 크기를 줄이기 위해 JavaScript minifies 하는 플러그 인입니다.

* **grunt contrib 감시**: 파일 작업을 감시 하는 플러그 인입니다.

## <a name="preparing-the-application"></a>응용 프로그램 준비

를 시작 하려면 새 빈 웹 응용 프로그램을 설정 하 고 TypeScript 예를 들어 파일을 추가 합니다. TypeScript 파일은 자동으로 기본 Visual Studio 설정을 사용 하 여 JavaScript로 컴파일되는 하며 우리의 원료 Grunt를 사용 하 여 처리 됩니다.

1.  Visual Studio에서 만들 새 `ASP.NET Web Application`합니다.

2.  에 **새 ASP.NET 프로젝트** 대화 상자에서 ASP.NET 핵심 선택 **빈** 서식 파일 확인 단추를 클릭 하 고 있습니다.

3.  솔루션 탐색기에서 프로젝트 구조를 검토 합니다. `\src` 폴더에 빈 포함 `wwwroot` 및 `Dependencies` 노드.

    ![빈 웹 솔루션](using-grunt/_static/grunt-solution-explorer.png)

4.  라는 새 폴더 추가 `TypeScript` 프로젝트 디렉터리에 있습니다.

5.  모든 파일을 추가 하기 전에 보겠습니다 Visual Studio에 포함 되도록 옵션 ' 컴파일 저장 ' 체크 TypeScript 파일에 대 한 합니다. *도구 > 옵션 > 텍스트 편집기 > Typescript > 프로젝트*

    ![TypeScript 파일의 자동 compliation 설정 옵션](using-grunt/_static/typescript-options.png)

6.  마우스 오른쪽 단추로 클릭는 `TypeScript` 디렉터리 및 선택 **추가 > 새 항목** 상황에 맞는 메뉴에서 합니다. 선택 된 **JavaScript 파일** 항목 및 파일 이름을 *Tastes.ts* (참고는 \*.ts 확장). TypeScript는 아래 코드 줄을 파일에 복사 (저장할 때, 새 *Tastes.js* JavaScript 소스 파일이 표시 됩니다).
    
    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7.  두 번째 파일을 추가 **TypeScript** 디렉터리 이름을 `Food.ts`합니다. 아래 코드를 파일에 복사 합니다.

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

## <a name="configuring-npm"></a>NPM 구성

다음으로, NPM 다운로드 grunt 및 성가신 작업을 구성 합니다.

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **추가 > 새 항목** 상황에 맞는 메뉴에서 합니다. 선택은 **NPM 구성 파일** 항목을 기본 이름을 그대로 두고 *package.json*, 클릭 하 고는 **추가** 단추입니다.

2. 에 *package.json* 파일에 `devDependencies` 중괄호 개체, "grunt"를 입력 합니다. 선택 `grunt` Intellisense에서 나열 하 고 Enter 키를 누릅니다. Visual Studio 성가신 패키지 이름에 따옴표 사용 하 고 콜론을 추가 합니다. 콜론의 오른쪽에서 Intellisense 목록 맨 위에서 패키지의 안정적인 최신 버전을 선택 (키를 눌러 `Ctrl-Space` Intellisense는 표시 되지 않는 경우).

    ![grun Intellisense](using-grunt/_static/devdependencies-grunt.png)
    
    > [!NOTE]
    > NPM 사용 하 여 [의미 체계 버전 관리](http://semver.org/) 종속성을 구성할 수 있습니다. 번호 매기기 있는 패키지를 식별 하는 의미 체계 버전 관리 라고도 SemVer <major>.<minor>합니다. <patch>. Intellisense만 몇 가지 일반적인 옵션을 표시 하 여 의미 체계 버전 관리를 간소화 합니다. Intellisense 목록 (위 예제에서&0;.4.5)에서 맨 위 항목에는 패키지의 안정적인 최신 버전으로 간주 됩니다. 캐럿 (^) 기호가 일치 하는 가장 최근의 주 버전 및 물결표 (~) 최신 부 버전 일치 합니다. 참조는 [NPM semver 버전 파서 참조](https://www.npmjs.com/package/semver) SemVer 제공 하는 전체 표현할 수를 기준으로 합니다.

3. 로드할 종속성 grunt 추가-contrib-\* 에 대 한 패키지 *정리*, *jshint*, *concat*, *uglify*, 및 *조사식* 아래 예와에서 같이 합니다. 버전의 예제와 일치 하도록 필요가 없습니다.

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

4. 저장 된 *package.json* 파일입니다.

각 devDependencies 항목에 대 한 패키지는 각 패키지에 필요한 모든 파일과 함께 다운로드 됩니다. 패키지 파일을 찾을 수는 `node_modules` 을 사용 하 여 디렉터리의 **모든 파일 표시** 솔루션 탐색기에서 단추입니다.

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> 솔루션 탐색기에서 종속성 마우스 오른쪽 단추로 클릭 하 여 수동으로 복원할 수 해야 할 경우 `Dependencies\NPM` 선택 하는 **패키지 복원** 메뉴 옵션입니다.

![패키지를 복원 합니다.](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Grunt를 구성합니다.

Grunt 라는 매니페스트를 사용 하 여 구성 된 *Gruntfile.js* 정의 하는, 로드 하 고 Visual Studio에서 이벤트에 따라 자동으로 실행 되도록 구성 하거나 수동으로 실행할 수 있는 작업을 등록 합니다.

1.  프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **추가 > 새 항목**합니다. 선택의 **Grunt 구성 파일** 옵션에서 기본 이름을 그대로 두고 *Gruntfile.js*, 클릭 하 고는 **추가** 단추입니다.

    모듈 정의 포함 하는 초기 코드 및 `grunt.initConfig()` 메서드. `initConfig()` 각 패키지에 대 한 옵션을 설정 하는 데 사용 되는 모듈의 나머지 부분에서 로드 되 고 작업을 등록 하 고 있습니다.
    
    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
      });
    };
    ```

2. 내부는 `initConfig()` 메서드를 옵션에 대 한 추가 `clean` 작업 예제에 표시 된 대로 *Gruntfile.js* 아래입니다. Clean 작업이 디렉터리 문자열의 배열을 수락합니다. 이 작업 wwwroot/lib에서 파일을 제거 하 고 전체/임시 디렉터리를 제거 합니다.

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

3. InitConfig() 메서드 아래에 대 한 호출을 추가 `grunt.loadNpmTasks()`합니다. 이렇게 하면 작업 실행 가능한 Visual Studio에서.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

4. 저장 *Gruntfile.js*합니다. 파일에는 아래 스크린샷과 같이 표시 됩니다.

    ![초기 gruntfile](using-grunt/_static/gruntfile-js-initial.png)

5. 마우스 오른쪽 단추로 클릭 *Gruntfile.js* 선택한 **Task Runner 탐색기** 상황에 맞는 메뉴에서 합니다. Task Runner 탐색기 창이 열립니다.

    ![작업 러너 탐색기 메뉴](using-grunt/_static/task-runner-explorer-menu.png)

6. 확인 `clean` 아래 표시 **작업** Task Runner 탐색기에 있습니다.

    ![작업 러너 탐색기 작업 목록](using-grunt/_static/task-runner-explorer-tasks.png)

7. 정리 작업을 마우스 오른쪽 단추로 클릭 하 고 선택 **실행** 상황에 맞는 메뉴에서 합니다. 명령 창에는 작업의 진행률이 표시 됩니다.

    ![작업 러너 탐색기 실행에 대 한 정리 작업](using-grunt/_static/task-runner-explorer-run-clean.png)
    
    > [!NOTE]
    > 파일 또는 디렉터리를 아직 정리 있습니다. 원하는 경우 솔루션 탐색기에서이 수동으로 만들 수 있으며 다음 테스트로 정리 작업을 실행.
    
8. InitConfig() 방법에서에 대 한 항목을 추가 `concat` 아래 코드를 사용 합니다.

    `src` 속성 배열은 결합 되는 순서로 결합 하 여 파일을 나열 합니다. `dest` 속성 생성 되는 결합된 된 파일에 경로 할당 합니다.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```
    
    > [!NOTE]
    > `all` 위 코드에서 속성은 대상의 이름입니다. 목표는 일부 성가신 작업에 여러 빌드 환경을 허용 하는 데 사용 됩니다. Intellisense를 사용 하 여 기본 제공 목표를 확인할 수도 있고 직접 지정할 수 있습니다.
    
9. 추가 된 `jshint` 아래 코드를 사용 하 여 작업 합니다.

    Jshint 코드 품질 유틸리티는 임시 디렉터리에 모든 JavaScript 파일에 대해 실행 됩니다.
    
    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > 옵션 "-W069" 오류가 발생 하 여 만들어집니다 jshint JavaScript 사용 하 여 대괄호, 즉 점 표기법 대신 속성을 할당 하려면 다음 구문을 사용 하는 경우 `Tastes["Sweet"]` 대신 `Tastes.Sweet`합니다. 옵션은 나머지 프로세스를 계속할 수 있도록 경고를 해제 합니다.

10.  추가 된 `uglify` 아래 코드를 사용 하 여 작업 합니다.

    작업 minifies는 *combined.js* 파일의 임시 디렉터리에서 찾은 wwwroot/lib 다음 표준 명명 규칙에에서 결과 파일을 만듭니다 *\<파일 이름\>. min.js*합니다.
    
    ```javascript
    uglify: {
      all: {
        src: ['temp/combined.js'],
        dest: 'wwwroot/lib/combined.min.js'
      }
    },
    ```

11. Grunt contrib 정리를 로드 하는 호출 grunt.loadNpmTasks() jshint, concat에 대 한 동일한 호출을 포함 하 고 아래 코드를 사용 하 여 uglify 합니다.
    
    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

12. 저장 *Gruntfile.js*합니다. 파일에는 다음 예제에서는 같이 표시 됩니다.

    ![전체 성가신 파일 예](using-grunt/_static/gruntfile-js-complete.png)

13. 작업 러너 탐색기 작업 목록 포함 `clean`, `concat`, `jshint` 및 `uglify` 작업 합니다. 순서로 각 작업을 실행 하 고 솔루션 탐색기에서 결과 확인 합니다. 각 작업은 오류 없이 실행 해야 합니다.
    
    ![각 작업을 실행 하는 task runner 탐색기](using-grunt/_static/task-runner-explorer-run-each-task.png)
    
    Concat 작업에서는 새 *combined.js* 파일을 임시 디렉터리에 배치 합니다. 단순히 jshint 작업 실행 하 고 출력을 생성 하지 않습니다. Uglify 작업에서는 새 *combined.min.js* 파일을 wwwroot/lib에 배치 합니다. 완료 되 면 솔루션 아래 스크린샷과 같이 표시 됩니다.
    
    ![솔루션 탐색기에서 모든 작업](using-grunt/_static/solution-explorer-after-all-tasks.png)
    
    > [!NOTE]
    > 각 패키지에 대 한 옵션에 대 한 자세한 내용은 참조 하십시오. [https://www.npmjs.com/](https://www.npmjs.com/) 및 기본 페이지에서 검색 상자에 패키지 이름 조회 합니다. 예를 들어, 모든 해당 매개 변수를 설명 하는 설명서 링크를 가져오는 성가신 contrib 정리 패키지를 찾을 수 있습니다.

### <a name="all-together-now"></a>모두 통합

Grunt를 사용 하 여 `registerTask()` 메서드를 특정 시퀀스에서 일련의 작업을 실행 합니다. 예를 들어 위 예에서는 단계 uglify-> jshint-> concat-> 정리 순서에서를 실행 하려면 모듈에 아래 코드를 추가 합니다. LoadNpmTasks() 호출 initConfig 외부와 동일한 수준에는 코드를 추가 해야 합니다.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

새 작업 별칭 작업에서 Task Runner 탐색기에 나타납니다. 마우스 오른쪽 단추로 클릭 하 고 다른 작업 것 처럼 실행할 수 있습니다. `all` 작업이 실행 됩니다 `clean`, `concat`, `jshint` 및 `uglify`를 순서 대로입니다.

![별칭 성가신 작업](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>변경 내용을 감시

A `watch` 작업 파일 및 디렉터리에서 감시 합니다. 시계 변경 내용을 검색 하는 경우 작업을 자동으로 트리거합니다. InitConfig 변경에 대 한 조사에 아래 코드를 추가 \*TypeScript 디렉터리에 있는.js 파일입니다. JavaScript 파일이 변경 되 면 `watch` 실행될지는 `all` 작업 합니다.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

호출을 추가 하면 `loadNpmTasks()` 표시 하는 `watch` Task Runner 탐색기에서 작업 합니다.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Task Runner 탐색기에서 조사식 작업을 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 실행을 선택 합니다. 조사식 작업이 실행 중임을 표시 하는 명령 창에는 "대기 중..." 표시 됩니다. 반환됩니다. TypeScript 파일 중 하나를 열고, 공백을 추가 하 고 파일을 저장 합니다. 조사식 작업 트리거를 순서 대로 실행 하는 다른 작업을 트리거할 합니다. 다음 스크린샷은 샘플 실행을 보여 줍니다.

![작업 출력을 실행](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Visual Studio 이벤트에 바인딩

Visual Studio에서 작업할 때마다 수동으로 작업을 시작 하려는 경우가 아니면 작업을 바인딩할 수 있습니다 **하기 전에 빌드**, **빌드 후**, **Clean**, 및 **프로젝트 열기** 이벤트입니다.

바인딩 하겠습니다 `watch` Visual Studio가을 열 때마다 실행 되도록 합니다. Task Runner 탐색기에서 조사식 작업을 마우스 오른쪽 단추로 클릭 하 고 선택 **바인딩 > 프로젝트 열기** 상황에 맞는 메뉴에서 합니다.

![프로젝트 열기에는 작업 바인딩](using-grunt/_static/bindings-project-open.png)

언로드 및 프로젝트를 다시 로드 합니다. 프로젝트 다시 로드 될 때 자동으로 실행 조사식 작업이 시작 됩니다.

## <a name="summary"></a>요약

Grunt 대부분 클라이언트 빌드 작업을 자동화 하는 데 사용할 수 있는 강력한 작업 러너입니다. Grunt NPM의 패키지를 Visual Studio와의 통합 도구 기능을 제공 하기를 활용 합니다. Visual Studio의 Task Runner 탐색기 구성 파일의 변경 사항을 감지 하 고 작업을 실행 하 고, 실행 중인 작업을 보고, 작업 Visual Studio 이벤트에 바인딩할 하는 편리한 인터페이스를 제공 합니다.

## <a name="additional-resources"></a>추가 리소스

   * [Gulp 사용](using-gulp.md)

