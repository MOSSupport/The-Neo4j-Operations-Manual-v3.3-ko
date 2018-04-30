### 10.2.2. CSV 파일 헤더 포맷
> 이 절에서는 Neo4j 임포트 시, CSV 파일의 헤더 포맷에 대해 설명합니다.

각 데이터 소스의 헤더 행은 데이터 필드의 해석 방법을 지정합니다. 헤더 행과 데이터 행에 동일한 구분 기호를 사용해야합니다.

헤더에는 `<name> : <field_type>` 형식의 각 필드에 대한 정보가 들어 있습니다. `<name>`은 특성과 노드 ID에 사용됩니다. 자세한 내용은 아래 관련 절을 참조합니다. 다른 모든 경우에는 필드의 `<name>` 부분은 무시됩니다.

#### 10.2.2.1. 속성
속성의 경우, 필드의 `<name>` 부분은 속성 키이고 `<field_type>` 부분은 데이터 형식입니다(아래 참조). 노드 데이터 파일과 관계 데이터 파일 모두에 속성을 가질 수 있습니다.  
**데이터 형식**
속성의 데이터 형식에 `int`, `long`, `float`, `double`, `boolean`, `byte`, `short`, `char`, `string`을 사용합니다. 데이터 형식이 지정되지않으면 디폴트로 `string`입니다. 배열로 정의하려면 []을 데이터 형식에 추가합니다. 기본적으로 배열 값은 `;`로 구분됩니다. 다른 구분 기호는` --array-delimiter`를 사용합니다.
**IGNORE**
데이터에 무시할 필드가있는 경우 `IGNORE` 키워드를 사용합니다. `IGNORE` 앞에는 `:`가 붙습니다. 아래 예제를 참조합니다.

#### 10.2.2.2. 노드 데이터 파일
노드 데이터가 들어있는 파일에는 필수 필드, `ID`와 선택적 필드, `LABEL:`이 있습니다.

**ID**
임포트되는 동안 사용될, 각 노드는 고유 ID가 있어야 합니다. ID는 노드간의 관계 생성시, 올바른 노드를 찾는데 사용됩니다. ID는 임포트되는 모든 노드, 심지어는 레이블(label)이 다른 노드와도 고유(unique)해야 합니다. 속성에서도 ID는 고유해야 합니다. 즉, 필드 정의 `<name>:ID`의 `<name>` 에서도 ID는 유일해야 합니다. 그러한 속성의 이름이 `<name>`에서 정의되지 않으면, 고유 ID는 임포트에서 사용되지만 나중에 참조는 할 수 없습니다.   
**LABEL**
이 필드에서 하나 이상의 레이블을 읽습니다. 배열과 마찬가지로, 여러 레이블은 디폴트로는 `;`로 구분되거나, 다른 구분 문자 지정은 `--array-delimiter`로 지정합니다.  

예제 10.2. 두개의 노드 파일 정의하기  
***
이 예제는 movies.csv 파일에서 3개의 영화를 정의합니다.  영화는 `movieId`, `year`, `title` 속성을 가집니다. 모든 영화에 `Movie` 레이블이 붙습니다. 그 중 두 영화에 `Sequel`이라는 레이블이  더 붙습니다.
```
movieId:ID,title,year:int,:LABEL
tt0133093,"The Matrix",1999,Movie
tt0234215,"The Matrix Reloaded",2003,Movie;Sequel
tt0242653,"The Matrix Revolutions",2003,Movie;Sequel
```
actors.csv파일에서는 3명의 배우를 정의합니다. 배우는 `personId`와 `name`속성과, `Actor`라는 레이블을 갖습니다.
```
personId:ID,name,:LABEL
keanu,"Keanu Reeves",Actor
laurence,"Laurence Fishburne",Actor
carrieanne,"Carrie-Anne Moss",Actor
```
***
#### 10.2.2.3. 관계 데이터 파일
관계 데이터 파일에는 3개의 필수 필드가 있습니다:   
**TYPE**
관계의 형식.
**START_ID**
이 관계의 시작 노드의 ID
**END_ID**
이 관계의 끝 노드의 ID

`START_ID` 및 `END_ID`는 이전 섹션에서 설명한대로 노드 데이터 소스 중 하나에 정의 된 고유 한 노드 ID를 참조합니다. 이들 중 어느 것도 이름을 사용하지 않습니다. `<name>:START_ID` 또는 `<name>:END_ID`가 정의되면 `<name>` 부분은 무시됩니다.

예제 10.3. 관계 파일 정의하기   
***
이 예제에서는 이전 예제의 두 노드 파일이 다음에 나오는 관계 파일과 함께 사용된다고 가정합니다.  

roles.csv 파일에서 배우와 영화의 관계를 정의합니다. 각 행은 관계 유형 `ACTED_IN`의 관계로 시작 노드와 끝 노드를 연결합니다. 위의 노드 파일에서 고유한 식별자인 `personId와` `movieId를` 사용하는 방법에 유의합니다. 영화에서 배우가 연기한 캐릭터의 이름은 관계에서 `role` 속성으로 저장됩니다.
```
:START_ID,role,:END_ID,:TYPE
keanu,"Neo",tt0133093,ACTED_IN
keanu,"Neo",tt0234215,ACTED_IN
keanu,"Neo",tt0242653,ACTED_IN
laurence,"Morpheus",tt0133093,ACTED_IN
laurence,"Morpheus",tt0234215,ACTED_IN
laurence,"Morpheus",tt0242653,ACTED_IN
carrieanne,"Trinity",tt0133093,ACTED_IN
carrieanne,"Trinity",tt0234215,ACTED_IN
carrieanne,"Trinity",tt0242653,ACTED_IN
```
***
#### 10.2.2.4. ID 스페이스
디폴트로 임포트(import) 도구는 노드 ID(식별자)가 여러 노드 파일에서도 고유하다고 가정합니다. 대부분의 경우 ID는 각의 엔티티 파일들에서 고유합니다 (예: CSV 파일에 관계형 데이터베이스에서 추출한 데이터가 들어 있고, ID 필드가 해당 테이블의 기본 키 열(primary key column)에서 가져온 경우). 이 상황을 처리하기 위해 ID 스페이스를 정의합니다. ID 스페이스는 구문 `ID (<ID 스페이스 식별자>)`를 사용하여 노드 파일의 `ID` 필드에 정의됩니다. 관계 파일에서 ID 스페이스의 ID를 참조하려면 `START_ID(<ID 스페이스 식별자>)` 및 `END_ID(<ID 스페이스 식별자>)` 구문을 사용합니다.

예제 10.4. ID 스페이스 정의와 사용  
***
movies.csv 파일에서 `Movie-ID` ID 스페이스를 정의합니다.
```
movieId:ID(Movie-ID),title,year:int,:LABEL
1,"The Matrix",1999,Movie
2,"The Matrix Reloaded",2003,Movie;Sequel
3,"The Matrix Revolutions",2003,Movie;Sequel
```
actors.csv파일의 헤더에서 `Actor-ID` ID 스페이스를 정의합니다.
```
personId:ID(Actor-ID),name,:LABEL
1,"Keanu Reeves",Actor
2,"Laurence Fishburne",Actor
3,"Carrie-Anne Moss",Actor
```
영화의 배우들을 연결할 때, 이전에 정의한 ID 스페이스를 사용합니다.
```
:START_ID(Actor-ID),role,:END_ID(Movie-ID),:TYPE
1,"Neo",1,ACTED_IN
1,"Neo",2,ACTED_IN
1,"Neo",3,ACTED_IN
2,"Morpheus",1,ACTED_IN
2,"Morpheus",2,ACTED_IN
2,"Morpheus",3,ACTED_IN
3,"Trinity",1,ACTED_IN
3,"Trinity",2,ACTED_IN
3,"Trinity",3,ACTED_IN
```
***
### 10.2.2.5. CSV 파일에서 건너뛰는 열(skip column)   
열(column) 위치에서 IGNORE 키워드를 사용하면 해당 필드는 완전히 무시됩니다.

예제 10.5. CSV 파일에서 하나의 열 건너뛰기  
***
예제에서는 노드 파일의 세 번째 열에 있는 데이터에 관심이 없어 건너 뜁니다. `IGNORE` 키워드 앞에는 `:`가 붙습니다.
```
personId:ID,name,:IGNORE,:LABEL
keanu,"Keanu Reeves","male",Actor
laurence,"Laurence Fishburne","male",Actor
carrieanne,"Carrie-Anne Moss","female",Actor
```
***
임포트하는 열의 오른쪽 열들에 불필요한 데이터가 모두 있으면, 명령행 옵션 `--ignore-extra-columns`를 대신 사용할 수 있습니다.
