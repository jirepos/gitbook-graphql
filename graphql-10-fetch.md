# fetch() 사용하기 
[How to use GraphQL variables to give queries type safety](https://www.contentful.com/blog/2021/10/22/how-to-use-graphql-variables/)에 설명이 잘되어 있다. 



```jsx
const query = `{
  blogPostCollection(where: {slug: "what-is-a-rest-api"}) {
    items {
      slug
      title
      excerpt
      readingTime
    }
  }
}`;

const response = await fetch(
  `https://graphql.contentful.com/content/v1/spaces/${SPACE_ID}/environments/master`,
  {
    method: "POST",
    headers: {
      Authorization: `Bearer %ACCESS_TOKEN%`,
      "content-type": "application/json",
    },
    body: JSON.stringify({ query }),
  },
).then((response) => response.json());

console.log(response);
```

```jsx
const query = `query GetBlogPostBySlug($slug: String!) {
  blogPostCollection(where: {slug: $slug}) {
    items {
      slug
      title
      excerpt
      readingTime
    }
  }
}`;

const variables = { slug: "what-is-a-rest-api" };

const response = await fetch(
  `https://graphql.contentful.com/content/v1/spaces/${SPACE_ID}/environments/master`,
  {
    method: "POST",
    headers: {
      Authorization: `Bearer %ACCESS_TOKEN%`,
      "content-type": "application/json",
    },
    body: JSON.stringify({ query, variables }),
  },
).then((response) => response.json());

console.log(response);
```


```html
<script>
  let employees = fetch('http://localhost/graphql', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        query: `{
          employee {
            empId, 
            empName ,
            email
          }
        }`
      })
    })
    .then( (response) => response.json() )
  let employeearr; 
  employees.then(  (data) => {
      console.log(data.data.employee);
      employeearr = data.data.employee; 
  }); 
</script>
```

```html
<script>
   const query =`query ATN_CARDS($empId:String) {
      attendCards(empId: $empId) {
          empId,
          atndDate,
          atndYear,
          atndTime 
      }
   }`;
  const variables = { empId: 'M164' };

  let attendcards = fetch('http://localhost/graphql', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({  query, variables })
    })
    .then( (response) => response.json() )
  let attendcardarr; 
  attendcards.then(  (data) => {
      console.log(data.data.attendCards);
      attendcardarr = data.data.attendCards; 
  }); 
</script>
```