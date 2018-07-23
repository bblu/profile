---
title: Pitfalls of REST-full Api designing
date: 2018-07-22 19:00
categories: blog
tags: [web]
---

The structure of rest-full api is good with business logic, do not design it to sql-like or nosql-like style. (eg:/user?filter='age>10',is really not good for web developer to use)，Before making it right or efficient, keep it as simple as possible or even stupid. 

### simple api has no pitfalls

they're clear as what they look down below.

```js
//get all users
app.get('/user', handler);

//get a user(id=101)
app.get('/user/101', handler);

//get telephone number of the 101 user
app.get('/user/101/tel', handler);
```

### page query

```js
reqUrl = '/user?page=1&limit=20'


```

### range query
```js
//get users where age >18 and age <=38
//this can make the point but the meaning of url is get age not the user.
reqUrl = '/user?field=age&min=18&max=38'
//
reqUrl = '/user?age=(18,38]'
//get user dept in (100,200)
reqUrl = '/user?dept={100,200}'

```
### modules with connection

```js
//get all users in dept 100
reqUrl = '/user?dept=100'
//get user from /user is better than /dept
reqUrl = '/dept/100/users'

//get all todos for user 101
reqUrl = '/todo?assignTo=101'
// is better than get them form /user
reqUrl = '/user/101/todo'
```

### global search

```js
//e:endWith, s:startWith,
reqUrl = '/search/user?name=sabc&age=[18,]' 

reqUrl = '/search/poi?lng=124.38697&lat=41.56321&limit=10' 

```