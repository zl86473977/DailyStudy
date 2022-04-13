## 步骤
1. 运行 npm run xxx的时候，npm 会先在当前目录的 node_modules/.bin 查找要执行的程序，如果找到则运行；
2. 没有找到则从全局的 node_modules/.bin 中查找，npm i -g xxx就是安装到到全局目录；
3. 如果全局目录还是没找到，那么就从 path 环境变量中查找有没有其他同名的可执行程序。

node_modules/.bin里的文件是哪来的
1. 可能是package-lock.json里的
2. 可能是安装包的时候包里的package.json里来的

可以看到如下类似的代码

    "bin": {
        "vue-cli-service": "bin/vue-cli-service.js"
    },

无需关注vue-cli-service.js是干啥的

.bin目录下的文件提供了几种兼容性的软链接文件,文件里无实质代码,只运行了路径文件
