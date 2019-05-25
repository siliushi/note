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
