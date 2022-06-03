# Enum, Union 

## Enum 
Java에서 Enum을 정의한다. 
```java
package com.example.demo.user;

public enum AllowedColor {
   RED, GREEN, BLUE 
}
```


Query Resolver에 Enum을 반환하는 메서드를 만든다. 
```java
  public AllowedColor favoriteColor() {
    return AllowedColor.RED;
  }
```


Schema에 enum을 정의한다. 

```
enum AllowedColor {
  RED
  GREEN
  BLUE
}
```

쿼리 타입을 정의한다. 
```
extend type Query {
  favoriteColor: AllowedColor  # enum return type
}
```


쿼리를 요청한다. 
```
query MyFavoritedColor { 
  favoriteColor
}
```
결과는 다음과 같다. 

```
{
  "data": {
    "favoriteColor": "RED"
  }
}
```

이번에는 Enum값을 전달해보자. 

Query Resolver에 다음을 추가한다. 
```java
  public String avatar(AllowedColor allowedColor) {
    return allowedColor.name();
  }
```

Schema에 query 타입을 정의한다.
```
extend type Query {
  avatar(allowedColor:AllowedColor): String # string return type
}
```


쿠리를 요청한다. 
```
query MyAvatar { 
  avatar(allowedColor: RED)
}
```
결과는 다음과 같다. 
```
{
  "data": {
    "avatar": "RED"
  }
}
```



