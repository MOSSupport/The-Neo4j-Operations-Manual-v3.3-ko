
## 2.4 Windows 설치

> 이 챕터에서는 Windows에서 Neo4j가 리눅스에서 어떻게 작용되는지 살펴봅니다. 

Neo4j는 콘솔 어플리케이션이나 윈도우 서비스로 작동할 수도 있습니다. 

개발하는 경우, Neo4j는 Neo4j 데스크 톱으로 작동되기도 합니다.


## 2.4.1 Neo4j 데스크톱 

   http://neo4j.com/download/ 에 접속한 후, 
   컴퓨터 내 Neo4j 데스크톱 지시 사항을 따라 가십시오.


## 2.4.2 Windows 콘솔 어플리케이션

   1. 새롭게 출시된 버전을  http://neo4j.com/download/ 에서 받으세요.

   2. 우측의 다운로드 파일을 택한 후, 모두 압출을 클릭하세요.

   3. 디렉토리를 추출된 상위 디렉토리로 변경하세요. 
      그 후, > bin\neo4j console 을 실행하세요.


   4. 콘솔 창에 > Ctrl-C를 입력하여 서버를 종료하세요.


## 2.4.3 Windows 서비스

   Neo4j는 윈도우 서비스에서도 작동될 수 있습니다. 서비스를 ```bin\neo4j install-service``` 와 함께 설치하십시오. 
   그리고, '''bin\neo4j start```로 시작하십시오.


   ```stop```, ```restart```, ```status```, 그리고 ```uninstall-service``` 명령어도 사용 가능합니다. 



## 2.4.4 Windows PowerShell (파워쉘) 모듈

  관리자는 Neo4j 파워쉘 모듈에서 다음의 역할을 합니다.:

  + Neo4j Windows® Services 를 설치, 시작, 정지시킨다.
  + ```Neo4j Shell``` 과 ```Neo4j Import```와 같은 툴을 작동시킨다.


 Powershell 모듈은 Neo4j의 [ZIP file](https://neo4j.com/download/) 배포판 일부로 설치됩니다. 


## 2.4.4.1 시스템 요구사항

   + PowerShell v2.0 또는 그 이상이 필요합니다.
   + 32 또는 64 비트 운영체제 시스템에서 지원됩니다. 



## 2.4.4.2 윈도우에서 Neo4j 관리리하기

   Windows에서 모듈로 가져오기전에 때때로 zip 파일 차단을 해제할 경우가 있습니다. zip 파일에서 마우스 우측을 클릭하고, "Properties"을 선택하면 "Unblock" 버튼을 포함하는 박스를볼 수 있습니다. 여기서, 모듈을 할당할 수 있습니다.


   스크립트 실행은 시스템에서 이용할 수 있어야 합니다. 예를들면, PowerShell 에 압축되어 실행하는 것이 