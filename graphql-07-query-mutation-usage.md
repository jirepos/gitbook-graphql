# Query와 Mutation 요청하기 

이 글에서는 GraphQL 서버에 쿼리하는 방법에 대해 자세히 배운다. 

쿼리는 데이터를 읽는데(R) 사용하고, 뮤테이션은 데이터를 변조(CUD) 하는데 사용한다는 개념 적인 규약을 정해 놓은 것 뿐이다. 내부적으로 들어가면 이 둘은 별 차이가 없다. 


## Query 요청

### 파라미터가 있는 쿼리 
두 개의 파라미터가 있는 쿼리를 정의한다. 
```
# The Root Query for the application
type Query {
    recentPosts(count: Int, offset: Int): [Post]!
}
```
Resolver에는 다음과 같이 메서드를 정의한다. 

```java
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
```
다음과 같이 쿼리를 작성한다.  { } 필드들은 리턴 값에서 표시할 컬럼을 나열한다. 
```
query { 
  recentPosts(count:1, offset: 1) {
    authorId,
    category,
    title
  }
}
```

반환값을 다음과 같다. 
```json
{
  "data": {
    "books": [
      {
        "title": "자바의 정석1",
        "author": {
          "name": "홍길동"
        }
      },
      {
        "title": "자바의 정석2",
        "author": {
          "name": "홍길동"
        }
      },
      {
        "title": "자바의 정석3",
        "author": {
          "name": "홍길동"
        }
      },
      {
        "title": "자바의 정석4",
        "author": {
          "name": "홍길동"
        }
      }
    ]
  }
}
```





### 파라미터 없는 Query 
서버에서 메서드가 파라미터가 없다. 
```java
public List<Post> getRecentPostsAll() {
}  
```
.graphqls에서는 괄호 없이 정의한다. 
```
# The Root Query for the application
type Query {
    recentPostsAll: [Post]!
}
```
다음과 같이 쿼리를 작성한다. 쿼리명 다음에 ()가 없다. 
```
query { 
  recentPostsAll {
    authorId,
    category,
    title
  }
}
```



### 객체 사용하기 
Book 클래스를 생성한다. Book 클래스는 Author 클래스를 필드로 가지고 있다. 
```java
package com.example.demo.book;

import lombok.Builder;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
@Builder
public class Book {
  private String title;
  private Author author; 
  public Book() {
  }
  
  public Book(String title, Author author) {
    this.title = title;
    this.author = author;
  }
}
```
Author 클래스를 정의한다. 

```java
import lombok.Builder;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
@Builder
public class Author {
  private String name;
  private List<Book> books; 
  public Author() {
  }
  public Author(String name, List<Book> books) {
    this.name = name;
    this.books = books;
  }
}
```

Query Resolver에 Book 목록을 반환하는 메서드를 정의한다. 
```java
  public List<Book> getBooks() {
    List<Book> list = new ArrayList<Book>() {
      {
        add(Book.builder().title("자바의 정석1").author(new Author("홍길동",null)).build());
        add(Book.builder().title("자바의 정석2").author(new Author("홍길동",null)).build());
        add(Book.builder().title("자바의 정석3").author(new Author("홍길동",null)).build());
        add(Book.builder().title("자바의 정석4").author(new Author("홍길동",null)).build());
      }
    };
    return list;
  }
```
Book 타입과 Author 타입을 정의한다. 
```
type Book {
  title: String
  author: Author
}

type Author {
  name: String
  books: [Book]
}

```

books 쿼리를 정의한다. 
```
extend type Query { 
  books: [Book]
}
```
다음과 같이 쿼리를 작성하여 요청한다.  반환값에서 author 객체의 name 필드를 선택하고 있다. 
```
query { 
  books {
    title, 
    author {
      name 
    }
  }
}
```

이번에는 Author 목록을 가져와 보자. 

Query Resolver에 Author 목록을 반환하는 메서드를 정의한다. 

```java
  public List<Author> getAuthors() {
    List<Author> list = new ArrayList<Author>() {
      {
        add(Author.builder().name("홍길동").build());
        add(Author.builder().name("임꺽정").build());
        add(Author.builder().name("유관순").build());
        add(Author.builder().name("안중근").build());
      }
    };
    return list;
  }
```
Schema에 authors 쿼리를 정의한다. 
```
extend type Query { 
  books: [Book]
  authors: [Author]
}
```
다음과 같이 쿼리를 작성한다. 
```
query { 
  authors {
    name ,
    books {
      title
    }
  }
}
```
반환값은 다음과 같다. 
```
{
  "data": {
    "authors": [
      {
        "name": "홍길동",
        "books": null
      },
      {
        "name": "임꺽정",
        "books": null
      },
      {
        "name": "유관순",
        "books": null
      },
      {
        "name": "안중근",
        "books": null
      }
    ]
  }
```

이번에는 책목록과 저자 목록을 동시에 가져와보자. 
다음과 같이 쿼리를 작성한다. 
```
query { 
  authors {
    name ,
    books {
      title
    }
  }
  books { 
    title 
  }
}
```
결과는 다음과 같다. 

```
{
  "data": {
    "authors": [
      {
        "name": "홍길동",
        "books": null
      },
      {
        "name": "임꺽정",
        "books": null
      },
      {
        "name": "유관순",
        "books": null
      },
      {
        "name": "안중근",
        "books": null
      }
    ],
    "books": [
      {
        "title": "자바의 정석1"
      },
      {
        "title": "자바의 정석2"
      },
      {
        "title": "자바의 정석3"
      },
      {
        "title": "자바의 정석4"
      }
    ]
  }
}
```

### 객체 파라미터 쿼리 요청
객체를 파라미터로 사용하여 쿼리를 해보기로 한다. 객체를 파라미터로 넘기려면 input을 정의해야 한다. 
```
input AuthorInput { 
  name: String
}
```
Query Resolver에 메서드를 정의한다. 
```java
  public List<Author> getAuthorsByObject(Author author) {
    
    System.out.println(author.getName());

    List<Author> list = new ArrayList<Author>() {
      {
        add(Author.builder().name("홍길동").build());
        add(Author.builder().name("임꺽정").build());
        add(Author.builder().name("유관순").build());
        add(Author.builder().name("안중근").build());
      }
    };
    return list;
  }
```
스키마에 쿼리를 추가한다. 
```
input AuthorInput { 
  name: String
}

extend type Query { 
  authorsByObject(author: AuthorInput): [Author]
}
```  

쿼리를 작성한다. 
```
query { 
  authorsByObject(author: {name: "홍길동"}) {
    name
  }
 
}
```
결과는 다음과 같다. 
```json
{
  "data": {
    "authorsByObject": [
      {
        "name": "홍길동"
      },
      {
        "name": "임꺽정"
      },
      {
        "name": "유관순"
      },
      {
        "name": "안중근"
      }
    ]
  }
}
```

## Mutation 요청
Resolver에 메서드를 다음과 같이 작성한다. 4 개의 파라미터가 있다. 

```java

  public Post addPost(String id, String title, String category, String authorId) {
    return Post.builder().id(id).title(title).category(category).authorId(authorId).build();
  }
```

스키마는 다음과 같이 작성한다.

```
#The Root Mutation for the application
type Mutation {
    addPost(id: String!, title: String!, category: String, authorId: String) : Post!
}
```
mutation은 다음과 같이 작성한다. 
```
mutation {
  addPost(id:"Hong", title: "이것", category:" 잡담", authorId:"A1") {
    id,
    title,
    category,
    authorId
  }
}
```

### Input 사용하기 
다음과 같이 Resolver를 작성한다. 
```java
  public List<Post> addPostWithInput(Post post) {

    System.out.println(post.getTitle());

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
```

스키마에 다음과 같이 Input을 정의한다. 
```
input PostInput {
    title: String!
    category: String
    authorId: String!
}
```
mutation을 다음과 같이 작성한다. 
```

#The Root Mutation for the application
type Mutation {
    addPostWithInput(postInput: PostInput) : [Post]!
}
```

다음과 같이 mutation 요청을 작성한다. 
```
mutation {
  addPostWithInput(postInput: {
    title: "kim",
    category: "Park",
    authorId:"1111"
  }) {
    id,
    title, 
    category,
    authorId 
  }
}
```



## References
[GraphQL schema basics](https://www.apollographql.com/docs/apollo-server/schema/schema/#the-query-type)       
[쿼리 & 뮤테이션](https://graphql-kr.github.io/learn/queries/)    