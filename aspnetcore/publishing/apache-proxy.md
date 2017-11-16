---
title: "Apache를 사용하여 Linux에서 ASP.NET Core 호스트"
description: "CentOS에서 Apache를 역방향 프록시 서버로 설정하여 Kestrel에서 실행되는 ASP.NET Core 웹 응용 프로그램에 HTTP 트래픽을 리디렉션하는 방법을 알아봅니다."
keywords: "ASP.NET Core,Apache,CentOS,역방향 프록시,Linux,mod_proxy,httpd,호스팅"
author: spboyer
ms.author: spboyer
manager: wpickett
ms.date: 10/19/2016
ms.topic: article
ms.assetid: fa9b0cb7-afb3-4361-9e7e-33afffeaca0c
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/apache-proxy
ms.openlocfilehash: 34ede2fdebe0e9516f62e39618e7adba3c8c89db
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="set-up-a-hosting-environment-for-aspnet-core-on-linux-with-apache-and-deploy-to-it"></a>Linux에서 Apache를 사용하여 ASP.NET Core에 대한 호스팅 환경을 설정하고 해당 환경에 배포

작성자: [Shayne Boyer](https://github.com/spboyer)

Apache는 널리 사용되는 HTTP 서버이고 Nginx와 비슷한 HTTP 트래픽을 리디렉션하도록 프록시로 구성할 수 있습니다. 이 가이드에서는 CentOS 7의 Apache를 설정하고 역방향 프록시로 사용하여 들어오는 연결을 시작하고 Kestrel에서 실행되는 ASP.NET Core 응용 프로그램으로 리디렉션하는 방법에 대해 알아봅니다. 이를 위해 *mod_proxy* 확장 및 기타 관련 Apache 모듈을 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

1. sudo 권한을 가진 표준 사용자 계정으로 CentOS 7을 실행하는 서버
2. 기존 ASP.NET Core 응용 프로그램 

## <a name="publish-your-application"></a>응용 프로그램 게시

개발 환경에서 `dotnet publish -c Release`를 실행하여 서버에서 실행할 수 있는 자체 포함된 디렉터리에 응용 프로그램을 패키지합니다. 게시된 응용 프로그램은 SCP, FTP 또는 기타 파일 전송 메서드를 사용하여 서버에 복사되어야 합니다. 

> [!NOTE]
> 프로덕션 배포 시나리오에서 연속 통합 워크플로는 응용 프로그램을 게시하고 자산을 서버로 복사하는 워크플로를 수행합니다. 

## <a name="configure-a-proxy-server"></a>프록시 서버 구성

역방향 프록시는 동적 웹 응용 프로그램을 지원하기 위한 일반적인 설정입니다. 역방향 프록시는 HTTP 요청을 종료하고 이 요청을 ASP.NET 응용 프로그램에 전달합니다.

프록시 서버는 클라이언트 요청을 자체 수행하는 대신 다른 서버에 전달하는 서버입니다. 역방향 프록시는 일반적으로 임의의 클라이언트 대신 고정 대상에 전달됩니다. 이 가이드에서 Apache는 Kestrel이 ASP.NET Core 응용 프로그램을 제공하는 동일한 서버에서 실행되는 역방향 프록시로 구성됩니다. 

응용 프로그램의 각 조각은 별도 물리적 컴퓨터, Docker 컨테이너 또는 아키텍처 요구나 제한 사항에 따라 조합된 구성에 존재할 수 있습니다.

### <a name="install-apache"></a>Apache 설치

CentOS에 Apache 웹 서버를 설치하는 작업은 단일 명령이지만 먼저 패키지를 업데이트하겠습니다.

```bash
    sudo yum update -y
```

이렇게 하면 모든 설치된 패키지를 최신 버전으로 업데이트할 수 있습니다. `yum`을 사용하여 Apache 설치

```bash
    sudo yum -y install httpd mod_ssl
```

출력은 다음과 비슷한 내용을 반영해야 합니다.

```bash
    Downloading packages:
    httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01     
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
    Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
    Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

    Installed:
    httpd.x86_64 0:2.4.6-40.el7.centos.4                                                                           

    Complete!
```

> [!NOTE]
> 이 예제에서 CentOS 7 버전이 64비트이기 때문에 출력은 httpd.86_64를 반영합니다. 출력은 서버에 따라 달라질 수 있습니다. Apache를 설치한 위치를 확인하려면 명령 프롬프트에서 `whereis httpd`를 실행합니다. 

### <a name="configure-apache-for-reverse-proxy"></a>역방향 프록시에 Apache 구성

Apache의 구성 파일은 `/etc/httpd/conf.d/` 디렉터리 내에 위치합니다. `/etc/httpd/conf.modules.d/`의 모듈 구성 파일 외에도 **.conf** 확장을 포함한 모든 파일은 알파벳순으로 처리됩니다. 여기에는 모듈을 로드하는 데 필요한 구성 파일도 포함됩니다.

앱에 대한 구성 파일을 만듭니다. 이 예제에서는 `hellomvc.conf`라고 합니다.

```text
    <VirtualHost *:80>
        ProxyPreserveHost On
        ProxyPass / http://127.0.0.1:5000/
        ProxyPassReverse / http://127.0.0.1:5000/
        ErrorLog /var/log/httpd/hellomvc-error.log
        CustomLog /var/log/httpd/hellomvc-access.log common
    </VirtualHost>
```

하나의 파일이나 여러 파일의 서버에 여러 개가 있을 수 있는 *VirtualHost* 노드는 포트 80을 사용하여 모든 IP 주소에서 수신하도록 설정됩니다. 다음 두 줄은 루트에서 받은 모든 요청을 컴퓨터 127.0.0.1 5000 포트에 전달하고 반대로 수행하도록 설정됩니다. 양방향 통신이 있는 경우 *ProxyPass* 및 *ProxyPassReverse* 설정이 모두 필요합니다.

로깅은 *ErrorLog* 및 *CustomLog* 지시문을 사용하여 VirtualHost별로 구성될 수 있습니다. *ErrorLog*는 서버가 오류를 기록하는 위치이고 *CustomLog*는 파일 이름 및 로그 파일의 형식을 설정합니다. 이 경우에는 요청 정보를 기록할 위치입니다. 각 요청에 하나의 줄이 있습니다.

파일을 저장하고 구성을 테스트합니다. 모든 항목이 통과하는 경우 응답은 `Syntax [OK]`이어야 합니다.

```bash
    sudo service httpd configtest
```

Apache를 다시 시작합니다.

```text
    sudo systemctl restart httpd
    sudo systemctl enable httpd
```

## <a name="monitoring-our-application"></a>응용 프로그램 모니터링

이제 Apache는 `http://localhost:80`에 대해 만들어진 요청을 `http://127.0.0.1:5000`의 Kestrel에서 실행되는 ASP.NET Core 응용 프로그램에 전달하도록 설정됩니다.  그러나 Apache는 Kestrel 프로세스를 관리하도록 설정되지 않습니다. *systemd*를 사용하고 서비스 파일을 만들어 기본 웹앱을 시작하고 모니터링합니다. *systemd*는 프로세스를 시작, 중지 및 관리하기 위한 다양하고 강력한 기능을 제공하는 init 시스템입니다. 


### <a name="create-the-service-file"></a>서비스 파일 만들기

서비스 정의 파일을 만듭니다. 

```bash
    sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

응용 프로그램에 대한 예제 서비스 파일입니다.

```text
[Unit]
    Description=Example .NET Web API Application running on CentOS 7

    [Service]
    WorkingDirectory=/var/aspnetcore/hellomvc
    ExecStart=/usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
    Restart=always
    # Restart service after 10 seconds if dotnet service crashes
    RestartSec=10
    SyslogIdentifier=dotnet-example
    User=apache
    Environment=ASPNETCORE_ENVIRONMENT=Production 

    [Install]
    WantedBy=multi-user.target
```

> [!NOTE]
> **사용자** -- 사용자 *apache*가 구성에서 사용되지 않을 경우 여기서 정의된 사용자를 먼저 만들고 파일에 대한 적절한 소유권을 제공해야 합니다.

파일을 저장하고 서비스를 사용하도록 설정합니다.

```bash
    systemctl enable kestrel-hellomvc.service
```

서비스를 시작하고 실행 중인지 확인합니다.

```
    systemctl start kestrel-hellomvc.service
    systemctl status kestrel-hellomvc.service

    ● kestrel-hellomvc.service - Example .NET Web API Application running on CentOS 7
        Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
        Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
    Main PID: 9021 (dotnet)
        CGroup: /system.slice/kestrel-hellomvc.service
                └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

역방향 프록시를 구성하고 systemd를 통해 Kestrel을 관리하면 웹 응용 프로그램이 완전히 구성되고 로컬 컴퓨터(`http://localhost`)의 브라우저에서 웹 응용 프로그램에 액세스할 수 있습니다. 응답 헤더를 검사하는 **서버**는 Kestrel에서 지원하는 ASP.NET Core 응용 프로그램을 보여줍니다.

```text
    HTTP/1.1 200 OK
    Date: Tue, 11 Oct 2016 16:22:23 GMT
    Server: Kestrel
    Keep-Alive: timeout=5, max=98
    Connection: Keep-Alive
    Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a>로그 보기

Kestrel을 사용하는 웹 응용 프로그램은 systemd를 사용하여 관리되므로 모든 이벤트 및 프로세스가 중앙형 저널에 기록됩니다. 그러나 이 저널에는 systemd에서 관리하는 모든 서비스 및 프로세스에 대한 모든 항목이 포함됩니다. `kestrel-hellomvc.service` 관련 항목을 보려면 다음 명령을 사용합니다.

```bash
    sudo journalctl -fu kestrel-hellomvc.service
```

추가 필터링을 위해 `--since today`, `--until 1 hour ago` 같은 시간 옵션이나 이러한 옵션의 조합을 사용하여 반환되는 항목 수를 줄일 수 있습니다.

```bash
    sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-our-application"></a>응용 프로그램 보안

### <a name="configure-firewall"></a>방화벽 구성

*Firewalld*는 네트워크 영역에 대한 지원을 사용하여 방화벽을 관리하는 동적 디먼입니다. 그래도 아직 iptables를 사용하여 포트 및 패킷 필터링을 관리할 수 있습니다. 기본적으로 Firewalld를 설치해야 합니다. `yum`은 패키지를 설치하거나 확인하는 데 사용할 수 있습니다.

```bash
    sudo yum install firewalld -y
```

`firewalld`를 사용하여 응용 프로그램에 필요한 포트를 열 수 있습니다. 이 경우에는 포트 80 및 443을 사용합니다. 다음 명령은 이러한 항목이 영구적으로 열리도록 설정합니다.

```bash
    sudo firewall-cmd --add-port=80/tcp --permanent
    sudo firewall-cmd --add-port=443/tcp --permanent
```

방화벽 설정을 다시 로드하고 기본 영역에서 사용 가능한 서비스 및 포트를 확인합니다. `firewall-cmd -h`를 검사하여 옵션을 사용할 수 있습니다.

```bash 
    sudo firewall-cmd --reload
    sudo firewall-cmd --list-all
```

```bash
    public (default, active)
    interfaces: eth0
    sources: 
    services: dhcpv6-client
    ports: 443/tcp 80/tcp
    masquerade: no
    forward-ports: 
    icmp-blocks: 
    rich rules: 
```

### <a name="ssl-configuration"></a>SSL 구성

SSL에 Apache를 구성하려면 mod_ssl 모듈을 사용합니다.  이 항목은 `httpd` 모듈을 설치할 때 처음으로 설치됩니다. 이 항목이 누락되거나 설치되지 않은 경우 yum을 사용하여 구성에 추가합니다.

```bash
    sudo yum install mod_ssl
```
SSL을 적용하려면 `mod_rewrite`를 설치합니다.

```bash
    sudo yum install mod_rewrite
```

이 예제에서 만든 `hellomvc.conf` 파일을 수정하여 HTTPS에 새 **VirtualHost** 섹션을 추가할 뿐만 아니라 다시 쓰기를 사용할 수 있어야 합니다.

```text
    <VirtualHost *:80>
        RewriteEngine On
        RewriteCond %{HTTPS} !=on
        RewriteRule ^/?(.*) https://%{SERVER_NAME}/ [R,L]
    </VirtualHost>

    <VirtualHost *:443>
        ProxyPreserveHost On
        ProxyPass / http://127.0.0.1:5000/
        ProxyPassReverse / http://127.0.0.1:5000/
        ErrorLog /var/log/httpd/hellomvc-error.log
        CustomLog /var/log/httpd/hellomvc-access.log common
        SSLEngine on
        SSLProtocol all -SSLv2
        SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
        SSLCertificateFile /etc/pki/tls/certs/localhost.crt
        SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
    </VirtualHost>    
```

> [!NOTE]
> 이 예에서는 로컬로 생성된 인증서를 사용합니다. **SSLCertificateFile**은 도메인 이름에 대한 기본 인증서 파일이어야 합니다. **SSLCertificateKeyFile**은 CSR을 만들 때 생성된 키 파일이어야 합니다. **SSLCertificateChainFile**은 인증 기관에서 제공된 중간 인증서 파일(있는 경우)이 있어야 합니다.

파일을 저장하고 구성을 테스트합니다.

```bash
    sudo service httpd configtest
```

Apache를 다시 시작합니다.

```bash
    sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>추가 Apache 제안

### <a name="additional-headers"></a>추가 헤더 
악의적인 공격으로부터 보호하기 위해 몇 가지 헤더를 수정하거나 추가해야 합니다. `mod_headers` 모듈이 설치되었는지 확인합니다.

```bash
    sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking"></a>클릭재킹(clickjacking)으로부터 Apache 보호
클릭재킹은 감염된 사용자의 클릭을 수집하는 악의적인 기술입니다. 클릭재킹은 희생자(방문자)를 속여서 감염된 사이트를 클릭하게 합니다. X-FRAME-OPTIONS를 사용하여 사이트를 보호합니다.

httpd.conf 파일을 편집합니다.

```bash
    sudo nano /etc/httpd/conf/httpd.conf
```

`Header append X-FRAME-OPTIONS "SAMEORIGIN"` 줄을 추가하고 파일을 저장한 다음 Apache를 다시 시작합니다.

#### <a name="mime-type-sniffing"></a>MIME 형식 검색

이 헤더는 응답 콘텐츠 형식을 재정의하지 않도록 브라우저에 지시하므로 Internet Explorer가 선언된 콘텐츠 형식이 아닌 응답을 검색하는 MIME을 차단합니다. nosniff 옵션을 사용하여 서버에 콘텐츠가 text/html이라고 표시되는 경우 브라우저는 콘텐츠를 text/html로 렌더링합니다.

httpd.conf 파일을 편집합니다.

```bash
    sudo nano /etc/httpd/conf/httpd.conf
```

`Header set X-Content-Type-Options "nosniff"` 줄을 추가하고 파일을 저장한 다음 Apache를 다시 시작합니다.

### <a name="load-balancing"></a>부하 분산 

이 예제에서는 동일한 인스턴스 컴퓨터에서 CentOS 7와 Kestrel의 Apache를 설정하고 구성하는 방법을 보여줍니다.  그러나 단일 실패 지점이 없도록 하기 위해 *mod_proxy_balancer*를 사용하고 VirtualHost를 수정하면 Apache 프록시 서버 뒤에 있는 웹 응용 프로그램의 여러 인스턴스를 관리할 수 있습니다.

```bash
    sudo yum install mod_proxy_balancer
```

구성 파일에서 `hellomvc` 앱의 추가 인스턴스는 5001 포트에서 실행되도록 설정되었고 *프록시* 섹션은 *byrequests* 부하 분산에 대해 두 개의 멤버가 포함된 분산 장치 구성으로 설정되었습니다.

```text
    <VirtualHost *:80>
        RewriteEngine On
        RewriteCond %{HTTPS} !=on
        RewriteRule ^/?(.*) https://%{SERVER_NAME}/ [R,L]
    </VirtualHost>

    <VirtualHost *:443>
            ProxyPass / balancer://mycluster/ 

            ProxyPassReverse / http://127.0.0.1:5000/
            ProxyPassReverse / http://127.0.0.1:5001/

            <Proxy balancer://mycluster>
                BalancerMember http://127.0.0.1:5000
                BalancerMember http://127.0.0.1:5001 
                ProxySet lbmethod=byrequests
            </Proxy>

            <Location />
                SetHandler balancer
            </Location>
            ErrorLog /var/log/httpd/hellomvc-error.log
            CustomLog /var/log/httpd/hellomvc-access.log common
            SSLEngine on
            SSLProtocol all -SSLv2
            SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
            SSLCertificateFile /etc/pki/tls/certs/localhost.crt
            SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
    </VirtualHost>
```

### <a name="rate-limits"></a>속도 제한
`htttpd` 모듈에 포함되어 있는 `mod_ratelimit`를 사용하여 클라이언트의 대역폭 양을 제한할 수 있습니다. 

```bash
    sudo nano /etc/httpd/conf.d/ratelimit.conf
```
예제 파일은 루트 위치 아래에서 600KB/초로 대역폭을 제한합니다.

```text
    <IfModule mod_ratelimit.c>
        <Location />
            SetOutputFilter RATE_LIMIT
            SetEnv rate-limit 600
        </Location>
    </IfModule>
```
