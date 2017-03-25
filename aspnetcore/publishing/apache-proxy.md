---
title: "Apache 웹 서버를 사용 하 여 역방향 프록시로 | Microsoft 문서"
description: "Apache 역방향 프록시 서버 설정 CentOS Kestrel에서 실행 중인 ASP.NET 핵심 웹 응용 프로그램에 HTTP 트래픽을 리디렉션할 수에 대해 설명 합니다."
keywords: "ASP.NET Core, Apache, CentOS, 역방향 프록시, Linux, mod_proxy, httpd, 호스팅"
author: spboyer
ms.author: spboyer
manager: wpickett
ms.date: 10/19/2016
ms.topic: article
ms.assetid: fa9b0cb7-afb3-4361-9e7e-33afffeaca0c
ms.technology: aspnet
ms.prod: asp.net-core
uid: publishing/apache-proxy
translationtype: Machine Translation
ms.sourcegitcommit: 010b730d2716f9f536fef889bc2f767afb648ef4
ms.openlocfilehash: 3111d03fbacc0d8d66b127a4c5c07ed4c1136d24
ms.lasthandoff: 03/23/2017

---
# <a name="using-apache-web-server-as-a-reverse-proxy"></a>Apache 웹 서버를 사용 하 여 역방향 프록시로

[Shayne 보이 어](https://www.github.com/spboyer)

Apache는 매우 널리 사용 되는 HTTP 서버 및 nginx 비슷합니다 HTTP 트래픽을 리디렉션할 수 프록시로 구성할 수 있습니다. 이 가이드에서는 CentOS 7의 Apache 설정으로 사용 하 여 역방향 프록시 Kestrel에서 실행 중인 ASP.NET 핵심 응용 프로그램에 리디렉션하거나 및 들어오는 연결을 시작 하는 방법에 설명 합니다. 이 작업을 위해 사용 합니다는 *mod_proxy* 확장 및 기타 관련 Apache 모듈입니다.

## <a name="prerequisites"></a>필수 조건

1. Sudo 권한이 있는 표준 사용자 계정으로 CentOS 7을 실행 하는 서버입니다.
2. 기존 ASP.NET 핵심 응용 프로그램입니다. 

## <a name="publish-your-application"></a>응용 프로그램 게시

실행 `dotnet publish -c Release` 서버에서 실행할 수 있는 자체 포함 된 디렉터리에 응용 프로그램 패키지를 개발 환경에서. 게시 된 응용 프로그램 다음 SCP, FTP 또는 다른 파일 전송 방법을 사용 하 여 서버에 복사 해야 합니다. 

> [!NOTE]
> 프로덕션 배포 시나리오에서 연속 통합 워크플로 자산을 서버에 복사 하 고 응용 프로그램을 게시 한 작업을 수행 합니다. 

## <a name="configure-a-proxy-server"></a>프록시 서버를 구성 합니다.

역방향 프록시는 동적 웹 응용 프로그램을 처리 하기 위한 일반적인 설치 합니다. 역방향 프록시는 HTTP 요청을 종료 하 고 ASP.NET 응용 프로그램에 전달 합니다.

프록시 서버는 자체 수행 하는 대신 다른 서버에 대 한 클라이언트 요청을 전달 하는 하나입니다. 역방향 프록시를 임의의 클라이언트 대신 일반적으로 고정된 대상에 전달합니다. 이 가이드에서는 Apache Kestrel ASP.NET 핵심 응용 프로그램 서비스는 동일한 서버에서 실행 되는 역방향 프록시도 구성 됩니다. 

응용 프로그램의 각 조각은 별도 물리적 컴퓨터, Docker 컨테이너 또는 아키텍처 요구 사항이 나 제한 사항에 따라 구성의 조합에 존재할 수 있습니다.

### <a name="install-apache"></a>Apache 설치

먼저 하는 단일 명령 보겠습니다 CentOS에 Apache 웹 서버를 설치 프로그램 패키지를 업데이트 합니다.

```bash
    sudo yum update -y
```

이렇게 하면 모든 설치 된 패키지의 최신 버전으로 업데이트 됩니다. 사용 하 여 Apache를 설치 합니다.`yum`

```bash
    sudo yum -y install httpd mod_ssl
```

출력에는 다음과 유사 하 게 반영 해야 합니다.

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
> 이 예제에서는 출력 CentOS 7 버전은 64 비트 이므로 httpd.86_64를 반영 합니다. 출력은 서버에 대해 다 수 있습니다. Apache 설치 된 위치를 확인 하려면 실행 `whereis httpd` 명령 프롬프트에서. 

### <a name="configure-apache-for-reverse-proxy"></a>Apache 역방향 프록시에 대 한 구성

Apache 구성 파일은 내에 `/etc/httpd/conf.d/` 디렉터리입니다. 인 파일은 **.conf** 확장에서 모듈 구성 파일 뿐 아니라 사전순으로 처리 됩니다 `/etc/httpd/conf.modules.d/`, 구성이 포함 된 모듈을 로드 하는 데 필요한 파일입니다.

이 예제에서는 이름을 앱에 대 한 구성 파일 만들기`hellomvc.conf`

```text
    <VirtualHost *:80>
        ProxyPreserveHost On
        ProxyPass / http://127.0.0.1:5000/
        ProxyPassReverse / http://127.0.0.1:5000/
        ErrorLog /var/log/httpd/hellomvc-error.log
        CustomLog /var/log/httpd/hellomvc-access.log common
    </VirtualHost>
```

*VirtualHost* 는 있을 수 있습니다 여러 노드를 파일 또는 여러 파일에서 서버에 포트 80을 사용 하 여 모든 IP 주소에서 수신 대기 하도록 설정 됩니다. 다음 두 줄은 컴퓨터 127.0.0.1 포트 5000 및 역방향에서 루트에서 수신 하는 모든 요청을 전달로 설정 됩니다. -양방향 통신을 모두 설정 경우도 *ProxyPass* 및 *ProxyPassReverse* 필요 합니다.

로깅 VirtualHost 당 구성할 수 있습니다를 사용 하 여 *ErrorLog* 및 *CustomLog* 지시문입니다. *오류 로그* 는 서버 오류를 기록 하는 위치 및 *CustomLog* 파일 이름 및 로그 파일의 형식을 설정 합니다. 이 경우 요청 정보가 로깅됩니다. 각 요청에 대 한 한 줄이 됩니다.

파일을 저장 하는 구성을 테스트 합니다. 모든 항목에 통과 하는 경우 응답 있어야 `Syntax [OK]`합니다.

```bash
    sudo service httpd configtest
```

Apache를 다시 시작 합니다.

```text
    sudo systemctl restart httpd
    sudo systemctl enable httpd
```

## <a name="monitoring-our-application"></a>응용 프로그램 모니터링

Apache에 대 한 요청을 전달 하도록 설정 되어 이제 `http://localhost:80` 에서 Kestrel에서 실행 중인 ASP.NET 핵심 응용 프로그램에 로그온 `http://127.0.0.1:5000`합니다.  그러나 Apache로 설정 되지 Kestrel 프로세스를 관리할 수 있습니다. 사용 하 여 *systemd* 을 시작 하 고 기본 웹 앱 모니터링 서비스 파일을 만듭니다. *systemd* 는 시작, 중지, 프로세스 관리에 대 한 여러 가지 강력한 기능을 제공 하는 init 시스템입니다. 


### <a name="create-the-service-file"></a>서비스 파일 만들기

서비스 정의 파일 만들기 

```bash
    sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

응용 프로그램에 대 한 예제 서비스 파일입니다.

```text
[Unit]
    Description=Example .NET Web API Application running on CentOS 7

    [Service]
    WorkingDirectory=/var/aspnetcore/hellomvc
    ExecStart=/usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
    Restart=always
    RestartSec=10                                          # Restart service after 10 seconds if dotnet service crashes
    SyslogIdentifier=dotnet-example
    User=apache
    Environment=ASPNETCORE_ENVIRONMENT=Production 

    [Install]
    WantedBy=multi-user.target
```

> [!NOTE]
> **사용자** --사용자 *apache* 사용 되지 않는 구성에 따라 여기에 정의 된 사용자를 만든 다음 먼저 파일에 대 한 적절 한 소유권이 부여

파일을 저장 하 고 서비스를 사용 하도록 설정 합니다.

```bash
    systemctl enable kestrel-hellomvc.service
```

서비스를 시작 하 고 실행 중인지 확인 합니다.

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

### <a name="configure-firewall"></a>방화벽 구성

*Firewalld* 네트워크 영역에 대 한 지원과 함께 방화벽을 관리 하는 동적 디먼은 iptables 관리 포트 및 패킷 필터링을 사용할 수 있습니다. 기본적으로 설치 해야 Firewalld `yum` 패키지를 설치 하거나 확인 하는 데 사용 될 수 있습니다.

```bash
    sudo yum install firewalld -y
```

사용 하 여 `firewalld` 만 응용 프로그램에 필요한 포트를 열 수 있습니다. 이 경우 포트 80 및 443이 사용 됩니다. 다음 명령을 열려면 이러한에 영구적으로 설정 합니다.

```bash
    sudo firewall-cmd --add-port=80/tcp --permanent
    sudo firewall-cmd --add-port=443/tcp --permanent
```

방화벽 설정을 다시 로드 하 고 사용 가능한 서비스 및 기본 영역에는 포트를 확인 합니다. 검사 하 여 옵션을 사용할 수`firewall-cmd -h`

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

Apache ssl을 구성 하려면 mod_ssl 모듈 사용 됩니다.  이 처음 설치할 때 설치 우리는 `httpd` 모듈입니다. 그 누락 되었거나 설치 되지 않은, yum를 사용 하 여 구성에 추가 합니다.

```bash
    sudo yum install mod_ssl
```
SSL을 적용 하려면 설치`mod_rewrite`

```bash
    sudo yum install mod_rewrite
```

`hellomvc.conf` 이 예제에서는 새 추가할 뿐 아니라 다시 작성할 수 있도록 수정 해야 합니다.에 만든 파일을 **VirtualHost** HTTPS에 대 한 섹션입니다.

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
> 이 예제에서는 로컬에서 생성 된 인증서를 사용 하는 합니다. **SSLCertificateFile** 도메인 이름에 대 한 기본 인증서 파일 이어야 합니다. **SSLCertificateKeyFile** CSR을 만들 때 생성 된 키 파일 이어야 합니다. **SSLCertificateChainFile** (있는 경우) 중간 인증서 파일이 있어야 하는 인증 기관에서 제공한

파일을 저장 하는 구성을 테스트 합니다.

```bash
    sudo service httpd configtest
```

Apache를 다시 시작 합니다.

```bash
    sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>추가 Apache 제안

### <a name="additional-headers"></a>추가 헤더 
으로부터 보호 하기 위해 악의적인 공격에 있을 수 수정 하거나 추가 하는 몇 가지 헤더는. 확인 하는 `mod_headers` 모듈이 설치 됩니다.

```bash
    sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking"></a>Apache clickjacking에서 보안
Clickjacking은 감염된 된 사용자의 클릭을 수집 하는 악성 기술입니다. Clickjacking 가장 하도록 한 다음 감염된 된 사이트를 클릭 하면 교착 상태가 발생 (방문자). X-프레임-옵션 사용 하면 사이트의 보안입니다.

Httpd.conf 파일을 편집 합니다.

```bash
    sudo nano /etc/httpd/conf/httpd.conf
```

추가 된 줄 `Header append X-FRAME-OPTIONS "SAMEORIGIN"` 하 고 파일을 저장 한 다음 Apache를 다시 시작 합니다.

#### <a name="mime-type-sniffing"></a>MIME 형식 검사

이 헤더 MIME 스니핑에서 Internet Explorer에서 선언 된 content-type 응답 헤더 응답 콘텐츠 형식을 재정의할 수는 없습니다 브라우저에 지시 방지 합니다. Nosniff 옵션을 서버는 콘텐츠는 text/html 표시 되 면 브라우저는 렌더링 text/html로.

Httpd.conf 파일을 편집 합니다.

```bash
    sudo nano /etc/httpd/conf/httpd.conf
```

추가 된 줄 `Header set X-Content-Type-Options "nosniff"` 하 고 파일을 저장 한 다음 Apache를 다시 시작 합니다.

### <a name="load-balancing"></a>부하 분산 

이 예제에는 설정 및 CentOS 7 및 Kestrel에서 동일한 인스턴스가 컴퓨터에 Apache를 구성 하는 방법을 보여 줍니다.  그러나 단일 실패 지점이 없는 하기 위해서 사용 하 여 *mod_proxy_balancer* Apache 프록시 서버 뒤에 웹 응용 프로그램의 여러 인스턴스를 관리 하면는 VirtualHost를 수정 하 고 있습니다.

```bash
    sudo yum install mod_proxy_balancer
```

다른 인스턴스를 구성 파일에는 `hellomvc` 5001 포트에서 실행 되도록 설치 된 응용 프로그램 및 *프록시* 섹션 부하를 분산 두 멤버가 포함 된 분산 장치 구성으로 설정 되었고 *byrequests*합니다.

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
사용 하 여 `mod_ratelimit`에 포함 되어 있는 `htttpd` 모듈의 클라이언트는 대역폭의 양을 제한할 수 있습니다. 

```bash
    sudo nano /etc/httpd/conf.d/ratelimit.conf
```
예제 파일은 루트 위치 아래의 600 k B/초도 대역폭을 제한합니다.

```text
    <IfModule mod_ratelimit.c>
        <Location />
            SetOutputFilter RATE_LIMIT
            SetEnv rate-limit 600
        </Location>
    </IfModule>
```

