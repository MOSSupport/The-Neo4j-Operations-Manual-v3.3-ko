
## 2.2.3. Neo4j 시스템 서비스

> 이 섹션에서는 Neo4j 시스템 서비스를 설정하고 작동하는 방법에 대해 다룹니다. 대부분의 리눅스 배포판이 그러하듯이 시스템에 ```systemd``` 가 있다고 가정합니다. 

### 2.2.3.1. 환경 설정

설정은 /etc/neo4j/neo4j.conf에서 확인할 수 있습니다. 다양한 패키지에 대한 파일 위치 관련 내용은 [섹션 3.1,"파일 위치"](../../configuration/file-locations.md)을 참조하면 됩니다. 

### 2.2.3.2. 시스템 시작에서 자동으로 서비스 실행

RPM 패키지 설치 후 Neo4j를 시스템 부트에서 자동으로 시작하려면 서비스를 활성화해야 합니다. Debian-기반 배포판 설치 시 이 작업은 자동으로 완료됩니다. 

```
systemctl enable neo4j
```

### 2.2.3.3. 서비스 통제

시스템 서비스는 ```systemctl ```명령어로 제어됩니다. 이것은 다양한 명령어를 수용합니다.

```
systemctl {start|stop|restart} neo4j
```

서비스 커스터마이징은 서비스 오버라이드 파일에 위치할 수 있습니다. 특정 옵션을 편집하려면, 아래 명령어를 사용하여 해당 파일 편집기를 엽니다.:

```
systemctl edit neo4j
```

그 후, ```[service]```섹션 아래에 커스터마이징을 배치합니다. 아래 예는 일부 유저에게 변경 될 수있는 기본값을 나열합니다.

```
[Service]
# The user and group which the service runs as.
User=neo4j
Group=neo4j
# If it takes longer than this then the shutdown is considered to have failed.
# This may need to be increased if the system serves long-running transactions.
TimeoutSec=120
```

가능한 오버라이드를 포함하여 효율적인 서비스를 출력할 수 있습니다.:

```
systemctl cat neo4j
```

설정을 변경했으면 Neo4j를 다시 시작해야합니다. 

```
systemctl restart neo4j
```

### 2.2.3.4. Log

Neo4j 로그는 ```journalctl ```명령어를 사용하여 볼 수 있고 ```journald```에 기록됩니다.

```
journalctl -e -u neo4j
```

```journald```는 일정 시간 후에 로그를 자동으로 회전시키며 기본적으로 재부팅할 때마다 유지되지 않습니다. 자세한 사항은 ```man journald.conf```을 참조하면 됩니다. 

