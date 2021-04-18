# bind实现

## 一
```
bind方法可以创建一个新的函数,当这个新的函数被调用时, bind的第一个参数当做运行的this , 之后的参数当做实际参数传入
```

```js
// 实现如下操作
var foo = {
    value: 1
};

function bar() {
	return this.value;
}

var bindFoo = bar.bind(foo);

console.log(bindFoo()); // 1
```

```js
// 第一版

Function.prototype.bind2 = function(content){
    // 这里保存self的作用是保存初始调用者, 这里即是bar函数
    let self = this;

    // 因为调用bind是返回一个函数,所以返回一个匿名函数
    return function() {
        // 在匿名函数执行时,这里是一个闭包,即将创建匿名函数时所保存的作用域里面会有一个self的值;apply执行这个函数,且将this改变成指定对象
        return self.apply(content)
    }
}

```

## 二
> bind方法还有一个特征,即在bind时可以传入参数,返回的新函数,如果继续传入实参,则传入的参数是bind时传入的剩余参数
```js
var foo = {
    value: 1
};

function bar(name, age) {
    console.log(this.value);
    console.log(name);
    console.log(age);

}

var bindFoo = bar.bind(foo, 'daisy');
bindFoo('18');
// 1
// daisy
// 18
```
```js
// 实现第二版
Function.prototype.bind2 = function(content){
    let self = this;

    // 将bind时传入的参数保存,注意是保存除了第一个参数以外的所有参数
    let args = Array.prototype.slice.call(arguments , 1)

    return function(){
        // 接着继续保存bind返回的参数,执行时所传入的参数,这样即可实现,bind时传入的参数和bind之后返回的函数传入的参数合并
        let bindArgs = Array.prototype.slice.call(arguments)
        return self.apply(content,args.concat(bindArgs))
    }
}
```

## 三
> 当bind返回的函数当构造函数使用时,bind指定的this会失效,但传入的参数还是会生效
```js
var value = 2;

var foo = {
    value: 1
};

function bar(name, age) {
    this.habit = 'shopping';
    console.log(this.value);
    console.log(name);
    console.log(age);
}

bar.prototype.friend = 'kevin';

var bindFoo = bar.bind(foo, 'daisy');

// 在new的时候, this的指向是obj
var obj = new bindFoo('18');
// undefined
// daisy
// 18
console.log(obj.habit);
console.log(obj.friend);
// shopping
// kevin
```
```js
// 实现第三版
Function.prototype.bind2 = function(content){
    // 这里的this是绑定函数
    let self =  this;
    
    let args = Array.prototype.slice.call(arguments, 1)

    var fBound = function(){
        let bindArgs = Array.prototype.slice.call(arguments)

        // 如果通过bind返回的函数是当构造函数使用,那么this instanceof Fbound是true,则使用当前的this
        //  反之就是当普通函数使用,使用之前的content
        return self.apply(this instanceof Fbound ? this : content , args.concat(bindArgs))
    }

    //  修改返回函数的 prototype 为绑定函数的 prototype，实例就可以继承绑定函数的原型中的值
    fBound.prototype = this.prototype
    
    return fBound
}
```
> 上述的实现有一个缺点,就是当修改返回函数的原型对象时,绑定函数的原型对象也会被修改,这是不可取的,所以改进方法可以使用一个空函数做空转
```js
// 第三版改版
Function.prototype.bind2 = function(content){
    let self = this;

    let args = Array/.prototype.slice.call(arguments,1)

    var Fnop = function(){}

    var fBound = function(){
        let bindArgs = Array.prototype.slice.call(arguments)

        return self.apply(this instanceof Fbound ? this : content , args.concat(bindArgs))
    }

    // 使用Fnop函数做空转
    Fnop.prototype = this.prototype

    fBound.prototype = new Fnop()

    // 这样如果直接修改返回函数的原型, 那么绑定函数的原型不会被修改
    return fBound 
}
```