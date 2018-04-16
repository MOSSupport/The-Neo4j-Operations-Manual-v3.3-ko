
## 2.4 Windows 설치

> 이 챕터에서는 Windows에서 Neo4j가 리눅스에서 어떻게 작용되는지 살펴봅니다. 

Neo4j는 콘솔 어플리케이션이나 윈도우 서비스로 작동할 수도 있습니다. 

개발하는 경우, Neo4j는 Neo4j 데스크 톱으로 작동되기도 합니다.


### 2.4.1 Neo4j 데스크톱 

http://neo4j.com/download/]("http://neo4j.com/download/")에 접속한 후, 
컴퓨터 내 Neo4j 데스크톱 지시 사항을 따라 가십시오.


### 2.4.2 Windows 콘솔 어플리케이션

   1. 새롭게 출시된 버전을  http://neo4j.com/download/ 에서 받으세요.

   2. 우측의 다운로드 파일을 택한 후, 모두 압출을 클릭하세요.

   3. 디렉토리를 추출된 상위 디렉토리로 변경하세요. 
      그 후,  ```bin\neo4j console```을 실행하세요.


   4. 콘솔 창에 ```Ctrl-C```를 입력하여 서버를 종료하세요.


### 2.4.3 Windows 서비스

Neo4j는 윈도우 서비스에서도 작동될 수 있습니다. 서비스를 ```bin\neo4j install-service``` 와 함께 설치하십시오. 
그리고, ```bin\neo4j start```로 시작하십시오.
```stop```, ```restart```, ```status```, 그리고 ```uninstall-service``` 명령어도 사용 가능합니다. 



### 2.4.4 Windows PowerShell (파워쉘) 모듈

관리자는 Neo4j 파워쉘 모듈에서 다음의 역할을 합니다.:

+ Neo4j Windows® Services 를 설치, 시작, 정지시킨다.
+ ```Neo4j Shell``` 과 ```Neo4j Import```와 같은 툴을 작동시킨다.

Powershell 모듈은 Neo4j의 [ZIP file](https://neo4j.com/download/) 배포판 일부로 설치됩니다. 


#### 2.4.4.1 시스템 요구사항

+ PowerShell v2.0 또는 그 이상이 필요합니다.
+ 32 또는 64 비트 운영체제 시스템에서 지원됩니다. 



#### 2.4.4.2 윈도우에서 Neo4j 관리리하기

Windows에서 모듈로 가져오기전에 때때로 zip 파일 차단을 해제할 경우가 있습니다. zip 파일에서 마우스 우측을 클릭하고, "Properties"을 선택하면 "Unblock" 버튼을 포함하는 박스를볼 수 있습니다. 여기서, 모듈을 할당할 수 있습니다.


스크립트 실행은 시스템에서 이용할 수 있어야 합니다. 예를들면 높은 PowerShell에서 이것을 실행하면 됩니다. 

```Set-ExecutionPolicy -ExecutionPolicy RemoteSigned```

자세한 내용은 [실행 관련 내용]("https://docs.microsoft.com/ko-kr/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6&viewFallbackFrom=powershell-Microsoft.PowerShell.Core")을 참조하면 됩니다. 

관리자 권한이 없다면, PowerShell 모듈에서 경고를 표시합니다. 


#### 2.4.4.3. 모듈을 임포트하는 방법

모든 파일은 Neo4j가 설치된 bin 디렉토리, 즉 다운로드 한 파일을 압축한 곳에 있습니다. 예를 들어, Neo4j가 C:\Neo4j에 설치되어 있다면 모듈은 다음과 같이 임포트될 것 입니다.:

```
Import-Module C:\Neo4j\bin\Neo4j-Management.psd1
```

이것을 모듈의 현재 세션에 추가할 것 입니다. 

모듈이 임포트 되면, Neo4j 서버의 상호 콘솔 버션을 아래와 같이 실행할 수 있습니다.:

```
Invoke-Neo4j console
```

서버를 멈추려면 명령어가 생성한 콘솔 창에 ```Ctrl-C``` 을 실행하면 됩니다. 


#### 2.4.4.4. 모듈에서 정보를 얻기 위해서는?

한 번 모듈이 임포트 되면, 다음과 같이 가능한 명령을 쿼리할 수 있습니다. 

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

+ 상세한 출력으로 서비스 설치:

```
Invoke-Neo4j install-service -Verbose
```

+ 관리 작업에 쓸 수 있는 명령:

```
Invoke-Neo4jAdmin
```


#### 2.4.4.6. 공통 PowerShell 변수

모듈 명령어는 공통 PowerShell에서 ```Verbose```변수를 쓸 수 있습니다. 