# SpringBoot에서 GraphQL 사용

SpringBoot에서 GraphQL 사용하려면 SpringBoot 버전의 제약이 있다. 

* \>= 2.7.0.M1 and \< 3.0.0-M1
* Java 11 




## 디렉터리 구조 
```
📁project_root
  📁src/main/java
    📁com/example/demo
      📄DemoApplication.java # Main Applicaion
      📄GraphqlConfiguration.java # GraphQLQueryResolver를 객체를 Spring Bean을 생성
      📄Post.java   # 주고 받을 JavaBean
      📄Query.java  # GraphQLQueryResolver를 구현한 클래스 
  📁src/main/resources
    📄application.yml
    📄post.graphqls  # GraphQL 스키마 정의 
  📄pom.xml
```

## dependencies 설정 
pom.xml에 다음을 추가한다. 

```xml
		<dependency>
				<groupId>com.graphql-java</groupId>
				<artifactId>graphql-spring-boot-starter</artifactId>
				<version>5.0.2</version>
		</dependency>
		<dependency>
				<groupId>com.graphql-java</groupId>
				<artifactId>graphql-java-tools</artifactId>
				<version>5.2.4</version>
		</dependency>
		<dependency>
				<groupId>com.graphql-java</groupId>
				<artifactId>graphql-java</artifactId>
				<version>9.2</version>
		</dependency>
```    

graphql-spring-boot-starter와 grapql-java는 버전이 같아야 한다. 버전이 맞지 않으면 오류발생한다. 



## GraphQL Servlet 활성화 하기 
servlet는 graphql-spring-boot-starter가 boot application 에 추가되고 GraphSLSchema bean이 존해한다면  /graphql에 접근할 수 있다. 

```yaml
graphql:
  servlet:
    # Sets if GraphQL servlet should be created and exposed. If not specified defaults to "true".
    enabled: true
    # Sets the path where GraphQL servlet will be exposed. If not specified defaults to "/graphql"
    mapping: /graphql
    cors-enabled: true
    cors:
      # List 로 받기 때문에 여러 도메인을 설정 할 수 있다.
      allowed-origins: http://some.domain.com
      allowed-methods: GET, HEAD, POST
    # if you want to @ExceptionHandler annotation for custom GraphQLErrors
    exception-handlers-enabled: true
    context-setting: PER_REQUEST_WITH_INSTRUMENTATION
    # Sets if asynchronous operations are supported for GraphQL requests. If not specified defaults to true.
    async-mode-enabled: true
```
```yaml
graphql:
  tools:
    # resource 디렉토리 내의 스키마 파일의 위치 설정
    schema-location-pattern: "**/*.graphqls"
    # 스키마 확인 기능을 사용하여 타입 시스템
    introspection-enabled: true
```    
기본값을 사용할 경우 resource 디렉토리 내의 graphqls 확장자의 파일을 읽는다.


## SpringBoot Application 설정
이 예제에서는 DB 연결을 하지 않는다. 그래서 @EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})을 추가해야 한다. 안 그러면 url이 비었다는 오류가 발생한다. 

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;

@SpringBootApplication
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class DemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}

}
```


## 응답 데이터 정의 
Query 요청에 대한 응답 데이터로 사용할 JavaBean을 정의한다. 
```java
package com.example.demo;

import lombok.Builder;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter 
@Builder
public class Post {
  private String id;
  private String title;
  private String category;
  private String authorId;
}
```

## GraphQLQueryResolver 구현 
다음과 같이 요청에 대한 응답을 하는 메서드를 구현한다. 
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


## Spring Configuration class
이 클래스에서ㅓ Query 객체를 생성한다. Resolver 구현체에 @Component 어노테이션을 붙이면 이 클래스를 생성하지 않는다. 

```java
package com.example.demo;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class GraphqlConfiguration {

  @Bean
  public Query query() {
      return new Query();
  }
  
}///~
```
## GraphQL Scheme 정의 
*.graphqls 파일에 Entity에 해당하는 모델Model, CRUD에 해당하는 쿼리Query 등의 정의를 해본다. src/main/resources 아래 경로에 둔다. 
```
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
```


## 사용하기 
pom.xml에 다음의 의존성을 추가한다. 
```xml

		<dependency>
				<groupId>com.graphql-java</groupId>
				<artifactId>graphiql-spring-boot-starter</artifactId>
				<version>5.0.2</version>
		</dependency>
```
그러면 다음과 같이 호출하면 API를 테스트할 수 있는 화면이 표시된다. 
```
http://localhost:8080/graphiql 
```
화면에서 Query 목록이 나오고 recentPosts를 선택하면 테스트할 수 있다. 

다음과 같이 쿼리를 작성한다.
```
query {
  recentPosts(count: 1, offset:1) {
    id,
    title 
  }
}
```
다음과 같이 응답이 온다. 
```json
{
  "data": {
    "recentPosts": [
      {
        "id": "1",
        "title": "Post 1"
      },
      {
        "id": "2",
        "title": "Post 2"
      },
      {
        "id": "3",
        "title": "Post 3"
      },
      {
        "id": "4",
        "title": "Post 4"
      },
      {
        "id": "5",
        "title": "Post 5"
      }
    ]
  }
}
```


## References 
[Getting Started with GraphQL and Spring Boot](https://www.baeldung.com/spring-graphql)    
[graphql-java-kickstart/graphql-spring-boot](https://github.com/graphql-java-kickstart/graphql-spring-boot)
    



- Schema 확장자 : 모든 GraphQL Schema 의 확장자는 *.graphqls 로 설정되어야 합니다.
- Query : 읽기 연산을 정의함
- Mutation : 쓰기 연산을 정의함
- type : 응답값을 정의함
- input : 파라미터값을 정의함
- type, input 의 혼용은 불가능 함 : 2개의 값이 정확히 동일해도 각각 정의 내려야 함.
- 타입명 뒤에 ! : null 값을 허용하지 않겠다는 의미임
- 루트 쿼리(Root Query)와 루트 뮤테이션(Root Mutation)에 대한 정의는 하나만 존재해야 합니다.