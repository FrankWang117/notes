# setting scrollbar styles  
use scss
```
    // 设置滚动条的样式
    &::-webkit-scrollbar {
        width: 5px;
    }
    // 滚动槽
    &::-webkit-scrollbar-track {
        -webkit-box-shadow: inset006pxrgba(0,0,0,0.3);
        border-radius: 5px;
    }
    // 滚动条滑块
    &::-webkit-scrollbar-thumb {
        border-radius: 5px;
        background: #CCCCCC;
        -webkit-box-shadow: inset006pxrgba(0,0,0,0.5);
    }

    &::-webkit-scrollbar-thumb:window-inactive {
        background: #89B5E8;
    }
```
