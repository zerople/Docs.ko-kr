<a name="cs"></a>
### <a name="add-a-database-connection-string"></a>데이터베이스 연결 문자열 추가

*appsettings.json* 파일에 연결 문자열을 추가합니다.

[!code-json[Main](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

<a name="reg"></a>
###  <a name="register-the-database-context"></a>데이터베이스 컨텍스트 등록

*Startup.cs* 파일에서 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 데이터베이스 컨텍스트를 등록합니다.