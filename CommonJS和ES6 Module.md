# CommonJS的关键词是exports、module.exports

CommonJS是2009年由JavaScript社区提出的包含了模块化的一个标准，后来被Node.js所采用并实现，也就是说我们在Node.js中用到的模块导入导出都是依照CommonJS标准来实现的；
  
# ES6Module的关键词是export、export default

该模块标准是在ES6时才被提出的，此后JS才具有了模块化这一特性

## CommonJS用法
    // util.js
    function speak() {
        console.log('三国志真好玩');
    }
    let name = 'zl'
    
    // 以下两种写法是一样的，不过显然有时候写法1看上去更便捷点
    // 导出写法1
    module.exports { speak, name }
    // 第1种导出方式是将需要导出的函数或变量存储到 module.exports 里面，其中 module.exports 原本是一个空对象
    // 导出写法2
    exports.speak = speak;
    exports.name = name;
    // 第2种导出方式中，exports 在内部其实是指向了 module.exports，所以当我们执行 exports.变量 或 exports.函数 时，其实就相当于把变量或函数存储到 module.exports 中
    
    // page.js
    const util = require('./util.js');
    util.speak() // 方法被执行
    util.name // zl
    
    
## ES6 module用法
    // util.js
    // 写法1
    export let name = 'zl'
    export function speak(){console.log('三国志真好玩');}
    // 第一种是单个的变量或函数导出，只需要直接在开头使用 export 关键字即可；
    
    // 写法2
    function speak() {
        console.log('三国志真好玩');
    }
    let name = 'zl'
    export {
        name,
        speak,
    }
    // 第二种情况是批量地把多个变量或函数导出，只需要把它们储存到一个对象中即可
    
    // 写法三
    export let count = 3 // 非默认导出的内容将失效
    export default show // 默认导出函数show
    // export后加default表示默认导出，导出的该变量或函数是匿名的（一个模块只能默认导出一次）
    
    // page.js
    import { speak, name } from './util.js';
    speak() // 方法被执行
    name // zl
    // 改名
    import { speak as talk, name as id } from './util.js';
    talk() // 方法被执行
    id // zl
    // 用 * 表示全部导入
    import * as util from './util.js'
    util.speak() // 方法被执行
    util.name // zl
    // 如果是写法三的默认导出呢
    import fun from './util.js'
    fun() // speak被调用

# 区别
1. 对于模块的依赖方式不一样，CommonJS导入是`动态`的，ES6Module导入是`静态`的.

CommonJS这种写法具体引入什么文件只有在代码运行时才确定，而ES6Module不可以如此书写，ES6Module的引入代码编译阶段就确定了

2. CommonJS导入是值拷贝，ES6 Module导入的是值的引用。

CommonJS
    // util.js
    function speak() {
        console.log('三国志真好玩');
        name = 'leo'
    }
    let name = 'zl'
    module.exports = {
        speak,
        name
    }
    // page.js
    const util = require('./util.js')
    console.log(util.name); // zl
    util.speak(); // 三国志真好玩
    console.log(util.name); // `zl`
    
ES6 Module
    // util.js
    export let name = 'zl'
    export function speak() {
        console.log('三国志真好玩');
        name = 'leo';
    }
    // page.js
    import { speak, name } from './common.js';
    console.log(name); // zl
    speak(); // 三国志真好玩
    console.log(name); // `leo`
