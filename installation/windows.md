
## 2.4 Windows 설치

> 이 챕터에서는 Windows에서 Neo4j가 어떻게 작용되는지 살펴봅니다. 

Neo4j는 콘솔 어플리케이션이나 윈도우 서비스로 작동할 수도 있습니다. 
개발하는 경우, Neo4j는 Neo4j 데스크 톱으로 작동되기도 합니다.


### 2.4.1 Neo4j 데스크톱 

[http://neo4j.com/download/](http://neo4j.com/download) 에 접속한 후, 
Neo4j 데스크톱 지시 사항을 따라 가십시오.


### 2.4.2 Windows 콘솔 어플리케이션

   1. 새롭게 출시된 버전을  http://neo4j.com/download/ 에서 받으세요.

   2. 우측의 다운로드 파일을 택한 후, 모두 압축을 클릭하세요.

   3. 디렉토리를 추출된 상위 디렉토리로 변경하세요. 
      그 후,  ```bin\neo4j console```을 실행하세요.


   4. 콘솔 창에 ```Ctrl-C```를 입력하여 서버를 종료하세요.


### 2.4.3 Windows 서비스

Neo4j는 윈도우 서비스에서도 작동될 수 있습니다. 서비스를 ```bin\neo4j install-service``` 와 함께 설치하고, ```bin\neo4j start```로 시작하십시오.

```bin\neo4j```에서 사용할 수 있는 명령어는 : ```help```, ```start```, ```stop```, ```restart```, ```status```, ```install-service```, ```uninstall-service``` 그리고 ```update-service```입니다. 

#### 2.4.3.1. 자바 옵션

Neo4j가 서비스로 설치되었다면 자바 옵션은 서비스 환경 설정에 저장됩니다. 서비스가 설치된 후 이 옵션의 변경 사항은 서비스 설정이 업데이트 되기 전까지 적용되지 않습니다. 예를 들어, [neo4j.conf](../configuration/file-locations.md) 내 ```dbms.memory.heap.max_size```설정을 변경하면 서비스가 업데이트 되고 재시작 되기 전까지 아무런 영향을 미치지 않습니다. 서비스를 업데이트하려면 ```bin\neo4j update-service```을 실행해야 합니다. 그 후, 서비스를 재시작하여 새로운 구성으로 서비스를 실행하면 됩니다. 

시스템에서 Java가 설치된 경로에 대해서도 동일하게 적용됩니다. 경로가 변경된다면 예를들어, 자바를 새 버전으로 업그레이드할 때 경로가 변경되면 ```update-service``` 명령어를 사용하고 서비스를 재시작해야 합니다. 그 후, 서비스는 새로운 자바 경로를 사용할 것 입니다. 

**예시 2.1. 업데이트 서비스**

1. 서비스 설치 
```
bin\neo4j install-service

```

2. 메모리 환경설정 변경
```
echo dbms.memory.heap.initial_size=8g >> conf\neo4j.conf
echo dbms.memory.heap.max_size=16g >> conf\neo4j.conf
```

3. 업데이트 서비스
```
bin\neo4j update-service
```

4. 서비스 재시작
```
bin\neo4j restart
```

### 2.4.4 Windows PowerShell (파워쉘) 모듈

관리자는 Neo4j 파워쉘 모듈에서 다음 역할을 합니다.:

+ Neo4j Windows® Services 를 설치, 시작, 정지시킨다.
+ 툴을 ```Neo4j Shell``` 과 ```Neo4j Import```와 같이 작동한다.

Powershell 모듈은 Neo4j의 [ZIP file](https://neo4j.com/download/) 배포판 일부로 설치됩니다. 


#### 2.4.4.1 시스템 요구사항

+ PowerShell v2.0 또는 그 이상이 필요합니다.
+ 32 또는 64 비트 운영체제 시스템에서 지원됩니다. 

#### 2.4.4.2 윈도우에서 Neo4j 관리리하기

Windows에서 모듈로 가져오기전에 때때로 zip 파일 다운로드 차단을 해제할 경우가 있습니다. zip 파일에서 마우스 우측을 클릭하고, "Properties"을 선택하면 "Unblock" 버튼을 포함하는 박스를 볼 수 있습니다. 여기서, 모듈을 할당할 수 있습니다.

시스템에서 스크립트를 실행할 수 있어야 합니다. 예를들어 높은 PowerShell에서 이것을 실행하면 됩니다. 

```Set-ExecutionPolicy -ExecutionPolicy RemoteSigned```

자세한 내용은 [실행 관련 내용]("https://docs.microsoft.com/ko-kr/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6&viewFallbackFrom=powershell-Microsoft.PowerShell.Core")을 참조하면 됩니다. 

관리자 권한이 없다면, PowerShell 모듈에서 경고를 표시합니다. 

#### 2.4.4.3. 모듈을 임포트하는 방법

모든 파일은 Neo4j가 설치된 bin 디렉토리, 즉 다운로드 파일을 압축한 곳에 있습니다. 예를 들어, Neo4j가 C:\Neo4j에 설치되어 있다면 모듈은 다음과 같이 임포트될 것 입니다.:

```
Import-Module C:\Neo4j\bin\Neo4j-Management.psd1
```

이것을 모듈의 현재 세션에 추가할 것 입니다. 

모듈이 임포트 되면, Neo4j 서버의 상호 콘솔 버션을 아래와 같이 실행할 수 있습니다.:

```
Invoke-Neo4j console
```

서버를 멈추려면 명령어가 생성한 콘솔 창에 ```Ctrl-C``` 을 입력하면 됩니다. 


#### 2.4.4.4. 모듈관련 헬프를 받는 방법

한 번 모듈이 임포트 되면, 다음 명령이 쿼리가 가능합니다. 

출력은 아래와 같이 출력됩니다. 

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Function        Invoke-Neo4j                        3.3.4      Neo4j-Management
Function        Invoke-Neo4jAdmin                   3.3.4      Neo4j-Management
Function        Invoke-Neo4jBackup                  3.3.4      Neo4j-Management
Function        Invoke-Neo4jImport                  3.3.4      Neo4j-Management
Function        Invoke-Neo4jShell                   3.3.4      Neo4j-Management
```

모듈에서 기본 PowerShell help 명령어도 사용할 수 있습니다. 

```
Get-Help Invoke-Neo4j
```

help 명령어 예시를 보기 위해서 다음을 실행하십시오.

```
Get-Help Invoke-Neo4j -examples
```

#### 2.4.4.5. 사용 예시

+ 가능한 명령어 리스트:

```
Invoke-Neo4j
```

+ Neo4j 서비스 현 상태:

```
Invoke-Neo4j status
```

+ 서비스 설치 자세히 출력:

```
Invoke-Neo4j install-service -Verbose
```

+ 관리 작업에 쓸 수 있는 명령:

```
Invoke-Neo4jAdmin
```


#### 2.4.4.6. 공통 PowerShell 변수

모듈 명령어는 공통 PowerShell에서 ```Verbose```변수를 쓸 수 있습니다. 
