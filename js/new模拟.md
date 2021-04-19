# new模拟

```js
function Otaku (name, age) {
    this.name = name;
    this.age = age;

    this.habit = 'Games';
}

Otaku.prototype.strength = 60;

Otaku.prototype.sayYourName = function () {
    console.log('I am ' + this.name);
}
```

> 模拟new
```js
function objectFactory(){
    let obj = new Object()

    // Constructor为传入的第一个参数,即是一个构造函数
    // 因为使用了slice,所以arguments也会被修改,将下标为0的参数移除
    let Constructor = [].slice.call(arguments)
    // 所以,此时的arguments是剩余参数了
    // Constructor是传入要被实例化的函数

    // 保存原型对象
    obj.__proto__ = Constructor.prototype
    
    // 将构造器执行,传入剩余参数
    let ret = Constructor.apply(obj, arguments)

    // 如果返回的是一个对象,则把对象return出去,如果什么都没有返回,则把实例返回
    return  typeof  ret === 'object' ? ret : obj
}
```

> 使用
```js
var person = objectFactory(Otaku , 'Kevin', '18');

console.log(person.name) // undefined
console.log(person.habit) // undefined
console.log(person.strength) // 60
console.log(person.age) // 18
```