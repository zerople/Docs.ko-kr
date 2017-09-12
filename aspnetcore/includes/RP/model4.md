다음 표에서는 ASP.NET Core 코드 생성기의 매개 변수를 자세히 설명합니다.

| 매개 변수               | 설명|
| ----------------- | ------------ |
| -m  | 모델의 이름입니다. |
| -dc  | 데이터 컨텍스트입니다. |
| -udl | 기본 레이아웃을 사용합니다. |
| -outDir | 뷰를 만들기 위한 상태 출력 폴더 경로입니다. |
| --referenceScriptLibraries | 편집 및 만들기 페이지에 `_ValidationScriptsPartial`을 추가합니다. |

`h` 스위치를 사용하여 `aspnet-codegenerator razorpage` 명령에 대한 도움말을 확인합니다.

```console
dotnet aspnet-codegenerator razorpage -h
```
<a name="test"></a>
### <a name="test-the-app"></a>앱 테스트

* 앱을 실행하고 브라우저에서 `/Movies`를 URL에 추가합니다(`http://localhost:port/movies`).
* **만들기** 링크를 테스트합니다.

 ![페이지 만들기](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* **편집**, **세부 정보** 및 **삭제** 링크를 테스트합니다.

다음 오류가 표시되면 마이그레이션을 실행했고 데이터베이스를 업데이트했는지 확인합니다.

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)
```