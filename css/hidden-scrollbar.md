# how to hidden scrollbar and the page can scroll
如果只需要兼容移动浏览器（Chrome 和 Safari），可以使用自定义滚动条的伪对象选择器
```
.father-div {
    &::-webkit-scrollbar{
        display: none;
    }
}
```
## 兼容IE 或 FireFox
就是在容器外面在嵌套一层 `overflow: hidden`，内部尺寸在限制尺寸和外部嵌套层一样，就变相隐藏了：
```
<style>
    .outer-container,.content {
        width: 200px;
        height: 200px;
    }
    .outer-container {
        position: relative;
        overflow: hidden;
    }
    .inner-container {
        position: absolute;
        left: 0;
        overflow-x: hidden;
        overflow-y: scroll;
    }

    /* for Chrome */
    .inner-container::-webkit-scrollbar {
        display: none;
    }
</style>

<div class="outer-container">
    <div class="inner-container">
       <div class="content">
           ......
       </div>
    </div>
</div>
```
