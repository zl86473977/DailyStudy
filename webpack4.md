# HMR: hot module replacement 热模块替换
作用：一个模块发生变化，只会重新打包这一个模块（而不是打包所有模块），极大提升构建速度
`样式文件`：可以使用HMR功能，因为style-loader内部实现了

`js文件`：默认不能使用HMR功能

> 需要修改js代码，添加支持HMR功能的代码
    
    if(module.hot) {
        // 一旦 ，module.hot为true,说明开启了HMR功能
        module.hot.accept('./xxx.js',function(){
            // 方法会监听xxx.js文件的变化，以但发生变化，其他模块不会重新打包构建
            // 会执行后面的回调函数
            xxx();//xxx文件里的xxx函数
        })
    }
    
注意：HMR功能对js的处理，只能处理非入口js文件的其他文件

`html文件`：默认不能使用HMR功能，同时会导致问题：html文件不能热更新（不用做HMR功能）
    解决：修改entry入口，将html文件引入，原字符串改为字符串数组(依旧无法使用HMR)
