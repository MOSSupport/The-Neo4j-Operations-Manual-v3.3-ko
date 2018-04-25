#### 7.1.4.1. 기본 역할 

<div class="abstract">
	<p>이 절에서는 Neo4j의 기본 역할에 대해서 설명합니다. 
	</p>
</div>

Neo4j는 역할 기반 액세스 제어 프레임워크에 다섯 가지 기본 제공 역할을 제공합니다.

-   `reader`

    데이터 그래프에 읽기 전용으로 연결 (모든 노드, 관계, 속성).

-   `editor`

    데이터 그래프에 읽기/쓰기 접근이 가능합니다. 쓰기 액세스는 기존 속성 키, 노드 레이블 및 그래프의 관계 유형을 만들고 변경하는 것으로 제한됩니다.

-   `publisher`
    데이터 그래프에 읽기/쓰기 접근.

-   `architect`

    데이터 그래프에 읽기/쓰기 접근. 향후 다른 스키마 구성과 함께 인덱스에 대한 접근 설정/삭제. 

-   `admin`

    데이터 그래프에 읽기/쓰기 접근. 향후 다른 스키마 구성과 함께 인덱스에 대한 접근 설정/삭제. 쿼리 보기/종료.

We detail below the set of actions on the data and database prescribed by each role:

| Action                                   | `reader` | `editor` | `publisher` | `architect` | `admin` | (no role) |
| ---------------------------------------- | -------- | -------- | ----------- | ----------- | ------- | --------- |
| Change own password                      | `X`      | `X`      | `X`         | `X`         | `X`     | `X`       |
| View own details                         | `X`      | `X`      | `X`         | `X`         | `X`     | `X`       |
| Read data                                | `X`      | `X`      | `X`         | `X`         | `X`     |           |
| View own queries                         | `X`      | `X`      | `X`         | `X`         | `X`     |           |
| Terminate own queries                    | `X`      | `X`      | `X`         | `X`         | `X`     |           |
| Write/update/delete data                 |          | `X`      | `X`         | `X`         | `X`     |           |
| Create new types of properties key       |          |          | `X`         | `X`         | `X`     |           |
| Create new types of nodes labels         |          |          | `X`         | `X`         | `X`     |           |
| Create new types of relationship types   |          |          | `X`         | `X`         | `X`     |           |
| Create/drop index/constraint             |          |          |             | `X`         | `X`     |           |
| Create/delete user                       |          |          |             |             | `X`     |           |
| Change another user’s password           |          |          |             |             | `X`     |           |
| Assign/remove role to/from user          |          |          |             |             | `X`     |           |
| Suspend/activate user                    |          |          |             |             | `X`     |           |
| View all users/roles                     |          |          |             |             | `X`     |           |
| View all roles for a user                |          |          |             |             | `X`     |           |
| View all users for a role                |          |          |             |             | `X`     |           |
| View all queries                         |          |          |             |             | `X`     |           |
| Terminate all queries                    |          |          |             |             | `X`     |           |
| Dynamically change configuration (see [Section 3.7, “Dynamic settings”](https://neo4j.com/docs/operations-manual/3.3/configuration/dynamic-settings/)) |          |          |             |             | `X`     |           |

A [user](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/terminology/#term-user) who has no assigned roles will not have any rights or capabilities regarding the data, not even read privileges. A user may have more than one assigned role, and the union of these determine what action(s) on the data may be undertaken by the user.