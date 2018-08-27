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
    #parent{
        height: 100%;
        width: 100%;
        overflow: hidden;
    }

    #child{
        width: 100%;
        height: 100%;
        overflow-y: scroll;
        padding-right: 17px; /* Increase/decrease this value for cross-browser compatibility */
        box-sizing: content-box; /* So the width will be 100% + 17px */
    }
</style>

```
第二种办法：
```
.parent {
    position: relative;
    width: 100%;
    overflow: hidden;

    .child {
        position: absolute;
        top: 0;
        right: -15px;
        bottom: 0;
        left: 0;
        overflow: auto;
    }
}
```
