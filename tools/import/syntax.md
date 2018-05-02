### 10.2.1. 문법
CSV 파일에서 데이터를 임포트하는 문법은 다음과 같습니다:
```
neo4j-admin import [--mode=csv] [--database=<name>]
                          [--additional-config=<config-file-path>]
                          [--report-file=<filename>]
                          [--nodes[:Label1:Label2]=<"file1,file2,...">]
                          [--relationships[:RELATIONSHIP_TYPE]=<"file1,file2,...">]
                          [--id-type=<STRING|INTEGER|ACTUAL>]
                          [--input-encoding=<character-set>]
                          [--ignore-extra-columns[=<true|false>]]
                          [--ignore-duplicate-nodes[=<true|false>]]
                          [--ignore-missing-nodes[=<true|false>]]
                          [--multiline-fields[=<true|false>]]
                          [--delimiter=<delimiter-character>]
                          [--array-delimiter=<array-delimiter-character>]
                          [--quote=<quotation-character>]
                          [--max-memory=<max-memory-that-importer-can-use>]
```
<div class="example">
예제 10.1. CSV 파일에서 데이터 임포트  
<div class="example-contents">
다음 절의 CSV 헤더 포맷에 따라 데이터 형식을 지정한 movies.csv, actors.csv, roles.csv 파일이 있고, 이들 세 파일에서 임포트 명령은 다음과 같습니다.  
<code>  
neo4j_home$ bin/neo4j-admin import --nodes import/movies.csv --nodes import/actors.csv --relationships import/roles.csv
</code>  
3.0-이전 데이터베이스로 임포트하는 명령을 다음과 같습니다:  
<code>
neo4j-admin import --mode=database [--database=<name>]
                          [--additional-config=<config-file-path>]
                          [--from=<source-directory>]
</code></div></div>
3.0-이전 데이터베이스 업그레이드의 `neo4j-admin import` 이용 방법은 [업그레이드](../../upgrade/deployment-upgrading.md) 절을 참고합니다. 
