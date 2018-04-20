### 10.2.2. CSV 파일 헤더 포맷
> 이 절에서는 Neo4j 임포트 시, CSV 파일의 헤더 포맷에 대해 설명합니다.

각 데이터 소스의 헤더 행은 데이터 필드의 해석 방법을 지정합니다. 헤더 행과 데이터 행에 동일한 구분 기호를 사용해야합니다.

헤더에는 `<name> : <field_type>` 형식의 각 필드에 대한 정보가 들어 있습니다. `<name>`은 특성과 노드 ID에 사용됩니다. 자세한 내용은 아래 관련 절을 참조합니다. 다른 모든 경우에는 필드의 `<name>` 부분은 무시됩니다.

#### 10.2.2.1. 속성
속성의 경우, 필드의 `<name>` 부분은 속성 키이고 `<field_type>` 부분은 데이터 형식입니다(아래 참조). 노드 데이터 파일과 관계 데이터 파일 모두에 속성을 가질 수 있습니다.  
**데이터 형식**
Use one of int, long, float, double, boolean, byte, short, char, string to designate the data type for properties. If no data type is given, this defaults to string. To define an array type, append [] to the type. By default, array values are separated by ;. A different delimiter can be specified with --array-delimiter.
**IGNORE**
If there are fields in the data that we wish to ignore completely, this can be done using the IGNORE keyword. IGNORE must be prepended with a :. See example in the relevant section below.
10.2.2.2. Node data files
For files containing node data, there is one mandatory field; the ID, and one optional field; the LABEL:

ID
Each node must have a unique ID which is used during the import. The IDs are used to find the correct nodes when creating relationships. Note that the ID has to be unique across all nodes in the import; even for nodes with different labels. The unique ID can be persisted in a property whose name is defined by the <name> part of the field definition <name>:ID. If no such property name is defined, the unique ID will be used for the purpose of the import but not be available for reference later.
LABEL
Read one or more labels from this field. Like array values, multiple labels are separated by ;, or by the character specified with --array-delimiter.
Example 10.2. Define two nodes files
We define three movies in the movies.csv file. They have the properties movieId, year and title. All the movies are given the label Movie. Two of them are also given the label Sequel.

movieId:ID,title,year:int,:LABEL
tt0133093,"The Matrix",1999,Movie
tt0234215,"The Matrix Reloaded",2003,Movie;Sequel
tt0242653,"The Matrix Revolutions",2003,Movie;Sequel
We also define three actors in the actors.csv file. They all have the properties personId and name, and the label Actor.

personId:ID,name,:LABEL
keanu,"Keanu Reeves",Actor
laurence,"Laurence Fishburne",Actor
carrieanne,"Carrie-Anne Moss",Actor

#### 10.2.2.3. Relationship data files
For files containing relationship data, there are three mandatory fields:

TYPE
The relationship type to use for this relationship.
START_ID
The ID of the start node for this relationship.
END_ID
The ID of the end node for this relationship.
The START_ID and END_ID refer to the unique node ID defined in one of the node data sources, as explained in the previous section. None of these takes a name, e.g. if <name>:START_ID or <name>:END_ID is defined, the <name> part will be ignored.

Example 10.3. Define a relationships file
In this example we assume that the two nodes files from the previous example are used together with the following relationships file.

We define relationships between actors and movies in the file roles.csv. Each row connects a start node and an end node with a relationship of relationship type ACTED_IN. Notice how we use the unique identifiers personId and movieId from the nodes files above. The name of character that the actor is playing in this movie is stored as a role property on the relationship.

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

#### 10.2.2.4. ID spaces
By default, the import tool assumes that node identifiers are unique across node files. In many cases the ID is only unique across each entity file, for example when our CSV files contain data extracted from a relational database and the ID field is pulled from the primary key column in the corresponding table. To handle this situation we define ID spaces. ID spaces are defined in the ID field of node files using the syntax ID(<ID space identifier>). To reference an ID of an ID space in a relationship file, we use the syntax START_ID(<ID space identifier>) and END_ID(<ID space identifier>).

Example 10.4. Define and use ID spaces
Define a Movie-ID ID space in the movies.csv file.

movieId:ID(Movie-ID),title,year:int,:LABEL
1,"The Matrix",1999,Movie
2,"The Matrix Reloaded",2003,Movie;Sequel
3,"The Matrix Revolutions",2003,Movie;Sequel
Define an Actor-ID ID space in the header of the actors.csv file.

personId:ID(Actor-ID),name,:LABEL
1,"Keanu Reeves",Actor
2,"Laurence Fishburne",Actor
3,"Carrie-Anne Moss",Actor
Now use the previously defined ID spaces when connecting the actors to movies.

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

### 10.2.2.5. Skip columns in CSV files
If you use the IGNORE keyword in a column position, that field will be ignored completely.

Example 10.5. Skip a column in a CSV file
In this example, we are not interested in the data in the third column of the nodes file and wish to skip over it. Note that the IGNORE keyword is prepended by a :.

personId:ID,name,:IGNORE,:LABEL
keanu,"Keanu Reeves","male",Actor
laurence,"Laurence Fishburne","male",Actor
carrieanne,"Carrie-Anne Moss","female",Actor
If all your superfluous data is placed in columns located to the right of all the columns that you wish to import, you can instead use the command line option --ignore-extra-columns.
