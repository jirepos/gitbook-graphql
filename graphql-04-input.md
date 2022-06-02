# Input 

지금까지는 열거형 타입이나 문자열과 같은 스칼라 값을 인자로 필드에 전달하는 방법에 대해서만 설명했습니다. 하지만 복잡한 객체도 쉽게 전달할 수 있습니다. 이는 뮤테이션에서 특히 유용합니다. 뮤테이션은 생성될 전체 객체를 전달하고자 할 수 있습니다. GraphQL 스키마 언어에서 입력 타입은 일반 객체 타입과 정확히 같지만, type 대신 input 을 사용합니다.
[입력 타입](https://graphql-kr.github.io/learn/schema/#)


많은 경우 동일한 입력 매개변수를 모두 수용하는 다양한 Mutation를 찾을 수 있습니다. 일반적인 예는 데이터베이스에서 개체를 생성하고 데이터베이스에서 개체를 업데이트할 때 종종 동일한 매개변수를 사용한다는 것입니다. 스키마를 더 단순하게 만들기 위해 type 키워드 대신 input 키워드를 사용하여 '입력 유형'을 사용할 수 있습니다.



Mutation에 간단히 객체를 전달할 때에는 input을 사용한다. 응답으로 받을 때에는 type을 사용한다. 


```
input MessageInput {
  content: String
  author: String
}

type Message {
  id: ID!
  content: String
  author: String
}

type Query {
  getMessage(id: ID!): Message
}

type Mutation {
  createMessage(input: MessageInput): Message
  updateMessage(id: ID!, input: MessageInput): Message
}
```

여기서 돌연변이는 메시지 유형을 반환하므로 클라이언트는 메시지를 변경하는 요청과 동일한 요청에서 새로 수정된 메시지에 대한 추가 정보를 얻을 수 있습니다.

입력 유형은 다른 개체인 필드를 가질 수 없으며 기본 스칼라 유형, 목록 유형 및 기타 입력 유형만 있습니다.

끝에 Input을 사용하여 입력 유형의 이름을 지정하는 것은 유용한 규칙입니다. 왜냐하면 단일 개념적 개체에 대해 약간 다른 입력 유형과 출력 유형을 모두 원하는 경우가 많기 때문입니다.

[Mutations and Input Types](https://graphql.org/graphql-js/mutations-and-input-types/)    