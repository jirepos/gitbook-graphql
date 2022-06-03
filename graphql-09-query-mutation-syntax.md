# Query와 Mutation 구문


## 필드 
간단한 쿼리를 살펴본다. 
```
{
  hero {
    name
  }
}
```

{ name } 은 반환값에서 가져올 필드를 선택한다. 

결과는 다음과 같다. 
```
{
  "data": {
    "hero": {
      "name": "R2-D2"
    }
  }
}
```
필드는 객체를 참조할 수도 있다.  이 경우 객체 대한 필드를 하위 선택할 수 있다. 
```
{
  hero {
    name 
    # 쿼리에 주석을 쓸 수도 있습니다!
    friends {
      name 
    }
  }
}
```


결과는 다음과 같다. 

```
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```
위 예제에서, friends 필드는 배열을 반환한다. 


## 인자 
필드에 인자를 전달하는 기능을 추가하면, 훨씬 다양한 일을 할 수 있다. 
```
{
  human(id: "1000") {
    name
    height
  }
}
```
결과는 다음과 같다. 
```
{
  "data": {
    "human": {
      "name": "Luke Skywalker",
      "height": 1.72
    }
  }
}
```

인자는 다양한 타입이 될 수 있다 아래는 열거형 타입을 사용했다. 

```
{
  human(id: "1000") {
    name
    height(unit: FOOT)
  }
}
```
결과는 다음과 같다. 
```
{
  "data": {
    "human": {
      "name": "Luke Skywalker",
      "height": 5.6430448
    }
  }
}
```


## 별칭
필드의 결과를 원하는 이름으로 바꿀 수 있다. 
```
{
  empireHero: hero(episode: EMPIRE) {
    name
  }
  jediHero: hero(episode: JEDI) {
    name
  }
}
```
결과는 다음과 같다. 
```
{
  "data": {
    "empireHero": {
      "name": "Luke Skywalker"
    },
    "jediHero": {
      "name": "R2-D2"
    }
  }
}
```
위 예제에서 두 hero 필드는 서로 충돌하지만, 서로 다른 이름의 별칭을 지정할 수 있으므로 한 요청에서 두 결과를 모두 얻을 수 있다.

## 작업이름 
지금까지는 query 키워드와 query 이름을 모두 생략 한 단축 문법을 사용했지만, 실제 애플리케이션에서는 코드를 덜 헷갈리게 작성하는 것이 좋다.

다음은 query 를 작업 타입, HeroNameAndFriends 를 작업 이름 으로한 예제이다. 
```
query HeroNameAndFriends {
  hero {
    name
    friends {
      name
    }
  }
}
```
작업 타입 은 쿼리(query), 뮤테이션(mutation), 구독(subscription) 이 될 수 있으며, 어떤 작업의 타입인지를 기술한다. 



결과는 다음과 같다. 
```
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

## 변수 
지금까지는 모든 인자를 쿼리 문자열 안에 작성했다. 그러나 대부분의 응용프로그램에서 필드에 대한 인자는 동적이다. 

GraphQL은 동적 값을 쿼리에서 없애고, 이를 별도로 전달하는 방법을 제공한다. 이러한 값을 변수라고 한다. 


변수를 사용하기 위해서는 다음 세 가지 작업을 해야 한다. 

1. 쿼리안의 정적 값을 $variableName 으로 변경한다.
2. $variableName 을 쿼리에서 받는 변수로 선언한다.
3. 별도의 전송규약(일반적으로는 JSON) 변수에 variableName: value 을 전달한다. 

```
query HeroNameAndFriends($episode: Episode) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}

{
  "episode": "JEDI"
}
```

### 변수 정의 
변수 정의는 위 쿼리에서 ($episode: Episode) 부분이다. 정적타입 언어의 함수에 대한 인자 정의와 동일하다. $ 접두사가 붙은 모든 변수를 나열하고 그 뒤에 타입(이 경우 Episode)이 온다.


선언된 모든 변수는 스칼라, 열거형, input object type이어야 ㅎ한다. 


### 변수 기본값 
타입 선언 다음에 기본값을 명시하여 쿼리의 변수에 기본값을 할당할 수도 있다.
```
query HeroNameAndFriends($episode: Episode = "JEDI") {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```



## 지시어 
위에서는 동적 쿼리를 구현하기 위해 변수를 사용하여 문자열 보간 작업을 피하는 방법에 대해 알아보았다. 인자에 변수를 전달하면 이러한 문제를 상당히 해결할 수 있지만, 변수를 사용하여 쿼리의 구조와 형태을 동적으로 변경하는 방법이 필요할 수도 있다. 

```
query Hero($episode: Episode, $withFriends: Boolean!) {
  hero(episode: $episode) {
    name
    friends @include(if: $withFriends) {
      name
    }
  }
}


{
  "episode": "JEDI",
  "withFriends": false
}
```
지시어는 필드나 프래그먼트 안에 삽입될 수 있으며 서버가 원하는 방식으로 쿼리 실행에 영향을 줄 수 있습니다. 코어 GraphQL 사양에는 두 가지 지시어가 포함되어 있으며, 이는 GraphQL 서버에서 지원해야 한다. 

* @include(if: Boolean): 인자가 true 인 경우에만 이 필드를 결과에 포함합니다.
* @skip(if: Boolean) 인자가 true 이면 이 필드를 건너뜁니다.




## 프래그먼트 
프래그먼트를 사용하면 필드셋을 구성한 다음 필요한 쿼리에 포함시킬 수 있다. 다음은 프래그먼트을 사용하여 위 상황을 해결하는 방법의 예제이다. 

```
{
  leftComparison: hero(episode: EMPIRE) {
    ...comparisonFields
  }
  rightComparison: hero(episode: JEDI) {
    ...comparisonFields
  }
}
​
fragment comparisonFields on Character {
  name
  appearsIn
  friends {
    name
  }
}
```
결과는 다음과 같다. 
```
{
  "data": {
    "leftComparison": {
      "name": "Luke Skywalker",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ],
      "friends": [
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        },
        {
          "name": "C-3PO"
        },
        {
          "name": "R2-D2"
        }
      ]
    },
    "rightComparison": {
      "name": "R2-D2",
      "appearsIn": [
        "NEWHOPE",
        "EMPIRE",
        "JEDI"
      ],
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```


### 프래그먼트 안에서 변수 사용하기 
쿼리나 뮤테이션에 선언된 변수는 프래그먼트에 접근할 수 있다.
```
query HeroComparison($first: Int = 3) {
  leftComparison: hero(episode: EMPIRE) {
    ...comparisonFields
  }
  rightComparison: hero(episode: JEDI) {
    ...comparisonFields
  }
}

fragment comparisonFields on Character {
  name
  friendsConnection(first: $first) {
    totalCount
    edges {
      node {
        name
      }
    }
  }
}
```

```
{
  "data": {
    "leftComparison": {
      "name": "Luke Skywalker",
      "friendsConnection": {
        "totalCount": 4,
        "edges": [
          {
            "node": {
              "name": "Han Solo"
            }
          },
          {
            "node": {
              "name": "Leia Organa"
            }
          },
          {
            "node": {
              "name": "C-3PO"
            }
          }
        ]
      }
    },
    "rightComparison": {
      "name": "R2-D2",
      "friendsConnection": {
        "totalCount": 3,
        "edges": [
          {
            "node": {
              "name": "Luke Skywalker"
            }
          },
          {
            "node": {
              "name": "Han Solo"
            }
          },
          {
            "node": {
              "name": "Leia Organa"
            }
          }
        ]
      }
    }
  }
}
```


## 뮤테이션
쿼리와 마찬가지로 뮤테이션 필드가 객체 타입을 반환하면 중첩 필드를 요청할 수 있다. 이는 변경된 객체의 새로운 상태를 가져오는 데에 유용하다. 간단한 뮤테이션 예제를 살펴 보겠다.

```
mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
  createReview(episode: $ep, review: $review) {
    stars
    commentary
  }
}

{
  "ep": "JEDI",
  "review": {
    "stars": 5,
    "commentary": "This is a great movie!"
  }
}
```






## References
[Query & Mutation](https://graphql-kr.github.io/learn/queries/)    