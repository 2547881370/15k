# instanceof实现原理
```js
function new_instance_of(leftVaule , rightVaule){
    let rightProto  =  rightVaule.prototype
    leftVaule = leftVaule.__proto__;
    while(true){
        if(leftVaule === null){
            return false
        }
        if(leftVaule === rightProto){
            return true
        }
        leftVaule = leftVaule.__proto__ 
    }
}

class Person {}
let person = new Person()
new_instance_of(person , Person) //true
```