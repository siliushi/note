# React.js

1、Do not use setState in componentDidMount
componentDidMount 执行是在DOM渲染完成后，在这里面使用setState会触发重绘，建议在constructor或者componentWillMount，推荐使用constructor


# Vue.js  
  
