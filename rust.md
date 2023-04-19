# rust

## 1. rust中的常见函数记录：

* as\_bytes 方法将 String 转化为字节数组
* 使用 iter 方法在字节数组上创建一个迭代器:

## 2. 字符串slice

*   字符串String中一部分值的引用，

    ```rust
    # fn main() {
    	let s = String::from("hello world");

    	let hello = &s[0..5];
    	let world = &s[6..11];
    # }
    ```

    从0开始，可以不写..之前的值，下面两个语句意思一致

    ```rust
    let slice = &s[0..2];
    let slice = &s[..2];
    ```

    同样若包含最后一个字节，..后面的值也可以省略

    ```
    let slice = &s[3..len];
    let slice = &s[3..];
    ```

    也可以同时舍弃这两个值来获取整个字符串的 slice

    ```
    let slice = &s[0..len];
    let slice = &s[..];
    ```

    ” 字符串 slice” 的类型声明写作 \&str:
*   结构体的..更新语法

    ```rust
    # struct User {
    #	active: bool,
    #	username: String,
    #	email: String,
    #	sign_in_count: u64,
    # }
    #
    fn main() {
    // --snip--
    #
    #	let user1 = User {
    #		email: String::from("someone@example.com"),
    #		username: String::from("someusername123"),
    #		active: true,
    #		sign_in_count: 1,
    #	};
    	let user2 = User {
    		email: String::from("another@example.com"),
    		..user1 //.. user1 必须放在最后,以指定其余的字段应从user1的相应字段中获取其值,
            	    //我们可以选择任何顺序为任意字段指定值,不用考虑结构体定义中字段的顺序。
    		};
    	}
    ```

    类单元结构体（没有任何字段的结构体）

增加属性来派生 Debug trait,并使用调试格式打印 Rectangle 实例

```rust
#[derive(Debug)]    //属性派生
struct Rectangle {
	width: u32,
	height: u32,
}
fn main() {
	let rect1 = Rectangle {
	width: 30,
	height: 50,
	};
	
	println!("rect1 is {:?}", rect1);//在{}中添加:？表示以debug模式不是Display格式
}
```

可以用可以使用 {:#?} 替换 println! 字符串中的{:?}，使输出变得更易读。

另一种使用 Debug 格式打印数值的方法是使用 dbg! 宏。dbg! 宏接收一个表达式的所有权(与 println! 宏相反,后者接收的是引用),打印出代码中调用 dbg! 宏时所在的文件和行号,以及该表达式的结果值,并返回该值的所有权。

```rust
#[derive(Debug)]
struct Rectangle {
	width: u32,
	height: u32,
}
fn main() {
	let scale = 2;
	let rect1 = Rectangle {
		width: dbg!(30 * scale),
		height: 50,
	};

	dbg!(&rect1);//使用dbg！宏
}
```

调用 dbg! 宏会打印到标准错误控制台流(stderr),与 println! 不同。

方法：imp,

枚举

`match vs` `if let`

*   嵌套路径来消除大量的use行

    use std::cmp::Ordering; use std::io;

    等价于

    use std::{cmp::Ordering, io};

    use std::io; use std::io::Write;

    等价于（可以在嵌套路径中使用self）

    use std::io::{self, Write};
* 将一个路径下 所有公有项引入作用域,可以指定路径后跟 \*

use std::collections::\*;

**let v = vec!\[1, 2, 3, 4, 5]; 两种获取元素的方法**

l\&v\[2]

等价于

v.get(2)

**使用 to\_string 方法从字符串字面值创建 String**

let data = "initial contents"; let s = data.to\_string();

// 该方法也可直接用于字符串字面值: let s = "initial contents".to\_string();

// 也可以使用 String :: from 函数来从字符串字面值创建 String。

let s = String::from("initial contents");

#### 更新字符串

**使用push\_str 和 push附加字符串**

可以通过 push\_str 方法来附加字符串 slice,从而使 String 变长

let mut s = String::from("foo"); s.push\_str("bar");

**push\_str 方法采用字符串 slice,因为我们并不需要获取参数的所有权。**

let mut s1 = String::from("foo"); let s2 = "bar";

s1.push\_str(s2); println!("s2 is {}", s2);

**push 方法被定义为获取一个单独的字符作为参数,并附加到 String 中。**

let mut s = String::from("lo"); s.push('l');

**使用 + 运算符或 format! 宏拼接字符串**

对于更 为复杂的字符串链接,可以使用 format! 宏:

```c
# fn main() {
let s1 = String::from("tic");
let s2 = String::from("tac");
let s3 = String::from("toe");

let s = format!("{}-{}-{}", s1, s2, s3);
# }
```

#### 函数和闭包的区别

闭包调用和函数调用非常像，区别是，闭包可以捕获外部变量，而函数不可以。

```rust
let out = 42;
fn = add(i: i32, j: i32) -> i32 { i + j }
let closure_annotated =  |i: i32, j: i32| -> i32 { i + j + out};
let closure_inferred = |i, j| i + j + out;
let i = 1;
let j = 2;

assert_eq!(3, add(i+j));
assert_eq!(45, closure_annotated(i, j));
assert_eq!(45, closure_inferred(i, j));
```

Rust中闭包实际上就是由一个匿名结构体和trait来组合实现的。

```rust
fn two_times_impl() -> impl Fn(i32) -> i32 {
	let i = 2;
	move |j| j * i
}
fn main(){
	let result = two_times_impl();
	assert_eq!(result(2), 4);
}
```

注意：在函数two\_times\_impl中最后返回闭包时使用了move关键字。这是因为在一般情况下，闭包默认会按引用捕获变量。如果将此闭包返回，则引用也会跟着返回，但是整个函数调用完毕之后，函数内的本地变量i就会被销毁，那么随闭包返回的变量i的引用，也将成为悬垂指针。所以如果不使用move关键字，编译器会报错。使用move关键字，将捕获变量i的所有权转移到闭包中，就不会按引用进行捕获变量，这样闭包才可以安全地返回。

在Rust中，函数和方法是有区别的。如果不是在impl块里定义的函数，就是自由函数。而在impl块中定义的函数被称为方法，类似面向对象的概念。

* Rust具名结构体是面向对象思想的一种体现
* 看起来像元组和具名结构体的混合体，叫元组结构体，特点是：字段没有名称，只有类型。

双端队列VecDeque实现了两种push方法，push\_front和push\_back。push\_front的行为像栈，push\_back的行为像队列。通过get方法加索引值可以获取队列中相应的值。

* 利用push\_front先后添加了元素1和2，但是相应的索引是1和0，正是栈数据结构先进后出的体现
* 通过push\_back先后添加元素3，4，5.索引相应位置为2，3，4

链表的显示引入：`std::collections::LinedList`

Key-Value映射表：HashMap和BTreeMap

Key必须是可哈希的类型，Value必须是在编译期已知大小的类型，HashMap是无序的，BTreeMap是有序的。类型签名是：HashMap\<K, V>和BTreeMap\<K, V>.

HashSet和BTreeSet其实就是HashMap\<K, V>和BTreeMap\<K, V>把Value设置为空元组的特定类型，等价于HashSet\<K, ()>和BTreeSet\<K, ()>。这两类集合的特性如下:

* 集合中的元素应该是唯一的，因为是Key-Value映射表的Key。
* 同理，集合中的元素应该都是可哈希的类型。

Rust提供的优先队列是基于二叉最大堆( Binary Heap)实现的。

智能指针（Smart Pointer）

Box的行为像引用，并且可以自动释放内存，所以我们称其为智能指针。

泛型和trait 这是rust系统中最重要的两个概念

泛型并不是rust特有的概念，为了方便代码的复用

trait

* trait是Rust唯一的接口抽象方式
* 可以静态生成，也可以动态调用
* 可以当作标记类型拥有某些特定行为的“标签”来使用

trait是对类型行为的抽象。

单元类型和单元结构体大小为0，由单元类型组成的数组大小也为0，ZST类型的特点是，它们的值就是其本身，运行时并不占用内存空间。ZSD类型代表的意义正是“空”

如果说ZST类型表示“空”的话，那么底类型就等价示“无”，底类型无值，而且可以等价于任意类型。

## trait

* 接口抽象。接口是对类型行为的统一约束
* 泛型约束。泛型的行为被trait限定在更有限的范围内。
* 抽象类型。在运行时作为一种间接的抽象类型去使用，动态的分发给具体的类型
* 标签trait。对类型的约束，可以直接作为一种“标签”使用。

### 接口抽象

特点：

* 接口中可以定义方法，并支持默认实现。
* 接口中不能实现另一个接口，但是接口之间可以继承。
* 同一个接口可以同时被多个类型实现，但不能被同一个类型实现多次。
* 使用impl关键字为类型实现接口方法。
* 使用trait关键字来定义接口。

孤儿规则：如果要实现某个trait，那么该trait和要实现该trait的类型至少有一个要在当前crate中定义。

关联类型Output必须指定具体类型。函数add的返回类型可以写Point,也可以写Self，也可以写Self::Output。

trait继承

Rust不支持传统的面向对象的继承，但是支持trait继承。子trait可以继承父trait中定义或实现的方法。

使用trait对泛型进行约束，叫做trait限定，格式如下：

fn generic\<T: MyTrait + MyOtherTrait + SomeStandardTrait > (t : T) { }

该泛型函数签名表达的意思是：需要一个类型T，并且该类型T必须实现MyTrait、MyOtherTrait和SomeStandardTrait中定义的全部方法，才能使用该泛型函数。

理解trait限定

类型可以看作具有相同属性值的集合。当声明变量let x: u32时，意味着x 属于u32,也就是说，x属于u32集合。

trait Paginate: Page + PerPage

trait也是一种类型，是一种方法集合，或者说，是一种行为的集合。Paginate是集合Page和PerPage交集的子集。

Rust中冒号代表集合的“包含于”关系，而加号则代表交集。

impl\<T: A + B > C for T

在Rust编程中，组合优于继承，Rust并不提供类型层面上的继承，Rust中所有的类型都是独立存在的，所以Rust中的类型可以看作语言允许的最小集合，不能再包含其他子集。而trait限定可以对这些类型集合进行组合，也就是求交集。

#### 抽象类型

Rust目前有两种方法来处理抽象类型：trait对象和impl Trait

trait对象

当trait对象在运行期进行动态分发时，也必须确定大小，否则无法为其正确分配内存空间，所以必须同时满足以下两条规则的trait才可以作为trait对象使用。

* trait的Self类型参数不能被限定为Sized
* trait中所有的方法都必须是对象安全的

对象安全的方法必须满足以下三点之一：

* 方法受Self: Sized约束
*   方法签名同时满足以下三点：

    * 必须不包含任何泛型参数。如果包含泛型，trait对象在虚表（Vtable）中查找方法时将不确定该调用哪个方法。
    * 第一个参数必须为Self类型或可以解引用为Self的类型（也就是说，必须有接收者，比如self,\&self，\&mut self, self: Box），没有接收者的方法对trait对象来说毫无意义）。
    * Self不能出现在除第一个参数之外的地方，包括返回值中。这是因为如果出现Self,那就意味着Self和self、\&self或\&mut self的类型相匹配。但是对于trait对象来说，根本无法做到保证类型匹配，因此这种情况下的方法是对象不安全的。总结来说：没有额外Self类型参数的非泛型成员方法。
    * trait中不能包含关联常量。

    Rust一共提供了5个重要的标签trait，都被定义在标准库std::marker模块中。分别是：

    * Sized trait : 用来标识编译期可确定大小的类型
    * Unsized trait: 实验特性，用于标识动态大小类型（DST）
    * Copy trait ： 用来标识可以按位复制其值的类型
    * Send trait： 用来标识可以跨线程安全通信的类型
    * Sync trait： 用来标识可以在线程间安全共享引用的类型

线程不安全的代码会因为共享内存而产生内存破坏行为。

多线程编程之所以有这么严重的问题，是因为系统级的线程是不可控的，编写好的代码不一定会按期望的顺序执行，会带来竟态条件。不同的线程同时访问一块共享变量也会造成数据竞争。竟态条件是不可能被消除的，数据竞争是有可能被消除的，而数据竞争是线程安全最大的“隐患”。

Rust无数据竞争并发的基石是：Send和Sync两个标签trait

* 实现了Send的类型，可以安全地在线程间传递值，也就是可以跨线程传递所有权。
* 实现了Sync的类型，可以跨线程安全地传递共享（不可变）引用。

有了这两个标签trait，就可以把Rust中所有的类型归为两类，可以安全跨线程传递的值和引用，以及不可以跨线程传递的值和引用。再配合所有权机制，带来的效果就是，Rust能够在编译期就检查出数据竞争的隐患，而不需要等到运行时再排查。

重叠规则和孤儿规则

在linux虚拟地址空间中，注意堆和栈，内存增长的方向，栈向下，由高地址像低地址增长，堆向上，由地址像高地址增长。（栈内存：用于支持CPU入栈或出栈的指令操作）

栈顶由栈指针寄存器ESP保存，起初栈顶指针指向栈底的位置，降低ESP的地址等价于开辟栈空间，增加ESP的地址等价于回收栈空间。栈内存最重要的作用是在程序运行过程中保存函数调用所要维护的信息。存储每次函数调用所需信息的记录单元被称为栈帧，有时也被称为活动记录

栈帧一般包括3方面内容：函数返回地址和参数，临时变量（包括函数内部的非静态局部变量和编译器产生的临时变量），保存的上下文。

EBP是帧指针，它指向当前栈帧的前一个固定的位置，而ESP始终指向栈顶。EBP指向的值是调用该函数之前的旧的EBR值，这样在函数返回时，就可以通过该值恢复到调用前的值。由EBP和ESP指针构成的区域就是一个栈帧，一般是指当前栈帧。

栈上数据的生命周期都是在一个函数调用周期内的。

## 堆

大顶堆和小顶堆

堆数据结构和堆内存并无直接的联系。

C语言中，通过调用malloc函数来申请堆内存，并可以通过free函数来释放它，在C++语言中，使用new和delete函数

堆分配算法：空闲链表（Free List） 和 位图标记（Bitmap）

## 智能指针和RALL

指针分为三种：

引用、原生指针（裸指针）和智能指针

引用就是Rust提供的普通指针，用&和\&mut操作符来创建，形如\&T和\&mut T。

原生指针是指形如\*const T 和 \*mut T 这样的类型

异同：

* 可以通过as操作符随意转换，例如\&T as \*const T和 \&mut Tas \*mut T
* 原生指针可以在unsafe块下任意使用，不受Rust的安全检查规则的限制，而引用则必须受到编译器安全检查规则的限制。

智能指针：

内存安全：（只要不出现以下内存问题即为内存安全）

* 使用未定义内存
* 空指针
* 悬垂指针
* 缓冲区溢出
* 非法释放未分配的指针或已经释放过的指针

Rust中使用Option类型来代替空指针，Option实际是枚举体，包含两个值：Some（T）和None，分别代表两种情况，有和无。

悬垂指针：指堆内存已被释放，但其本身还没有做任何处理，依旧指向已回收内存地址的指针，如果悬垂指针被程序使用，则会出现无法预期的后果。

内存泄露的原因

* 线程崩溃，析构函数无法调用。
* 使用引用计数时造成了循环引用。
* 调用Rust标准库中的forget函数主动泄露。

内存泄露是指没有对应该释放的内存进行释放，属于没有对合法的数据进行操作，内存不安全操作是对不合法的数据进行了操作，两者性质不同，造成的后果也不相同。

所有者拥有以下三种权限：

* 控制资源（不仅仅是内存）的释放
* 出借所有权，包括不可变（共享的）的和可变（独占）的
* 转移所有权

闭包对环境变量有三种捕获方式：

* 对于复制语义类型，以不可变引用（\&T）来捕获
* 对于移动语义类型，执行移动语义（move）转移所有权来捕获
* 对于可变绑定，如果在闭包中包含对其进行修改的操作，则以可变引用（\&mut）来捕获

借用规则：

* 规则1：借用的生命周期不能长于出借方（拥有所有权的对象）的生命周期。（防止出现悬垂指针）
* 规则2：可变借用（引用）不能有别名，因为可变借用具有独占性。
* 规则3：不可变借用（引用）不能再次出借为可变借用。

总结：共享不可变，可变不共享
