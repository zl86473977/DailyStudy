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
    

# source-map
一种 提供源代码到构建后代码映射 技术（如果构建后代码出错了，通过映射可以追踪代码错误）
> [inline-|hidden-|eval-|][nosources-][cheap-[module-]]source-map
* source-map: `外部`
    *  错误代码的准确信息 和 源代码的错误位置
* inline-source-map: `内联`
    * 只生成一个内联source-map
    * 错误代码的准确信息 和 源代码的错误位置
* hidden-source-map: `外部`
    * 错误代码错误原因，但是没有错误位置
    * 不能追踪到源代码错误，只能提示到构建后代码的错误位置（只隐藏源代码）
* eval-source-map: `内联`
    * 每一个文件都生成对应的source-map
    * 错误代码的准确信息 和 源代码的错误位置（会多一个文件hash值）
* nosources-source-map: `外部`
    * 错误代码的准确信息，但是没有任何源代码信息 （全部隐藏）
* cheap-source-map: `外部`
    * 错误代码的准确信息，源代码的错误位置
    * 只能精确到行，其他的可以精确到列
* cheap-module-source-map: `外部`
    * 错误代码的准确信息，源代码的错误位置
    * module会将loader的source map加入

`内联` 和 `外部` 的区别
1. 外部生成了文件，内联没有
2. 内联构建速度更快

开发环境：速度快，调试更友好
> 速度快(eval > inline > cheap ...)  val-cheap-source-map第一块
> 调试更友好 source-map > cheap-module-source-map > cheap-source-map
eval-source-map / eval-cheap-module-source-map

生产环境：源代码要不要隐藏，调试要不要友好
> 内联会让代码的体积变大，所以在生产环境不用内联
hidden-source-map
nosources-source-map


# oneOf 
配置的loader只会匹配一个
注意：不能有两个配置处理同一种类型

# 缓存
1. babel缓存
> 让第二次打包构建速度更快

    {
        test: /\.js$/,
        exclued: /node_modules/,
        loader: 'babel-loader',
        options: {
            presets: [
                '@babel/preset-env',
                {
                    useBuiltIns: 'usage',
                    corejs: { version: 3 },
                    targets: {
                        chrome: '60',
                        firefox: '50'
                    }
                }
            ],
            // 开启babel缓存
            // 第二次构建时，会读取之前的缓存
            cacheDirectory: true
        }
    }
2. 文件资源缓存
让代码上线运行缓存更好使用
> [hash:10]

hash: 每次webpack构建时会生成一个唯一的hash值
    
    防止静态资源被缓存，可以在打包后文件增加[hash:10]作为文件名称一部分。
    问题：因为js和css同时使用一个hash值，如果重新打包会导致所有缓存失效（可能我只改了一个文件）
> [chunkhash:10]

chunkhash: 根据chunk生成的hash值，如果打包来源于同一个chunk，那么hash值就一样

问题：js和css的hash值还是一样的，因为css是在js中被引入的，所以同属于一个chunk

> [contenthash:10]

contenthash: 根据文件的内容生成hash值，不同文件的hash值一定不一样

# tree shaking: 去除无用代码
前提：
1. 必须使用ES6模块化
2. 开启production

作用：
减少代码体积

package.json配置

    "sideEffects": false // 所有代码都没有副作用(都可以进行tree shaking)

问题：可能会把css 或者 @/babel/polyfill(副作用) 文件干掉

解决：
在package.json中进行配置，即匹配到的任何css文件都不进行Tree Shaking
    
    "sideEffects": ["
        *.css"
    ]


# code split

> 多入口多打包文件

    module.exports = {
        // 单入口
        // entry: './src/js/index.js',
        // 多入口: 有一个入口，最终输出就有一个bundle
        entry: {
            main: './src/js/index.js',
            test: './src/js/test.js',
        },
        output: {
            // 取文件名[name]
            filename: 'js/[name].[contenthash:10].js',
            path: resolve(__dirname, 'build')
        }
    }

> 独立并去重node_module代码

    module.exports = {
        // 单入口
        // entry: './src/js/index.js',
        output: {
            // 取文件名[name]
            filename: 'js/[name].[contenthash:10].js',
            path: resolve(__dirname, 'build')
        },
        // 可以将node_modules中代码单独打包一个chunk最终输出
        // 自动分析多入口chunk中，有么有公共的文件，如果有会打包成单独一个chunk
        optimization: {
            splitChunks: {
                chunks: 'all'
            }
        }
    }

> js代码

    // 通过js代码，让某个文件被单独打包成一个chunk
    // import动态导入语法：能将某个文件单独打包
    import(/* webpackChunkName: 'test' */./test)
        .then(({fun1, fun2}) => {
            fun1()
        })
        .catch(() => {
            console.log('文件加载失败了');
        })
        







