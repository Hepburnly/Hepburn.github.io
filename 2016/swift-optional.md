#Swift-Optional

谈到**swift**和**Objective-C**的区别，可能大家都会想到**Opational**（可选类型），**swift**中有，而**Objective-C**没有，接下来我们就来讲讲这个**Optional**。

简单来说就是“**?**”，顾名思义，就是问这个类型到底是为空呢，还是不为空呢？

##?
一、**swift**语言使用var定义变量，和别的语言不同，**swift**里不会自动给变量赋初始值，也就是说变量不会有默认值，所以要求使用变量之前必须要对其初始化。如果在使用之前不进行初始化的就会报错：

```
var str:String
//这里就会报错 
'str' without initial value prevents synthesized initializers
```
二、其实Optional是一个enum，里面有None和Some两种类型。所谓的nil就是Optional.None,非nil就是Optional.Some,然后会通过Some (T)包装(wrap)原始值，这也是为什么在使用Optional的时候要拆包（从enum里取出来原始值）的原因，也是PlayGround会把Optional值显示为类似{Some “hello world”}的原因，这里是enum Optional的定义：

```
enum Optional<</span>T> : LogicValue, Reflectable {
     case None
     case Some(T)
     init()
     init(_ some: T)
     
     /// Allow use in a Boolean context.
     func getLogicValue() -> Bool
     /// Haskell's fmap, which was mis-named
     func map<</span>U>(f: (T) -> U) -> U?
     func getMirror() -> Mirror
}
```
三、声明为Optional只需要在类型后面紧跟一个**？**即可。eg：

 **var str: String?**
 
 上面这个Optional的声明，意思不是“我声明了一个Optional的String值”，而是“我声明了一个Optional类型值，它可能包含一个String值，也可能什么都不包含”，也就是说实际上我们声明的Optional类型，而不是声明一个String类型，这一点很重要。
 
 一旦声明为Optional的，如果不显示的赋值就会有个默认值nil。判断一个Optional的值是否有值，可以用if来判断：
 
```
if str {
// do something with str
}
```
使用Optional值得时候需要在具体的操作，比如调用方法、属性、下标索引等前面加上一个？，如果是nil指，也就是Optional.None， 会跳转过后面的操作不执行，如果有值，就是Optional.Some，可能就会拆包（unwrap），然后对拆包后的指执行后面的操作，来保证执行这个操作的安全性。

四、**？**还可以用在安全地调用protocol类型方法上，eg：

```
@objc protocol Test {
      @optional func test(toPath: String) -> Bool;
}
@objc class Content: Test {
      //download method not be implemented
}
var delegate: Test = Test()
test?("some path")
```
因为上面的delegate是Test类型的，它的test方法是optional，所以它的具体实现有没有test方法是不确定的。swift提供了一种在参数括号前面加上一个？的方式安全地调用protocol的optional方法。

五、另外如果你需要向下面这样向下转型（Downcast），可能会用到as？： 

```
if let dataSource = object as? UITableViewDataSource {
   let rowsInFirstSection = dataSource.tableView(tableView, numberOfRowsInSection: 0)
}
```

##!
当然有**？**就有**！**，上面提到的Optional值拆包（unwrap）后才能得到原来值，然后才能对其操作，那怎么来拆包呢？拆包提到了几种方法。

一、硬解包，eg：
 
```
var str:String? = "you are a beatiful girl"
if str {
	let newStr = str!
}
```

二、可选绑定(Optional Binding)

```
var str:String? = "you are a beatiful girl"
let test = "No,you are a fatman"
if let newstr = str {
	let newStr = test + str
}
```
if let`或者`if var是可选绑定的两个关键字. 使用自然语言来描述上面这段代码的话, 意思就是如果str有值,解包它,并且将它的值赋值给newStr, 然后执行下面的条件语句; 如果str为空, 直接跳过条件语句块。

三、可选链式调用(Optional Chaining)

在解释optional chaining之前, 先来修改一下上面的例子. 这里重新创建一个名为Person的类, 包含两个可选类型的属性firstName和lastName. `findPersonInfo:`方法直接返回Person类的对象, 而不再是String.

```
class Person {
    var firstName: String?
    var lastName: String?
}
func findPersonInfo(littleName: String) -> Stock? {
    if (littleName == "two dog") {
        let a: Person = Person()
        a.firstName = "baba"
        a.lastName = "ali"
        return a
    } else if (littleName == "baby") {
        let b: Person = Person()
        b.firstName = "du"
        b.lastName = "bai"
        return b
    }
    return nil
}

//给alibaba 的lastName加长
if let person = findPersonInfo("two dog") {
    if let c = person.firstName {
        let d = person.lastName + "NB"
        print(d)
    }
}
```
用下面的更简单：

```
if let c = findPersonInfo("two dog")?.firstName {
       let d = person.lastName + "NB"
       print(d)
}
```