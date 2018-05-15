## 7.2. 확장 기능 보안                  

<div class="abstract">
	<p>이 절에서는 Neo4j에서 사용자 정의 작성의 보안을 보장하는 방법에 대해 설명합니다.
	</p>
</div>

Neo4j는 Cypher에서 직접 호출 할 수있는 사용자 정의 코드를 작성하여 확장 할 수 있습니다.[Developer Manual → Procedures 참조](https://neo4j.com/docs/developer-manual/3.3/extending-neo4j/procedures/). 이 절에서는 이러한 추가 보안을 보장하는 방법에 대해 설명합니다.

### 7.2.1. 샌드박스                     

Neo4j는 프로시저가 실수로 비보안 API를 사용하지 않도록 샌드박스를 제공합니다. 예를 들어, 사용자코드를 작성할 때 공개적으로 지원되지 않는 Neo4j API에 액세스 할 수 있으며 이러한 내부 API는 사전 통보없이 변경 될 수 있습니다. 또한, 이들의 사용은 안전하지 않은 행동을 취할 위험이 있습니다. 샌드박스 기능은 확장 기능 사용을 공개적으로 지원되는 API로 제한합니다. API는 독점적으로 안전한 작업을 포함하거나 보안 검사를 포함합니다.           


`dbms.security.procedures.unrestricted` 구성 설정은 데이터베이스에 모든 접속이 가능한 프로지서 목록 및/또는 사용자 정의 함수 목륵을 쉼표로 정의하여 샌드박스 기능을 우회할 수 있는 가능성을 제공합니다. 목록에는 완전한 프로지서 이름과 부분 이름에 와일드카드 `*`가 포함될 수 있습니다.


`dbms.security.procedures.unrestricted` 에서 허용되지 않는 API를 지원하는 확장을 로드하려고 시도하면 보안 로그에 경고가 발생합니다. 경고는 확장 프로그램이 로드하려고 하는 구성 요소에 대한 액세스 권한이 없다는 것을 나타냅니다. 또한 모의 프로시저는 프로시저 이름과 함께로드됩니다. 모의 프로시저를 호출하면 더 많은 사용 권한이 필요하기 때문에 프로시저를 로드하지 못했다는 오류가 발생합니다.

<div class="example">
예제 7.22. 샌드박스
<div class="example-contents">
이 예제에서 <code>my.extensions.example</code> 프로시저와 <code>my.procedures</code> 라이브러리의 프로시저 중 일부는 지원되지 않는 API를 사용한다고 가정합니다. 이 절차의 실행을 허용하기 위해 아래와 같이 설정을 구성합니다.

<p>
<pre>
<code>
# 샌드박스 예제
dbms.security.procedures.unrestricted=my.extensions.example,my.procedures.*
</code>
</pre>
</div>
</div>

### 7.2.2. 화이트리스트                     

화이트리스트는 더 큰 라이브러리의 몇 가지 확장만 로드 할 수 있도록 하는 데 사용될 수 있습니다.

`dbms.security.procedures.whitelist`설정은 라이브러리에서 사용할 수있는 특정 프로시저의 이름을 지정하는데 사용됩니다. 로드 될 프로시저의 쉼표로 구분 된 목록을 정의합니다. 목록에는 완전한 프로 시저 이름과 부분 이름에 와일드 카드`*`가 포함될 수 있습니다.

<div class="example">
예제 7.23. 화이트리스트
<div class="example-contents">
이 예제에서 <code>apoc.load.json</code> 메소드와 <code>apoc.coll</code>의 모든 메소드를 사용할 수 있기를 원합니다. 우리는이 기준에 일치하는 것을 제외하고 <code>apoc</code> 라이브러리로부터 추가적인 확장을 사용 가능하게 하고 싶지 않습니다.

<p>
<pre>
<code>
# Example white listing
dbms.security.procedures.whitelist=apoc.coll.*,apoc.load.*
</code>
</pre>
</div>
</div>

`dbms.security.procedures.whitelist`에 대해 알아둬야 할 몇 가지 사항이 있습니다 :

- 이 설정을 사용하면 나열된 것 이외의 확장자가 로드되지 않습니다. 특히 빈 문자열로 설정된 경우 확장이 로드되지 않습니다.
- 설정의 기본값은 `*` 입니다. 즉, 명시적으로 값을 지정하지 않으면 (또는 값이 없는 경우) 디렉토리 [*plugins*](https://neo4j.com/docs/operations-manual/current/configuration/file-locations/)에 있는 모든 라이브러리를 로드합니다.
- 이 매개 변수가 가리키는 확장이 내부 API에 액세스하도록 프로그래밍 된 경우 [섹션 7.2.1, "샌드박스"](securing-extensions.md/#7.2.1-샌드박스)에 설명이 됩니다.