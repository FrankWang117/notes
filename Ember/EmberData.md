## Models
在 Ember Data 中，每一个model 对象都是继承自 `Model` 类的子类实例。每一个model 对象中都定义了属性，关联关系和数据的作用。  
Model 中可以描述从服务器返回的属性的数据类型。比如，`Person` model 中定义了一个 `firstName` 属性--它是个string，还定义了一个`birthday`属性，它是个 date:  
```
//  app/models/person.js
import DS from 'ember-data';

export default DS.Model.extend({
    firstName: DS.attr('string'),
    birthday: DS.attr('date')
    })
```
model 中同样也可以描述它与其他对象之间的关系。比如，一个`order` 中有很多 `line-items`，一个`line-items`可以属于某个 `order`:  
(`order`-订单;`line-items`-可以理解为具体的商品)
```
//  app/models/order.js
import DS from 'ember-data';

export default DS.Model.extend({
    lineItems: DS.hasMany('line-item')
});
```
```
//  app/models/line-item.js
//  ...
export default DS.Model.extend({
    order: DS.belongsTo('order')
})；
```
Model (**`model`的实例是带有实际数据的**)并没有包含实际的数据，它只是定义了属性，关联关系和实例中的数据行为等。但是！我们可以把model 对应的实例称为`records`-它是包含具体数据的对象。  
## Records
`record` 是一个包含实际数据的 model 对象实例，它所包含的数据来自后端服务器。当然，在前端也可以生成一条 record ，然后把该条 record 保存到服务器去。  
每一条 record 通过它的 type 和唯一的 ID 来区分。  
比如，你现在正在编写一个通讯录应用，你或许会定义一个`person`model。每一个 `person`实例都会有个 type 属性 和一个 ID 属性。  
```
this.store.findRecord('person',1);  //  {id: 1,name: 'jack'}
```
ID 通常是在该属性第一个被保存进数据库时后端赋予的。不过我们也可以在客户端来生成ID。  
## Adapter
`adapter` 可以把Ember 对数据的请求(比方说： findRecord 方法) 翻译成我们的 http 请求。  
比如，如果我们的应用想要获取ID 为 1 的`Person`record，那么 Ember 要怎么载入这条数据？ 通过`HTTP` or `WebSocket`？如果是HTTP，那么 URL 是`/person/1`,还是 `/resources/people/1`?  
`adapter` 会负责来解决上述所有问题。当应用需要的数据在 store 中没有的时候，那么 store就会询问 `adapter`。 如果你修改一条 `record`并且保存了它，那么 store 就会将它递给 `adapter` 然后 `adapter` 会将对应的数据发送到服务器并且确认请求状态。  
`adapter` 使你可以完全忽略远程接口的实现方式，你只需要关注业务代码就可以了。  
## Caching
`store` 会自动把`record` 缓存下来。如果一条 `record` 之前被 load 过了，那么当第二次再需要它的时候，则会从store 的缓存中返回该 `record`。这样做可以减少数据在 客户端/服务器之间的传递次数。也能让 UI 迅速得以渲染。  
比如，你的应用将会第一次索取一个 ID 为 1 的 `person` record,这时该数据将从服务器获取。  
但是，下次，你的应用程序再索取ID 为 1 的`person` 时，store 会注意到它已经从服务器 load 并缓存了该信息。它不会发送请求，而是从缓存中为你的应用程序返回 ID 为 1 的 `person` record。此功能-我们可以把它称为 **身份映射**。  
使用身份映射非常重要，因为它可以确保你在UI 的某个部分所做的更改会传播到UI的其他部分。这也意味着你不必手动保持记录同步，你需要做的就是根据ID 获取你的数据，而不必担心应用的其他部分是否已经请求并加载它。  
从缓存中获取数据的一个缺点就是，可能会导致缓存中的数据是陈旧的。为了防止此陈旧数据长期存在问题，Ember Data 会在每次从 store 返回缓存记录时都会自动发出一个请求。 当新数据被载入进入时， `record` 会被更新，这是模板也会同步渲染。  
## Architecture Overview
当你的应用第一次向 `store` 索取 `record`时，store 会发现它没有本地副本并向 `adapter` 索要它。你的 `adapter` 将会从持久层中检索记录；通常持久层指的是后端的服务器。  
![流程](https://mmbiz.qpic.cn/mmbiz_png/uMxgUCBaqCmHwPWWbKviamOuzUksicniarEykRBSsuoIicAroSMicDbnRicQI2WbibeojFzTdSicUXMca0XWx0wDw4vQzw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)  
如上图所示，`adapter` 若无法立即返回请求的 `record`，就会向服务器发出异步请求，并且只有在该请求完成加载时才创建 `record`.  
由于这一过程是异步的，`store` 会立即返回一个 `promise`对象。类似的， store 中所有的与 `adapter` 交互的方法都会返回 Promise。  
一旦，服务器返回了请求成功的响应并返回 JSON payload， `adapter` 就会 resolve `promise` ,并把拿到的 JSON 数据交给 store。  
然后， `store`将会使用该 JSON 数据初始化 `record`，并且 `promise` resolve 后返回到你的应用中。  
![return](https://mmbiz.qpic.cn/mmbiz_png/uMxgUCBaqCmHwPWWbKviamOuzUksicniarEpftwV4giaGgnZib7jzHbndrVVLsOKYK49ImdCluxg3icAaWIAiaP6Rmhdw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)  
接下来，我们看一下 store 中已经有了某条 record 的缓存，那么获取数据的流程是什么样的：  
![](https://mmbiz.qpic.cn/mmbiz_png/uMxgUCBaqCmHwPWWbKviamOuzUksicniarEDCVJbOia8rx67xkScWicPtb61ZkBVElL46D3JgMpstEicqrXHuPPPyRYg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)  
在这种情况下，由于 store 已经知道该 record 在缓存中存在，那么它会立即把 `promise` resolve，并且不会询问`adapter`.  
Model, record, adapter, and store 是理解 Ember Data 的核心概念。
