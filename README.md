# React.js

1、Do not use setState in componentDidMount   
componentDidMount 执行是在DOM渲染完成后，在这里面使用setState会触发重绘，建议在constructor或者componentWillMount，推荐使用constructor


# Vue.js  
  

# JS  
  
1、querySelector与getElementByid或者getElementsByClassName的区别   
querySelector只能获取元素某一时刻的元素，不能获取动态元素    
   
2、getBoundingClientRect   
用于获取某个元素相对于视窗的位置集合   
   
3、document.documentElement  ===  HTML   
在ie7及ie7以下的html元素坐标会从(2, 2)开始算起    


#### 页面布局代码
```
<div class="g-app-wrapper">
    <div class="g-sidebar"></div>
    <div class="g-main"></div>
</div>
.g-app-wrapper {
    display: flex;
    min-width: 1200px;
}
.g-sidebar {
    flex-basis: 250px;
    margin-right: 10px;
}
.g-main {
    flex-grow: 1;
}
```
