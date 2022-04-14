# new关键字

    function Person(name, age) { // 这个就是构造函数
				this.name = name
				this.age = age
    }
    const person1 = new Person('小明', 20); // 这个是Person构造函数的实例
    const person2 = Person('小张', 30);
    console.log(person1)
    console.log(person2)

原本的构造函数是window对象的方法，如果不用new操作符而直接调用，那么构造函数的执行对象就 是window，即this指向了window;

当调用了Person('小张', 30)函数后，window对象多出了name和age两条属性;

现在用 new 操作符后，this 就指向了新生成的对象，可以在新生成的对象身上找到对应的 age 和 name

new 关键字内部干了如下三件事
1. 创建一个空对象，并使该空对象继承 Person.prototype
2. 执行构造函数，并将this指向刚刚创建的新对象
3. 返回新对象

源码实现如下

    function _new(Fn, ...args) {
        const obj = {};
        obj.__proto__ = Fn.prototype;
        const result = Fn.call(obj, ...args);
        // 这句很奇怪，返回基础数据和对象结果是不一样的
        return result instanceof Object ? result : obj;
    }


# prototype: 显式原型
# __ proto__: 隐式原型

一般，`构造函数`的prototype和其`实例`的__proto__是指向同一个地方的，这个地方就叫做`原型对象`

那什么是构造函数呢？俗话说就是，可以用来`new`的函数就叫构造函数，箭头函数不能用来当做构造函数





