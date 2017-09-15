# <a name="working-with-sqlite-in-an-aspnet-core-mvc-project"></a>ASP.NET Core MVC 프로젝트에서 SQLite 작업

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

`MvcMovieContext` 개체는 데이터베이스에 연결하고 데이터베이스 레코드에 `Movie` 개체를 매핑하는 작업을 처리합니다. 데이터베이스 컨텍스트는 *Startup.cs* 파일의 `ConfigureServices` 메서드에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록됩니다.

[!code-csharp[Main](../../tutorials/first-mvc-app-xplat/start-mvc/sample/MvcMovie/Startup.cs?name=snippet2&highlight=6-8)]

## <a name="sqlite"></a>SQLite

[SQLite](https://www.sqlite.org/) 웹 사이트 상태:

> SQLite는 신뢰성 높고 통합 기능을 제공하는 자체 포함된 임베디드 공용 도메인 SQL 데이터베이스 엔진입니다.  SQLite는 전 세계에서 가장 널리 사용되는 데이터베이스 엔진입니다.

여러 타사 도구를 다운로드하여 SQLite 데이터베이스를 관리하고 볼 수 있습니다. 다음은 [DB Browser for SQLite](http://sqlitebrowser.org/)의 이미지입니다. 다른 SQLite 도구를 사용할 경우 그에 관한 의견을 남겨 주세요.

![동영상 DB를 보여 주는 DB Browser for SQLite](../../tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

## <a name="seed-the-database"></a>데이터베이스 시드

*Models* 폴더에 `SeedData`라는 새 클래스를 만듭니다. 생성된 코드를 다음으로 바꿉니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/SeedData.cs?name=snippet_1)]

DB에 동영상이 있는 경우 시드 이니셜라이저가 반환됩니다.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>
### <a name="add-the-seed-initializer"></a>시드 이니셜라이저 추가

*Program.cs* 파일에서 `Main` 메서드에 시드 이니셜라이저를 추가합니다.

[!code-csharp[Main](../../tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Program.cs?highlight=6,16-32)]

### <a name="test-the-app"></a>앱 테스트

DB의 모든 레코드 삭제(시드 메서드 실행을 위해). 앱을 중지 및 시작하여 데이터베이스를 시드합니다.
   
앱에서 시드된 데이터를 보여 줍니다.

![동영상 데이터를 표시하는 MVC Movie 응용 프로그램이 열린 브라우저](../../tutorials/first-mvc-app/working-with-sql/_static/m55.png)
