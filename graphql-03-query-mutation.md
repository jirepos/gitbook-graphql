# 쿼리 & 뮤테이션

이 글에서는 GraphQL 서버에 쿼리하는 방법에 대해 자세히 배운다. 

쿼리는 데이터를 읽는데(R) 사용하고, 뮤테이션은 데이터를 변조(CUD) 하는데 사용한다는 개념 적인 규약을 정해 놓은 것 뿐이다. 내부적으로 들어가면 이 둘은 별 차이가 없다. 


## GraphQL Schema file과 Resolver를 여러개로 분리 
user 패키지 안에 UserQuery.java 파일을 생성하고, resources  아래에 user.graphqls 파일을 생성한다. 
```
📁project_root
  📁src/main/java
    📁com/example/demo
       📁user
          📄UserQuery.java # GraphQLQueryResolver를 구현한 클래스 
      📄Query.java  # GraphQLQueryResolver를 구현한 클래스 
  📁src/main/resources
    📄application.yml
    📄post.graphqls  # GraphQL 스키마 정의 
    📄user.graphqls  # GraphQL 스키마 정의 
  📄pom.xml
```
### GraphQL Schema 
**post.graphqls**    
```
schema {
    query: Query
    mutation: Mutation
}

type Post {
    id: ID!
    title: String!
    category: String
    authorId: String!
}

# The Root Query for the application
type Query {
    recentPosts(count: Int, offset: Int): [Post]!
}

#The Root Mutation for the application
type Mutation {
    addPost(id: String!, title: String!, category: String, authorId: String) : Post!
}
```


**user.graphqls**    
Query나 Mutation type을  extend  키워드를 붙여 정의한다. 
```
schema {
    query: Query
    mutation: Mutation
}


type User {
    id: ID
    name: String
    age: Int
}

extend type Query {
  user(id:String): User!
}
extend type Mutation { 
  addUser(id: String!, name:String!, age:Int!): User!
}
```

### Query Resolver
GraphQLQueryResolver를 상속받아 구현한다. 

**Query.java**     
```java
package com.example.demo;

import java.util.ArrayList;
import java.util.List;

import org.springframework.stereotype.Component;

import com.coxautodev.graphql.tools.GraphQLQueryResolver;


@Component 
public class Query implements GraphQLQueryResolver {

  public List<Post> getRecentPosts(int count, int offset) {
    List<Post> list = new ArrayList<Post>() {
        {
          add(Post.builder().id("1").title("Post 1").category("Category 1").authorId("1").build());
          add(Post.builder().id("2").title("Post 2").category("Category 2").authorId("2").build());
          add(Post.builder().id("3").title("Post 3").category("Category 3").authorId("3").build());
          add(Post.builder().id("4").title("Post 4").category("Category 4").authorId("4").build());
          add(Post.builder().id("5").title("Post 5").category("Category 5").authorId("5").build());
        }
    };
    return list;
  }

}/// ~
```
**UserQuery.java**   
```java
package com.example.demo.user;

import org.springframework.stereotype.Component;

import com.coxautodev.graphql.tools.GraphQLQueryResolver;


@Component
public class UserQuery implements GraphQLQueryResolver {
  public User getUser(String id) {
    return User.builder().id(id).name("John Doe").age(30).build();
  }
}///~
```


### Mutation Resolver
**UserMutationResolver.java**
```java
package com.example.demo.user;

import org.springframework.stereotype.Component;

import com.coxautodev.graphql.tools.GraphQLMutationResolver;

@Component
public class UserMutationResolver implements GraphQLMutationResolver  {
    public User addUser(String id, String name, int age) {
      return User.builder().id(id).name(name).age(age).build();
    }
}
```
**PostMutationResolver.java**   
```java
package com.example.demo;

import org.springframework.stereotype.Component;

import com.coxautodev.graphql.tools.GraphQLMutationResolver;


@Component
public class PostMutationResolver implements GraphQLMutationResolver  {
    public Post addPost(String id, String title, String category, String authorId) {
      return Post.builder().id(id).title(title).category(category).authorId(authorId).build();
    }
}
```


## References
[쿼리 & 뮤테이션](https://graphql-kr.github.io/learn/queries/#)    
[GraphQL 개념잡기](https://tech.kakao.com/2019/08/01/graphql-basic/)    
[Mapping multiple graphQL schema files to separate resolvers - Spring Boot](https://stackoverflow.com/questions/60357247/mapping-multiple-graphql-schema-files-to-separate-resolvers-spring-boot)    
[Java로 GraphQL 다루기]9https://velog.io/@oenomel87/Java%EB%A1%9C-GraphQL)     
