---
title: "Linux 프로덕션 환경에 게시 합니다. | Microsoft 문서"
description: 
keywords: ASP.NET Core
author: rick-anderson
ms.author: riande
manager: wpickett
ms.date: 03/20/2017
ms.topic: article
ms.assetid: 1c33e576-33de-481a-8ad3-896b94fde0e3
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/linuxproduction
translationtype: Machine Translation
ms.sourcegitcommit: a7d6980dab89b42cb63bb6840202719e802da4f3
ms.openlocfilehash: 6095fa4f08791f41d77dc96ad17f770e85cc648e
ms.lasthandoff: 03/23/2017

---
# <a name="publish-to-a-linux-production-environment"></a>Linux 프로덕션 환경에 게시

[Sourabh Shirhatti](https://twitter.com/sshirhatti)

이 가이드에서는 Ubuntu 16.04 서버에 프로덕션이 준비 된 ASP.NET 환경 설정에 살펴보겠습니다.

> [!NOTE]
> Ubuntu 14.04 supervisord Kestrel 프로세스를 모니터링 하기 위한 솔루션으로 좋습니다. systemd는 Ubuntu 14.04에 제공 되지 않습니다. [이전 버전의이 문서를 참조 하십시오.](https://github.com/aspnet/Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)

기존 ASP.NET 핵심 응용 프로그램을 사용 하 고 역방향 프록시 서버 뒤에 놓습니다. 다음 Kestrel 웹 서버에 요청을 전달 하도록 역방향 프록시 서버를 설정 됩니다.

또한 디먼 시작할 때 웹 응용 프로그램 실행 프로그램을 확인 하 고 고가용성을 보장 하기 위해 충돌 시 웹 응용 프로그램을 다시 시작 하려면 프로세스 관리 도구를 구성 합니다.

## <a name="prerequisites"></a>필수 조건

1. Sudo 권한이 있는 표준 사용자 계정 사용 하 여 Ubuntu 16.04 서버에 액세스 합니다.

2. 기존 ASP.NET 핵심 응용 프로그램입니다.

## <a name="copy-over-your-app"></a>응용 프로그램 복사

실행 `dotnet publish` 서버에서 실행할 수 있는 자체 포함 된 디렉터리에 응용 프로그램 패키지를 개발 환경에서.

진행 하기 전에 어떤 도구 (SCP, FTP 등)를 워크플로에 통합을 사용 하 여 서버에 ASP.NET 핵심 응용 프로그램을 복사 합니다. 앱을 실행 하 고 이동 `http://<serveraddress>:<port>` linux 응용 프로그램이 올바르게 실행 하는 경우 참조를 브라우저에 있습니다. 계속 하기 전에 작업 중인 앱이 있는 것이 좋습니다.

> [!NOTE]
> 사용할 수 있습니다 [Yeoman](../client-side/yeoman.md) 새 프로젝트에 대 한 새 ASP.NET 핵심 응용 프로그램을 만들려고 합니다.

## <a name="configure-a-reverse-proxy-server"></a>역방향 프록시 서버를 구성 합니다.

역방향 프록시는 동적 웹 응용 프로그램을 처리 하기 위한 일반적인 설치 합니다. 역방향 프록시는 HTTP 요청을 종료 하 고 ASP.NET 응용 프로그램에 전달 합니다.

### <a name="why-use-a-reverse-proxy-server"></a>역방향 프록시 서버를 사용 하는 이유는?

웹 서비스 제공 파트 IIS, Apache 또는 Nginx와 같은 모든 기능을 갖춘 서버로 풍성한 하지 않지만 kestrel ASP.NET에서 동적 콘텐츠를 제공 하는 것에 대 한 훌륭한입니다. 역방향 프록시 서버를 사용 하면 정적 콘텐츠, 요청 캐싱, 요청 및 HTTP 서버에서 SSL 종료를 압축 하는 작업을 오프 로드할 수 있습니다. 역방향 프록시 서버 전용된 컴퓨터에 상주할 수 있습니다 또는 HTTP 서버와 함께 배포할 수 있습니다.

이 가이드에서는 하겠습니다 HTTP 서버와 함께 동일한 서버에서 실행 되는 Nginx의 단일 인스턴스를 사용 하도록 합니다. 그러나 요구 사항을 기반으로 다른 설치 프로그램을 선택할 수 있습니다.

IIS가 아닌 다른 역방향 프록시 서버를 설정할 때는 호출 해야 `app.UseIdentity` (에서 `Configure`) 전에 다른 외부 공급자입니다.

요청 전달 역방향 프록시를 사용 하 여 `ForwardedHeaders` 미들웨어 (에서 `Microsoft.AspNetCore.HttpOverrides` 패키지) 리디렉션 설정 하기 위해 사용 하는 URI는 `X-Forwarded-For` 및 `X-Forwarded-Proto` 대신의 헤더가 `Request.Scheme` 및 `Request.Host`. "

추가 `UseForwardedHeaders` 를 `Configure` 호출 하기 전에 `app.UseFacebookAuthentication` 또는 유사한:

```csharp
            app.UseForwardedHeaders(new ForwardedHeadersOptions
            {
                ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
            });
```

### <a name="install-nginx"></a>Nginx 설치

```
sudo apt-get install nginx
```

> [!NOTE]
> 선택적 Nginx 모듈을 설치 하려는 경우 Nginx 소스에서 바인딩할 해야 할 수 있습니다.

여기서는를 `apt-get` Nginx 설치 합니다. 설치 관리자는 또한 시스템 시작 시에 디먼 Nginx를 실행 하는 시스템 V init 스크립트를 만듭니다. 처음으로 Nginx를 방금 설치 않았으므로 우리 명시적으로 시작할 수를 실행 하 여

```bash
sudo service nginx start
```

이 시점에서 브라우저를 이동 하 고 기본 Nginx에 대 한 방문 페이지를 확인할 수 있습니다.

### <a name="configure-nginx"></a>Nginx를 구성 합니다.

ASP.NET 응용 프로그램에 대 한 요청을 전달 하는 역방향 프록시로 Nginx을 이제 구성

수정할 것입니다는 `/etc/nginx/sites-available/default`, 따라서 원하는 텍스트 편집기에서 열어 및 내용을 다음으로 바꿉니다.

```nginx
server {
    listen 80;
    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection keep-alive;
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Nginx 포트에서 들어오는 공용 트래픽을 전달 하는 가장 간단한 구성 파일 중 하나입니다 `80` 포트로 `5000` 는 웹 응용 프로그램에서 수신 합니다.

실행할 수 있습니다 nginx 구성 변경 완료 한 후 `sudo nginx -t` 구성 파일의 구문을 확인 합니다. 구성 파일 테스트에 성공한 경우 nginx를 실행 하 여 변경 내용을 선택 하도록 요청할 수 있습니다 `sudo nginx -s reload`합니다.

## <a name="monitoring-our-application"></a>응용 프로그램 모니터링

Nginx에 대 한 요청을 전달 하도록 설정 되어 이제 `http://localhost:80` 에서 Kestrel에서 실행 중인 ASP.NET 핵심 응용 프로그램에 로그온 `http://127.0.0.1:5000`합니다.  하지만, Nginx는 Kestrel 프로세스를 관리 하기 설정 되지 않습니다. 사용 하 여 *systemd* 을 시작 하 고 기본 웹 앱 모니터링 서비스 파일을 만듭니다. *systemd* 는 시작, 중지, 프로세스 관리에 대 한 여러 가지 강력한 기능을 제공 하는 init 시스템입니다. 

### <a name="create-the-service-file"></a>서비스 파일 만들기

서비스 정의 파일 만들기 

```bash
    sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

응용 프로그램에 대 한 예제 서비스 파일입니다.

```text
[Unit]
    Description=Example .NET Web API Application running on Ubuntu

    [Service]
    WorkingDirectory=/var/aspnetcore/hellomvc
    ExecStart=/usr/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
    Restart=always
    RestartSec=10                                          # Restart service after 10 seconds if dotnet service crashes
    SyslogIdentifier=dotnet-example
    User=www-data
    Environment=ASPNETCORE_ENVIRONMENT=Production 

    [Install]
    WantedBy=multi-user.target
```

> [!NOTE]
> **사용자** --사용자 *www 데이터* 사용 되지 않는 구성에 따라 여기에 정의 된 사용자를 만든 다음 먼저 파일에 대 한 적절 한 소유권이 부여

파일을 저장 하 고 서비스를 사용 하도록 설정 합니다.

```bash
    systemctl enable kestrel-hellomvc.service
```

서비스를 시작 하 고 실행 중인지 확인 합니다.

```
    systemctl start kestrel-hellomvc.service
    systemctl status kestrel-hellomvc.service

    ● kestrel-hellomvc.service - Example .NET Web API Application running on Ubuntu
        Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
        Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
    Main PID: 9021 (dotnet)
        CGroup: /system.slice/kestrel-hellomvc.service
                └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

역방향 프록시 구성 및 Kestrel systemd를 통해 관리를 사용 하 여 웹 응용 프로그램 구성 완벽 하 게 되 고 브라우저에서 로컬 컴퓨터에서 액세스할 수 `http://localhost`합니다. 응답 헤더를 검사 하는 **서버** 여전히 Kestrel에서 제공 하는 ASP.NET 핵심 응용 프로그램을 표시 합니다.

```text
    HTTP/1.1 200 OK
    Date: Tue, 11 Oct 2016 16:22:23 GMT
    Server: Kestrel
    Keep-Alive: timeout=5, max=98
    Connection: Keep-Alive
    Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a>로그 보기

Kestrel를 사용 하 여 웹 응용 프로그램을 관리 되는 systemd를 사용 하 여 이후 모든 이벤트와 프로세스는 중앙 집중식된 저널에 기록 됩니다. 그러나이 저널 모든 서비스 및 systemd에서 관리 하는 프로세스에 대 한 모든 항목을 포함 합니다. 보려는 `kestrel-hellomvc.service` 특정 항목에는 다음 명령을 사용 합니다.

```bash
    sudo journalctl -fu kestrel-hellomvc.service
```

필터링에 대 한 추가, 시간 옵션 같은 `--since today`, `--until 1 hour ago` 또는 이들의 조합을 반환 된 항목의 크기를 줄일 수 있습니다.

```bash
    sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-our-application"></a>응용 프로그램 보안

### <a name="enable-apparmor"></a>사용 하도록 설정`AppArmor`

Linux 보안 모듈 (LSM)는 보안 모듈의 다른 구현을 지 원하는 Linux 2.6 이후 Linux 커널에 포함 된 프레임 워크입니다. `AppArmor`프로그램의 리소스 제한 된 집합을 제한할 수 있는 필수 액세스 제어 시스템을 구현 하는 LSM입니다. 확인 [AppArmor](https://wiki.ubuntu.com/AppArmor) 사용 되 고 올바르게 구성 되어 있습니다.

### <a name="configuring-our-firewall"></a>우리의 방화벽 구성

모든 외부 포트를 사용 하지 않는 마무리 합니다. 복잡 하지 않은 방화벽 (ufw) 제공에 대 한 프런트 `iptables` 방화벽을 구성 하기 위한 명령줄 인터페이스를 제공 하 여 합니다. 확인 `ufw` 필요한 모든 포트에서 트래픽을 허용 하도록 구성 됩니다.

```bash
sudo apt-get install ufw
sudo ufw enable

sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

### <a name="securing-nginx"></a>Nginx 보안 설정

Nginx의 기본 배포는 SSL을 사용 하지 않습니다. 필요한 모든 보안 기능을 사용 하려면 소스에서 작성 합니다.

#### <a name="download-the-source-and-install-the-build-dependencies"></a>소스를 다운로드 하 고 빌드 종속성을 설치

```bash
# Install the build dependencies
sudo apt-get update
sudo apt-get install build-essential zlib1g-dev libpcre3-dev libssl-dev libxslt1-dev libxml2-dev libgd2-xpm-dev libgeoip-dev libgoogle-perftools-dev libperl-dev

# Download nginx 1.10.0 or latest
wget http://www.nginx.org/download/nginx-1.10.0.tar.gz
tar zxf nginx-1.10.0.tar.gz
```

#### <a name="change-the-nginx-response-name"></a>Nginx 응답 이름 변경

편집 *src/http/ngx_http_header_filter_module.c*

```c
static char ngx_http_server_string[] = "Server: Your Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Your Web Server" CRLF;
```

#### <a name="configure-the-options-and-build"></a>옵션을 구성 하 고 빌드

PCRE 라이브러리는 정규식에 대 한 필요 합니다. 정규식은 ngx_http_rewrite_module에 위치 지시문에서 사용 됩니다. http_ssl_module HTTPS 프로토콜 지원을 추가합니다.

와 같은 웹 응용 프로그램 방화벽을 사용 하는 것이 좋습니다 *ModSecurity* 응용 프로그램을 강화 합니다.

```bash
./configure
--with-pcre=../pcre-8.38
--with-zlib=../zlib-1.2.8
--with-http_ssl_module
--with-stream
--with-mail=dynamic
```

#### <a name="configure-ssl"></a>SSL 구성

* 포트에서 HTTPS 트래픽을 수신 하도록 서버 구성 `443` 신뢰할 수 있는 인증 기관 (CA)에서 발급 된 유효한 인증서를 지정 하 여 합니다.

* 아래와 같은 보다 강력한 암호화를 선택 하 고 HTTP를 HTTPS 통해 모든 트래픽을 리디렉션하여 제안 사례 중 일부를 사용 하 여 보안을 강화 합니다.

* 추가 `HTTP Strict-Transport-Security` (HSTS) 헤더를 사용 하면 클라이언트에서 수행한 모든 후속 요청은 HTTPS를 통한만 합니다.

* Strict-전송-보안 헤더를 추가 하지 않으면 또는 적절 하 게 선택한 `max-age` 나중에 SSL을 사용 하지 않도록 하려는 경우.

추가 `/etc/nginx/proxy.conf` 구성 파일입니다.

[!code-nginx[주](linuxproduction/proxy.conf)]

편집 `/etc/nginx/nginx.conf` 구성 파일입니다. 이 예제는 하나의 구성 파일에 http와 서버 모두 섹션이 있습니다.

[!code-nginx[주](../publishing/linuxproduction/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a>Nginx clickjacking에서 보안
Clickjacking은 감염된 된 사용자의 클릭을 수집 하는 악성 기술입니다. Clickjacking 가장 하도록 한 다음 감염된 된 사이트를 클릭 하면 교착 상태가 발생 (방문자). X-프레임-옵션 사용을 사이트의 보안을 합니다.

Nginx.conf (영문) 파일을 편집 합니다.

```bash
    sudo nano /etc/nginx/nginx.conf
```

추가 된 줄 `add_header X-Frame-Options "SAMEORIGIN";` 하 고 파일을 저장 한 다음 Nginx를 다시 시작 합니다.

#### <a name="mime-type-sniffing"></a>MIME 형식 검사

이 헤더 MIME 스니핑에서 Internet Explorer에서 선언 된 content-type 응답 헤더 응답 콘텐츠 형식을 재정의할 수는 없습니다 브라우저에 지시 방지 합니다. Nosniff 옵션을 서버는 콘텐츠는 text/html 표시 되 면 브라우저는 렌더링 text/html로.

Nginx.conf (영문) 파일을 편집 합니다.

```bash
    sudo nano /etc/nginx/nginx.conf
```

추가 된 줄 `add_header X-Content-Type-Options "nosniff"` 하 고 파일을 저장 한 다음 Nginx를 다시 시작 합니다.

