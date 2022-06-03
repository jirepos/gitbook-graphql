# GraphQL 문서화
GraphQL의 SDL(스키마 정의 언어)은 설명이라고 하는 Markdown 지원 문서 문자열을 지원합니다. 이는 그래프 소비자가 필드를 찾고 사용 방법을 배우는 데 도움이 됩니다.

다음 스니펫은 한 줄 문자열 리터럴과 여러 줄 블록을 모두 사용하는 방법을 보여줍니다.


```
"Description for the type"
type MyObjectType {
  """
  Description for field
  Supports **multi-line** description for your [API](http://example.com)!
  """
  myField: String!

  otherField(
    "Description for argument"
    arg: Int
  )
}
```





## References 
[Mutation 설계하기](https://fe-developers.kakaoent.com/2022/220113-designing-graphql-mutation/)     
[Document a GraphQL API](https://stackoverflow.com/questions/39504986/document-a-graphql-api)     
[GraphQL schema basics](https://www.apollographql.com/docs/apollo-server/schema/schema/)    
[GraphiQL](https://github.com/graphql/graphiql/blob/main/packages/graphiql/README.md)