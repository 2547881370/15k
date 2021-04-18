# this的指向

## 一. this的指向都是根据ECMAScript规范里面进行逻辑判断的
```js
var foo = 1;

// 对应的Reference是：
var fooReference = {
    base: EnvironmentRecord,
    name: 'foo',
    strict: false
};

/**------------------------*/
var foo = {
    bar: function () {
        return this;
    }
};
 
foo.bar(); // foo

// bar对应的Reference是：
var BarReference = {
    base: foo,
    propertyName: 'bar',
    strict: false
};
```

## 二. ECMAScript规范底层this指向的逻辑需要用到
## 2.1 GetBase方法 : 返回 reference 的 base value。
## 2.2 IsPropertyReference方法 : 如果 base value 是一个对象，就返回true。
```js
var foo = 1;

// fooReference 是 ref
var fooReference = {
    base: EnvironmentRecord,
    name: 'foo',
    strict: false
};

GetValue(fooReference) // 1;
// GetValue 返回对象属性真正的值，但是要注意：

// 调用 GetValue，返回的将是具体的值，而不再是一个 Reference

// 这个很重要，这个很重要，这个很重要。
```

## 三. 计算this逻辑如下
### 3.1 计算 MemberExpression 的结果赋值给 ref
### 3.2 判断 ref 是不是一个 Reference 类型
### 3.21 如果 ref 是 Reference，并且 IsPropertyReference(ref) 是 true, 那么 this 的值为 GetBase(ref)
### 3.22 如果 ref 是 Reference，并且 base value 值是 Environment Record, 那么this的值为 ImplicitThisValue(ref)
### 3.23 如果 ref 不是 Reference，那么 this 的值为 undefined


> 注意 : 简单理解 MemberExpression 其实就是()左边的部分
```js
    console.log(this)
}

foo(); // MemberExpression 是 foo

function foo() {
    return function() {
        console.log(this)
    }
}

foo()(); // MemberExpression 是 foo()

var foo = {
    bar: function () {
        return this;
    }
}

foo.bar(); // MemberExpression 是 foo.bar
```

## 示例
```js
var value = 1;

var foo = {
  value: 2,
  bar: function () {
    return this.value;
  }
}

//示例1
console.log(foo.bar());
//示例2
console.log((foo.bar)());
//示例3
console.log((foo.bar = foo.bar)());
//示例4
console.log((false || foo.bar)());
//示例5
console.log((foo.bar, foo.bar)());
```

```js
// 示例1计算过程
1. MemberExpression 计算结果是foo.bar
2. foo.bar且是一个Reference
var Reference = {
  base: foo,
  name: 'bar',
  strict: false
};
3. base value 为 foo，是一个对象，所以 IsPropertyReference(ref) 结果为 true。 true。
4. 因为IsPropertyReference是true , 所以 this = GetBase(ref)
```

```js
// 示例2计算过程
1. foo.bar 被 () 包住 , 实际上 () 并没有对 MemberExpression 进行计算，所以其实跟示例 1 的结果是一样的
```

```js
// 示例3计算过程
// (foo.bar = foo.bar)()
1. 有赋值操作符
2. 因为使用了 GetValue，所以返回的值不是 Reference 类型
3. 按照之前讲的判断逻辑: 如果 ref 不是Reference，那么 this 的值为 undefined
```

```js
// 示例4计算过程
// (false || foo.bar)()
1. 有逻辑与算法
2. 查看规范 11.11 Binary Logical Operators
3. 因为使用了 GetValue，所以返回的不是 Reference 类型，this 为 undefined
```

```js
// 示例5计算过程
// (foo.bar, foo.bar)()
1. 有逗号操作符
2. 查看规范11.14 Comma Operator ( , )
3. 因为使用了 GetValue，所以返回的不是 Reference 类型，this 为 undefined
```