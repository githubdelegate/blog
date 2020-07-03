# Swift 学习小结
## 1. Array 

Array 是结构体，属于值类型。
NSArray 是类，属于引用类型。
Array是否可以被修改完全是通过 var 和 let 关键字来决定的，Array 自身并不解决它是否可以被修改的问题。
使用 NSArray 和 NSMutableArCannot specialize non-generic type 'NSMutableArray'ray ，var 和 let 关键字就和数组是否可以被修改没关系了。它们只控制对应的变量是否可以被赋值成新的 NSArray 或 NSMutableArray 对象。



## 枚举

* 关联值  每个类型的可以使用不同类型作为关联值
* 原始值 
* 枚举递归 使用枚举本身来作为关联值


