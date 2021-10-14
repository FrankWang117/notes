## Finding Records
Ember 提供了用来检索数据的接口。  
### Retrieving(检索) a Single Record
通过给 `store.findRecord()` 方法传入 model 名称 和 ID 可以查找某条 record。该方法将会返回包含 record 对象的 promise 对象:  
```
//  GET /blog-posts/1
this.store.findRecord('blog-post',1)    // => GET /blog-posts/1
    .then(function(blogPost){
            //  Do something with 'blogPost'
        });
```
通过给`store.peekRecord()` 方法传入 model 名称和 ID ，可以只在本地 store 中查找某条 record。它仅会在本地 store 中有数据的情况下返回:  
```
let blogPost = this.store.peekRecord('blog-post', 1); // => no network request
```
### Retrieving Multiple Records
通过 `store.findAll()` 方法查找某类型所有的 records:  
```
//  GET /blog-posts
this.store.findAll('blog-post') // => GET /blog-posts
    .then(function(blogPosts){
            // Do something with 'blogPosts'
        });
```
通过 `store.peekAll()` 方法在本地 store 中查找某类型的所有 records:
```
let blogPosts = this.store.peekAll('blog-post'); // => no network request
```
`store.findAll()` 返回的是包含了 `DS.RecordArray` 对象的 `DS.PromiseArray` 对象;  
`store.peekAll()` 则直接返回了 `DS.RecordArray`对象。  
需要注意的是， `DS.RecordArray` 不是一个单纯的 js数组，它是实现了 `Ember.Enumerable` 接口的对象。这非常重要，比如，如果你要通过index 来获取数据中某个元素，`[]`符号是不能用的，你需要使用 `objectAt(index)`方法。  
### Querying for Multiple Records
Ember Data 提供了查询 records 的标准。调用 `store.query()` 将会发起一个 `GET` 请求。并且会把传入它的第二个对象参数序列化为 query params。该方法的返回值与 `findAll()` 一样，也是返回 `DS.PromiseArray` 对象。  
下面例子，我们将会查询名为 `Peter` 的 `person` 对象：  
```
//  GET to /person?filter[name]=Peter
this.store.query('person', {
    filter: {
        name: 'Peter'
    }
}).then(function(peters) {
    // Do something with 'peters'
})
```
### Querying for A Single Record
如果你正在使用的 adapter 支持接受单条返回数据，那么可以使用 `store.queryRecord()` 方法，该方法通过在 adapter中定义同名的方法来实现。  
比如，服务器有个接口可以返回单条record：  
```
//  GET /api/current_user
{
    user: {
        id: 1234,
        username: 'admin'
    }
}
```
在 User model 对应的adapter 中定义 `queryRecord()` 如下：  
```
//  app/adapters/user.js

import DS from 'ember-data';
import $ from 'jquery';

export default DS.Adapter.extend({
    queryRecord(store,type,query) {
        return $.getJSON('/api/current_user');
    }
});
```
然后调用 `store.queryRecord()`,就会向服务器发送请求:  
```
store.queryRecord('user',{}).then(function(user){
    let username = user.get('username');
    console.log(`Currently logged in ad ${username}`);
});
```
与上面提到的 `store.query()` 的情况一样，查询对象也可以传递给 `store.queryRecord()` 方法，并且可供适配器的 queryRecord() 用于限定请求。但是，适配器必须返回单个模型对象，而不是包含一个元素的数组，否则，Ember Data 将抛出异常。  
请注意，Ember 的默认 JSON API adapter 不直接支持 queryRecord() 功能，因为它依赖于以数组形式返回结果的 REST 请求定义。  
如果你的远程 API 或者 adapter 只返回数组，但是你又只需要获取单条record，那么你可以向下面这样使用 query():  
```
//    GET to /users?filter[email]=tomster@example.com

tom = store.query('user', {
    filter: {
        email: 'tomster@example.com'
    }
}).then(function(users) {
    return users.get("firstObject");
});
```
