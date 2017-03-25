---
title: "SQL Server LocalDB 사용 | Microsoft 문서"
author: rick-anderson
description: "SQL Server LocalDB를 사용 하 여 간단한 MVC 응용 프로그램 사용"
keywords: ASP.NET Core, SQL Server LocalDB, SQL Server LocalDB
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: article
ms.assetid: ff8fd9b8-7c98-424d-8641-7524e23bf541
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/working-with-sql
translationtype: Machine Translation
ms.sourcegitcommit: f93c93002fec0088a7040cd53f31fd5b5a62fea7
ms.openlocfilehash: 4ce2c5f58c9387f3585df2fdd69a9d4c37dc0301
ms.lasthandoff: 03/23/2017

---
# <a name="working-with-sql-server-localdb"></a>SQL Server LocalDB 사용

[Rick Anderson](https://twitter.com/RickAndMSFT)

`MvcMovieContext` 개체를 처리 하는 작업을 데이터베이스에 연결 하 고 매핑 `Movie` 데이터베이스 레코드에는 개체입니다. 등록 된 데이터베이스 컨텍스트는 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에는 `ConfigureServices` 에서 메서드는 *Startup.cs* 파일:

[!code-csharp[주](start-mvc/sample/MvcMovie/Startup.cs?name=snippet_cs&highlight=6-8)]

ASP.NET Core [구성](xref:fundamentals/configuration) 시스템 읽기는 `ConnectionString`합니다. 연결 문자열을 가져와서 로컬 개발을 위해는 *appsettings.json* 파일:

[!code-javascript[주](start-mvc/sample/MvcMovie/appsettings.json?highlight=3&range=8-10)]

환경 변수 또는 다른 테스트 또는 프로덕션 서버에 응용 프로그램을 배포할 때 사용할 수 있습니다 실제 SQL Server에 연결 문자열을 설정 하는 방법입니다. 참조 [구성](xref:fundamentals/configuration) 에 대 한 자세한 내용은 합니다.

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB는 SQL Server Express 데이터베이스 엔진의 대상이 되는 프로그램 개발을 위한의 경량 버전입니다. LocalDB는 요청에서 시작 하 고 복잡 한 구성은 없으며 사용자 모드에서 실행 합니다. 기본적으로 LocalDB 데이터베이스는 다음과 같이 생성 됩니다. "\*.mdf" 파일에 *c: / 사용자/\<사용자\>* 디렉터리입니다.

* **보기** 메뉴, 열기 **SQL Server 개체 탐색기** (SSOX).

  ![보기 메뉴](working-with-sql/_static/ssox.png)

* 오른쪽 단추로 클릭 하 여 `Movie` 테이블 **> 뷰 디자이너**

  ![Movie 테이블에서 열려 상황에 맞는 메뉴](working-with-sql/_static/design.png)

  ![디자이너에 열려 영화 테이블](working-with-sql/_static/dv.png)

키 아이콘 옆에 확인 `ID`합니다. 기본적으로 EF 라는 속성이 하면 `ID` 기본 키입니다.

* 오른쪽 단추로 클릭 하 여 `Movie` 테이블 **> 데이터 보기**

  ![Movie 테이블에서 열려 상황에 맞는 메뉴](working-with-sql/_static/ssox2.png)

  ![Movie 테이블 열리고 테이블 데이터 표시](working-with-sql/_static/vd22.png)

## <a name="seed-the-database"></a>데이터베이스를 시드하십시오

라는 새 클래스를 만들고 `SeedData` 에 *모델* 폴더입니다. 생성 된 코드를 다음으로 바꿉니다.

[!code-csharp[주](start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]

데이터베이스에서 모든 동영상 없으면 시드 이니셜라이저 반환을 확인 합니다.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

시드 이니셜라이저의 끝에 추가 된 `Configure` 에서 메서드는 *Startup.cs* 파일:

[!code-csharp[주](start-mvc/sample/MvcMovie/Startup.cs?highlight=9&name=snippet_seed)]

앱 테스트

* DB의 모든 레코드를 삭제 합니다. 브라우저 또는 SSOX에서 삭제 링크를 통해이 수행할 수 있습니다.
* 응용 프로그램에서 초기화를 강제로 (의 메서드를 호출는 `Startup` 클래스) 않으므로 seed 메서드를 실행 합니다. 초기화를 적용 하려면 IIS Express를 중지 하 고 다시 시작 해야 합니다. 다음 방법 중 하나를 사용 하 여이 수행할 수 있습니다.

  * 알림 영역에서 IIS Express 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭 하 고 탭 **종료** 또는 **사이트 중지**

    ![IIS Express 시스템 트레이 아이콘](working-with-sql/_static/iisExIcon.png)

    ![상황에 맞는 메뉴](working-with-sql/_static/stopIIS.png)

    > [!Note]
    > Visual Studio 2017 RC에서 IIS Express를 중지할 필요가 없습니다.

   * 비-디버그 모드에서 VS를 실행 되 고 F5 키를 눌러 디버그 모드에서 실행
   * 디버그 모드에서 VS 디버거를 중지 하 고 F5 키를 눌러 실행 한 경우
   
응용 프로그램에서 시드 된 데이터를 표시 합니다.

![영화 데이터를 보여 주는 Microsoft Edge에서 MVC 동영상 응용 프로그램을 엽니다.](working-with-sql/_static/m55.png)

>[!div class="step-by-step"]
[이전](adding-model.md)
[다음](controller-methods-views.md)  

