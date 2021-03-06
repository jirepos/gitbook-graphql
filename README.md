# GraphQL 

GraphQL이 무엇인지 살펴 볼 것이다. 

**GraphQL vs REST**     
REST API는 다양한 End point를 가진다. API 이름 짖는 것도 힘들다. 

예를 들면, 다음과 같이 요청을 한다. 

```
/api/user?id=1
```

응답은 이렇게 온다. 
```json
{
  "id": 1,
  "name": "user1"
}
```
다른 예를 보자. 
```
/api/address?user_id=1
```
응답은 다음과 같이 온다. 
```json 
{
  "Street": "Sim Street", 
  "city": "New York city"
}
```

요청 별로 API를 만든다. 반면에 GraphQL에서는 End point가 하나이다. 
요청을 보자.
```
/graphql 

query { 
  user(id: 1) {
    id,   # id을 응답 값으로 요청  
    name, # name을 응답 값으로 요청
    addres {  # address를 응답 값으로 요청 
      street,
      city 
    }
  }
}
```
응답 결과는 요청한 형태로 온다. 즉, query에 나열한 필드를 응답값으로 나열한다. 
```
{
  "user": {
    "id": 1,    # id를 요청한 경우에 응답 값 
    "name": "Hong",  # name을 요청한 경우에 응답 값 
    "address": {   # address를 요청한 경우에 응답 값 
      "street": Sim street", 
      "city": "New York City" 
    }
  }
}
```



> GraphQL은 페이스북에서 만든 쿼리 언어입니다. GrpahQL은 요즘 개발자들 사이에서 자주 입에 오르내리고 있으나, 2019년 7월 기준으로 얼리스테이지(early-stage)임은 분명합니다. 국내에서 GraphQL API를 Open API로 공개한 곳은 드뭅니다. 또한, 해외의 경우, Github 사례(Github v4 GraphQL)를 찾을 수는 있지만, 전반적으로 GraphQL API를 Open API로 공개한 곳은 많지 않습니다. 하지만 등장한지 얼마되지 않았음에도 불구하고, GraphQL의 인기는 매우 가파르게 올라가고 있다는 사실을 확인 할 수 있습니다.


> Graph QL(이하 gql)은 Structed Query Language(이하 sql)와 마찬가지로 쿼리 언어입니다. 하지만 gql과 sql의 언어적 구조 차이는 매우 큽니다. 또한 gql과 sql이 실전에서 쓰이는 방식의 차이도 매우 큽니다. gql과 sql의 언어적 구조 차이가 활용 측면에서의 차이를 가져왔습니다. 이 둘은 애초에 탄생 시기도 다르고 배경도 다릅니다. sql은 데이터베이스 시스템에 저장된 데이터를 효율적으로 가져오는 것이 목적이고, gql은 웹 클라이언트가 데이터를 서버로 부터 효율적으로 가져오는 것이 목적입니다. sql의 문장(statement)은 주로 백앤드 시스템에서 작성하고 호출 하는 반면, gql의 문장은 주로 클라이언트 시스템에서 작성하고 호출 합니다.



* **sql은 데이터베이스 시스템에 저장된 데이터를 효율적으로 가져오는 것이 목적이고, gql은 웹 클라이언트가 데이터를 서버로 부터 효율적으로 가져오는 것이 목적**


## GraphQL의 구조 

* 구조 
  * 쿼리/뮤테이션(query/mutation)
  * 스키마/타입(schema/type)
  * 리졸버(resolver)
  * 인트로스펙션(introspection) 



| Requirement | REST | GraphQL |
|---|---|---|
| Fetching data objects | GET | query |
| Writing data | POST | mutation
| Updating/deleting data | PUT/PATCH/DELETE | mutation |
| Watching/subscribing to data | - | subscription |

![](.gitbook/assets/gql-001.png)


 ### 쿼리/뮤테이션(query/mutation)
sql에서는 굳이 쿼리와 뮤테이션을 나누는데 내부적으로 들어가면 사실상 이 둘은 별 차이가 없습니다. 쿼리는 데이터를 읽는데(R) 사용하고, 뮤테이션은 데이터를 변조(CUD) 하는데 사용한다는 개념 적인 규약을 정해 놓은 것 뿐입니다.

쿼리와 뮤테이션 그리고 응답 내용의 구조는 상당히 직관적 입니다. 요청하는 쿼리문의 구조와 응답 내용의 구조는 거의 일치 합니다.



## 스키마/타입(schema/type)
데이터베이스 스키마(schema)를 작성할 때의 경험을 SQL 쿼리 작성으로 비유한다면, gql 스키마를 작성할 때의 경험은 C, C++의 헤더파일 작성에 비유가 됩니다. 그러므로, 프로그래밍언어(C, C++, JAVA등)에 익숙한 프로그래머라면 스키마 정의 또한 쉽게 배우실 것입니다.


## 리졸버(resolver)

gql 쿼리문 파싱은 대부분의 gql 라이브러리에서 처리를 하지만, gql에서 데이터를 가져오는 구체적인 과정은 resolver(이하 리졸버)가 담당하고, 이를 직접 구현 해야 합니다


## 인트로스펙션(introspection)



기존 서버-클라이언트 협업 방식에서는 연동규격서라고 하는 API 명세서를 주고 받는 절차가 반드시 필요 했습니다. 프로젝트 관리 측면에서 관리해야 할 대상의 증가는 작업의 복잡성 및 효율성 저해를 의미합니다. 이 API 명세서는 때때로 관리가 제대로 되지 않아, 인터페이스 변경 사항을 제때 문서에 반영하지 못하기도 하고, 제 타이밍에 전달 못하곤 합니다.

이러한 REST의 API 명세서 공유와 같은 문제를 해결하는 것이 gql의 인트로스펙션 기능 입니다. gql의 인트로스펙션은 서버 자체에서 현재 서버에 정의된 스키마의 실시간 정보를 공유를 할 수 있게 합니다. 이 스키마 정보만 알고 있으면 클라이언트 사이드에서는 따로 연동규격서를 요청 할 필요가 없게 됩니다. 클라이언트 사이드에서는 실시간으로 현재 서버에서 정의하고 있는 스키마를 의심 할 필요 없이 받아들이고, 그에 맞게 쿼리문을 작성하면 됩니다.



GraphQL에 대한 이해는 [여기](https://hasura.io/learn/graphql/svelte-apollo/intro-to-graphql/)를 방문해 보면 도식이 쉽게 설명이 되어 있다. 





## Graphql 테스트 도구 
### graphql-playground 
윈도우즈용 설치      
[graphql-playground](https://github.com/graphql/graphql-playground)      
[GraphQL Playground](https://www.electronjs.org/apps/graphql-playground)    



## References
[Intro to GraphQL](https://hasura.io/learn/graphql/svelte-apollo/intro-to-graphql/)     
[GraphQL 개념잡기](https://tech.kakao.com/2019/08/01/graphql-basic/)