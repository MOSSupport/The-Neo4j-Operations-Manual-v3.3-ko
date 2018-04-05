

## 2.5 도커

> 이 챕터에서는 도커 컨테이너에서 Neo4j가 리눅스에서 어떻게 작용되는지 살펴봅니다. 


도커는 OS X나 Windows에서 기본적으로 실행되지는 않습니다. 도커를 OS X와 Windows에서 작동시키기 위해서는 도커 설명서를 참조하십시오. 


## 2.5.1 개요

기본적으로 도커 이미지는 세 가지 원격 접속 포트를 나타냅니다.

	+ HTTP : ```7474``` 
	+ HTTPS : ```7473```
	+ Bolt : ```7687```


	또한, 다음 볼륨도 나타냅니다. 호스트 디렉토리를 ```--volume``` 옵션을 사용하여 마운트할 수 있습니다. 
	다른 배포 Neo4j에서 기본적으로 사용하는 디렉토리의 상세내용은 	[섹션 3.1, "File locations"]("https://neo4j.com/docs/operations-manual/current/configuration/file-locations/")에서 확인할 수 있습니다. 


	+	/conf
	+	/data
	+	/import
	+	/logs
	+	/metrics
	+	/plugins
	+	/ssl


	때론 데이터베이스와 로그를 컨테이너 밖에 두는 것이 바람직합니다. 다음 명령어는 컨테이너에서 Bolt와 Http 포트를위해 사용될 것 입니다. 그리고, /data와 /logs 볼륨은 호스트 디렉토리에 매핑됩니다. 

	!!! 이미지 !!!!


	리눅스의 ```http://localhost:7474```나 OS X의 ```http://$(docker-machine ip default):7474```에 접속하세요.


	이 문서에 있는 볼륨은 모두 OS X(VM머신에 ```$HOME```은 자동적으로 마운트된 곳에서 작업하기 위해 ```$HOME```하위에 저장되어 있습니다. 
	 

	 <span class="glyphicon glyphicon-info-sign" aria-hidden="true"></span> 
	기본적으로, Neo4j는 첫 접속 시 새 패스워드를 설정하기 위해 인증과 ```neo4j/neo4j```으로 로그인을 해야합니다. ```--env NEO4J_AUTH=neo4j/<password>```를 지정하여 Docker 컨테이너 비밀번호를 바로 설정할 수 있습니다. 또는 ```--env NEO4J_AUTH=none```을 명시하여 인증을 비활성화할 수 있습니다. 

 

	## 2.5.2 Neo4j 에디션


	테그는 Neo4j 커뮤니티와 엔터프라이즈 버전에서 모두 사용가능합니다. ```neo4j:3.3.0-enterprise```와 같이 특정 엔터프라이즈 버전 테그(tag)는 ```-enterprise```  suffix가 있습니다. 최근 Neo4j 버전은 ```neo4j:enterprise```로 사용할 수 있습니다. 

	

	## 2.5.2.1 Neo4j 에디션 라이센스


	Neo4j 에디션을 이용하기 위해서는 반드시 라이센스 동의를 해야합니다. 



	> (c) Network Engine for Objects in Lund AB. 2017. All Rights Reserved. Use of this Software without a proper commercial license with Neo4j, Inc. or its affiliates is prohibited.


	> 이메일 관련 문의 : licensing@neo4j.com

	> 더 많은 정보 관련 문의 : https://neo4j.com/licensing/



	라이센스에 동의하기 위해서 ```NEO4J_ACCEPT_LICENSE_AGREEMENT=yes``` 환경 변수를 설정해야 합니다. 이를 통해 다음 도큐먼트 인수를 사용할 수 있습니다. :


 	```--env=NEO4J_ACCEPT_LICENSE_AGREEMENT=yes'''


 	## 2.5.3 도커 환경 설정



 	# 2.5.3.1 파일 설명 제한

 	Neo4j는 많은 익덱스가 사용 중이거나, 데이터 베이스가 동시다발적으로 연결되어있다면 많은 파일 요약을 사욜할수 있습니다. 
 	도커는 컨테이너 내 많은 파일 요약을 통제합니다. 한계치는 개인의 환경 설정 시스템에 따라 다흐지만, Neoj4를 작동하는데 최소 40000개를 권장합니다. 


 	시스템 한계를 확인하기 위해서, 다음을 실행하십시오.:

 	!!!이미지!!!



 	싱글 컨테이너의 기본 설정을 재설정하기 위해서, 아래 ```--unlimit``` 옵션을 사용하십시오.:

 	!!!이미지!!!


 	##2.5.4 Neo4j 환경 설정

	이 이미지가 제공하는 구성은 Neo4j를 학습하기 위해 제작되었지만, 프로덕션 용에 알맞게 수정되어야 합니다. 특별히 Neo4j에 할당된 메모리는 같은 서버에서 다수의 콘테이너를 작동할 때 제한적 입니다.(아래의 ```NEO4J_CACHE_MEMORY```와 ```NEO4J_HEAP_MEMORY 참조) 

 
	환경 설정을 수정하는 3가지 방법이 잇습니다. :

 	+ 환경 변수 설정
 	+ /conf 볼륨 마운트
 	+ 새 이미지 생성



 	이 중에서 이미지를 얼마나 커스터마이징 하느냐에 따라 선택할 수 있습니다. 


 	##2.5.4.1 환경 변수 

 	작동할 때는 환경 변수를 컨테이너로 옮깁니다. 

 	!!!이미지 !!!


 	다음의 네이밍 제도를 이용해, 어떤 환경 설정 값도 전달 가능합니다. [Section A.1, "Configuration settings"](https://neo4j.com/docs/operations-manual/current/reference/configuration-settings/)


 	+ ```NEO4J_```를 접두에 사용.
 	+ 밑줄은 두번 적어야 됩ㅂ니다. : _는 __로 표시.
 	+ 점은 _로 대체합니다. : . 는 _로 표시.


 	예를 들어, ```dms.tx_log.rotation.size```는 다음 논증을 다라 설정됩니다. 


 	!!!이미지!!!


 	## Neo4J Enterprise Edition <code>Enterprise Edition</code>


 	남은 섹션에서는 Neo4j 엔터프라이즈 버전에서 이용가능한 환경 설정에 대해 다룹니다. 


 	+ ```NEO4J_dbms_mode``` : 데이터베이스 모드, ```SINGLE```의 기본, ```CORE``` 또는 일반적인 클러시터링을 위한 ```READ_REPLICA```, 고 클러스터링 이용을 위한 ```HA```.



 	## 인과 관계 클러스터 설정

 		+ ```NEO4J_causal__clustering_expected__core__cluster__siz``` : 초기 클러스터 사이즈(핵심 인스턴스 
 			 개수).

 		+ ```NEO4J_causal__clustering_initial__discovery__members``` : 핵심 클러스터 멤버의 초기 네트워크 	
 			 주소 세트.
 
 		+ ```NEO4J_causal__clustering_discovery__advertised__address``` : 호스트 이름/ IP 주소 그리고 
 			 멤버 검색 관리 통신을 알려주는 포트.

 		+ ```NEO4J_causal__clustering_transaction__advertised__address``` : 호스트 이름/ IP 주소와 			트랜잭션 처리를 알려주는 포트.

 		+ ```NEO4J_causal__clustering_raft__advertised__address``` : 클러스터 통신을 위해 호스트 이름 / 
 			 IP 주소를 알려주는 포트 


 	 인과관계 클러스터링을 설정하는 예를 아래에서 확인할 수 있습니다. 



 	 	## 고 가용 클러스터 설정

 	 		+ ```NEO4J_ha_server__id``` : 서버 ID는 클러스트 내 고유해야 합니다. 

 	 		+ ```NEO4J_ha_host_coordination``` : 포트를 포함한 주소는 모든 멤버가 HA 모드에서 클러스터 조정할 수 
 	 			 있어야합니다. 

 	 		+ ```NEO4J_ha_host_data``` : HA모드에서 데이터를 전송하는 주소(포트 포함)는 모든 클러스터 멤버가 조정해야 		 합니다. 
		
			+ ```NEO4J_ha_initial__hosts``` : 쉼포료 나뉜 클러스터의 다른 멤버 목록 


	
		 HA 클러스터 설정운 아래에서 확인할 수 있습니다. 


			 ##2.5.4.2 /conf 볼륨


			 Neo4j 설정을 임으로 수정하려면, 컨테이너에 /conf 볼륨을 추가하면 됩니다. 


			 	!!!이미지 !!!


			 	/conf 볼륨에 있는 모든 환경 설정 파일이 이미지가 제공한 파일을 덮어쓸 것 입니다. 그렇게 때문에, 파일 내 하나의 값을 바꾸려면 나머지 값이 완전하고 옳은 값이어야 합니다. 도커가 컨테이너로 전한 환경 변수는 /conf 볼륨 내 설정된 값을 오버라이드 할 것 입니다. 



			    <span class="glyphicon glyphicon-info-sign" aria-hidden="true"></span> 
			    > 환경 설정 볼륨을 사용한다면, 모든 네트워크 인터페이스를 고려해야합니다. 이는 ```dbms.connectors.default_listen_address=0.0.0.0```설정에서 조정할 수 있습니다. 


			    초기 환경 설정 파일을 덤프하려면, 이미지를 ```dump-config```와 함께 사용하십시오. 

			    !!!이미지!!!


			    ##2.5.4.3 새 이미지 생성



			    ```FROM neo4j:3.3```



			    ```COPY extra_conf.sh / extra_conf.sh```


			    ```docker run -e "EXTENSION_SCRIPT=/extra_conf.sh" cafe12345678```



			    ##2.5.4.4. Neo4j 업그레이드


			    + 단일-인스턴스 업그레이드 
			    + Neo4j 캐쥬얼 클러스터 업그레이드
			    + Neo4j HA클러스터 업그레이드



				###2.5.5 Neo4j 캐쥬얼 클러스터 모드 	<code>Enterprise Edition</code>


				Neo4j를 도커 내 CC모드에서 작동시키려면 서로 통신하도록 컨테이너의 클러스터를 연결해야합니다. 각 컨테이너는 다른 컨테이너에 대한 네으워크 경로와 
				```NEO4J_causal__clustering_expected__core__cluster__size```그리고 ```NEO4J_causal__clustering_initial__discovery__members```의 환경 변수가 코오에 대해 설정되어야 합니다. 읽기 복제본을 사용할 때는, ```NEO4J_casual__clustering__discovery__members``` 을 정의하면 됩니다. 
 



				싱글 도커 호스트 내에서, 이는 다음과 같이 발견할 수 있습니다. HTTP, HTTPs와 Bolt가 기본 포트로 사용된 것을 기억해야 합니다. 각각 컨테이너에서, 이들 포트는 도커 호스트 내 다른 그룹으로 맵핑 됩니다.


				!!!이미지!!!


				추가 인스턴스는 애드혹(ad-hoc) 방식으로 추가될 수 있습니다. 읽기 복테폼은 다음 예와 같이 추가할 수 있습니다. 


				!!! 이미지지 !!!


				각 칸테이너가 물리적 머신에서 작동하고 도커 네트워크를 사용하지 않는 상황이라면, 물리적 머신들끼리 소통할 수 있도록 광고 주소를 정의하는 것이 필요합니다. 각 컨테이너는 호스트 머신 네트워크에 연결되어 있습니다. 

				


				각 인스턴스는 다음과 같이 호출됩니다.:


				!!!  이미지    !!!


				```<public-address>```이 공공 호스트 이름이거나 기계의 IP주소 입니다. 

				Neo4j의 캐쥬얼 클러스터링에대 한 자세한 정보는 [섹션 4.2.3, "새 클러스터 생성"]("https://neo4j.com/docs/operations-manual/current/clustering/causal-clustering/setup-new-cluster/") 를 클릭하면 확인할 수 있습니다.


				###2.5.6 Neo4J 고 이요 모드 <code>Enterprise Edition</code>

				도커 내 Neo4j를 HA모드에서 작동시키려면 서로 통신하도록 컨테이너의 클러스터를 연결해야 합니다. 각 컨테이너는 다른 컨테이너에 대한 네으워크 경로와 ```NEO4J_ha_host_coordination```, ```NEO4J_ha_host_data``` 그리고 환경 변수가 그에 맞춰 설정되어야 합니다. 

 				 단일 도커(Docker) 호스트 내에서, 이는 다음과 같이 실행됩니다. 

 				 !!! 이미지!!!

 				 Neo4j 고 이용 모드에 대한 자세한 내용은 [섹션 B.2, "고 이용 클러스터 설정"]("https://neo4j.com/docs/operations-manual/current/tutorial/highly-available-cluster/")에서 확인할 수 있습니다. 


 				 ### 2.5.7. 사용자 정의 절차

 				 사용자 정의를 설치하기 위해서는, jars를 포함하는 플러그인 볼륨을 추가하면 됩니다. 

 				 !!! 이미지!!!

 				절차에 대한 자세한 내용은  [개발자 메뉴얼 -> 절차]("https://neo4j.com/docs/developer-manual/3.4-preview/extending-neo4j/procedures/")을 확인하면 됩니다. 


 				### 2.5.8. 사이퍼 쉘(Cypher Shell)

 				Neo4j 쉘은 다음 명령어를 사용해서 일부적으로 사용할 수 있습니다. 

 			 
				```docker exec --interactive --tty <container> bin/cypher-shell```
				


 				### 2.5.9 암호화 

 				도커 이미지는 Neo4j의 TLS서포트를 나타냅니다. 개별의 키와 증명서를 사용하려면, 