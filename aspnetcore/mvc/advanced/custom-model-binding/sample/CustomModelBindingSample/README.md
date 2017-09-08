# <a name="custom-model-binding-demo"></a>사용자 지정 모델 바인딩 데모

테스트할 수 있습니다는 `ByteArrayModelBinder` base64 인코딩 문자열 ImageController 끝점을 게시 하 고 응용 프로그램을 실행 하 여 (/ api/이미지 /). 파일 및 파일 이름 proparties 요청 양식 데이터로 본문에에서 지정 해야 (우체부 또는 유사한 도구를 사용 하 여). 사용할 수 있습니다 [이 샘플 문자열](Base64String.txt)합니다. 결과 지정한 파일 이름으로 업로드/이미지/wwwroot 폴더에 저장 됩니다.

사용자 지정 바인딩 예제를 테스트 하려면 다음 끝점 시도: /api/authors/1 /api/authors/2 (찾을 수 없음) /api/boundauthors/1 /api/boundauthors/2 (찾을 수 없음) /api/boundauthors/get/1 /api/boundauthors/get/2 (콘텐츠 없음)-이 작업에 대 한 검사 하지 않습니다 null 돌아와 찾을 수 없습니다
