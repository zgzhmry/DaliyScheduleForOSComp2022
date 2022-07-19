# 请注意，在阅读后续内容之前，请务必确保您已完成32 Rust Quizes，这些内容不能也不应用于学术不当的行为，本文仅为已完成32 Rust Quizes但对部分题目存在疑惑的同学提供参考（尽管在回答题目完成后会有解答，但是它是全英文的，这里将结合英文解答进行讲解）。受个人水平限制，不足和错误之处也请大家多多指正

**由于Github不支持自动生成目录，因此大家只能Ctrl+F查找想看的题目了。更新：突然发现在知乎上可以看到张汉东老师写的解答（<https://zhuanlan.zhihu.com/p/101354383>），发现自己的解答当中有很多都存在错误，请同学们移步张老师的解答，这篇文章看着当玩笑就好**

## Quiz1
````rust
macro_rules! m {
    ($($s:stmt)*) => {
        $(
            { stringify!($s); 1 }
        )<<*
    };
}

fn main() {
    print!(
        "{}{}{}",
        m! { return || true },
        m! { (return) || true },
        m! { {return} || true },
    );
}
````
按照题目的解答，本题考查Rust的语法边界问题。**macro_rule**给出了一个输入规则，在这里````($($s:stmt)*)````的一个作用就是对零个或多个语句进行匹配（类似于match）。````stringify!($s)````的意思是将被匹配的语句($s)转化为字符串，````$( )<<*````则是对每个语句的分隔符。因此，依照这个宏规则，在mian函数当中的第一个输出````m! { return || true }````相当于就是````retun (|| true)````,最终是返回后面那个表达式的值，也就是1。而```` m! { (return) || true }````用括号将````return````进行了隔离，使得return成为了一个单独的表达式，这时候就是一个单纯的逻辑或运算，因此返回结果为1。````m! { {return} || true }````使用了花括号对````return````进行隔离，被隔离的部分就成为了一个单独的语句，所以最后执行的输出为两个语句的执行结果，因此为2。
## Quiz
````rust
struct S;

fn main() {
    let [x, y] = &mut [S, S];
    let eq = x as *mut S == y as *mut S;
    print!("{}", eq as u8);
}
````
本题
## Quiz
````rust
struct D(u8);

impl Drop for D {
    fn drop(&mut self) {
        print!("{}", self.0);
    }
}

struct S {
    d: D,
    x: u8,
}

fn main() {
    let S { x, .. } = S {
        d: D(1),
        x: 2,
    };
    print!("{}", x);

    let S { ref x, .. } = S {
        d: D(3),
        x: 4,
    };
    print!("{}", x);
}
````
## Quiz3
````rust
struct S {
    x: i32,
}

const S: S = S { x: 2 };

fn main() {
    let v = &mut S;
    v.x += 1;
    S.x += 1;
    print!("{}{}", v.x, S.x);
}
````
本题给出了一个结构体S，但是后面给出了一个const，将S中x的值指定为2，在后面的运算当中自然就不能被重新赋值。这个和C/C++的常量基本是一样的，因此不多作解释。
## Quiz5
````rust
trait Trait {
    fn p(self);
}

impl<T> Trait for fn(T) {
    fn p(self) {
        print!("1");
    }
}

impl<T> Trait for fn(&T) {
    fn p(self) {
        print!("2");
    }
}

fn f(_: u8) {}
fn g(_: &u8) {}

fn main() {
    let a: fn(_) = f;
    let b: fn(_) = g;
    let c: fn(&_) = g;
    a.p();
    b.p();
    c.p();
}
````
这里的代码虽然很长，但实际上就只是````fn()````当中有无&的问题，有就是2，没有就是1。
## Quiz6
````rust
use std::mem;

fn main() {
    let a;
    let a = a = true;
    print!("{}", mem::size_of_val(&a));
}
````
本题的核心是理解语句````let a = a = true;````的含义。Rust不能像C/C++那样在同一个表达式连续进行赋值，因此这里的````a = a = true````实质上应该理解为````a = (a = true)````。
## Quiz7
````rust
#[repr(u8)]
enum Enum {
    First,
    Second,
}

impl Enum {
    fn p(self) {
        match self {
            First => print!("1"),
            Second => print!("2"),
        }
    }
}

fn main() {
    Enum::p(unsafe {
        std::mem::transmute(1u8)
    });
}
````
````#[repr(u8)]````的作用是指定内存布局的方式，在这里指定的是依照8位对齐，后面给出了一个枚举，但是后面分配的方法很有意思，这里没有给后面的匹配模式添加前缀，这也就意味着，无论如何，都只能匹配First。
## Quiz10
````rust
trait Trait {
    fn f(&self);
}

impl<'a> dyn Trait + 'a {
    fn f(&self) {
        print!("1");
    }
}

impl Trait for bool {
    fn f(&self) {
        print!("2");
    }
}

fn main() {
    Trait::f(&true);
    Trait::f(&true as &dyn Trait);
    <_ as Trait>::f(&true);
    <_ as Trait>::f(&true as &dyn Trait);
    <bool as Trait>::f(&true);
}
````
本题在特性当中定义了一个了一个f函数，这个函数在实现的时候有两种不同的实现方式，在main函数的调用当中，所有的调用都是通过布尔类型对f进行调用，因此都输出2
## Quiz15
````rust
trait Trait {
    fn f(&self);
}

impl Trait for u32 {
    fn f(&self) {
        print!("1");
    }
}

impl<'a> Trait for &'a i32 {
    fn f(&self) {
        print!("2");
    }
}

fn main() {
    let x = &0;
    x.f();
}
````
在main函数的第一句，将变量````x````赋予了&0，因此符合u32，所以调用了第一种方法。
## Quiz18
````rust
struct S {
    f: fn(),
}

impl S {
    fn f(&self) {
        print!("1");
    }
}

fn main() {
    let print2 = || print!("2");
    S { f: print2 }.f();
}
````
这里结构体S，并且为S定义了一个结构体成员并为其设置了一个成员方法，在main函数调用的时候，实质上是调用的S当中的方法，也就是````print!("1")````
## Quiz19
````rust
struct S;

impl Drop for S {
    fn drop(&mut self) {
        print!("1");
    }
}

fn main() {
    let s = S;
    let _ = s;
    print!("2");
}
````
这道题就比较简单了，首先正常打印2，然后在主函数结束的时候S被释放，打印1。但是这道题的解答很有意思，提到了这里如果在````let _ = s;````移动了s，那么s会被立即释放，S的Drop就会被激活，最终的打印结果就是12。
## Quiz22
````rust 
macro_rules! m {
    ($a:tt) => { print!("1") };
    ($a:tt $b:tt) => { print!("2") };
    ($a:tt $b:tt $c:tt) => { print!("3") };
    ($a:tt $b:tt $c:tt $d:tt) => { print!("4") };
    ($a:tt $b:tt $c:tt $d:tt $e:tt) => { print!("5") };
    ($a:tt $b:tt $c:tt $d:tt $e:tt $f:tt) => { print!("6") };
    ($a:tt $b:tt $c:tt $d:tt $e:tt $f:tt $g:tt) => { print!("7") };
}

fn main() {
    m!(-1);
    m!(-1.);
    m!(-1.0);
    m!(-1.0e1);
    m!(-1.0e-1);
}
````
这里的所有带“-”号的输入，并没有被识别为负数，而是单独作为一个字符。
## Quiz23
````rust
trait Trait {
    fn f(&self);
    fn g(&self);
}

struct S;

impl S {
    fn f(&self) {
        print!("1");
    }

    fn g(&mut self) {
        print!("1");
    }
}

impl Trait for S {
    fn f(&self) {
        print!("2");
    }

    fn g(&self) {
        print!("2");
    }
}

fn main() {
    S.f();
    S.g();
}
````
本题为结构体S构建了两种不同的函数，这两种不同的函数有分别有两种不同的实现方法。在调用函数的时候会优先调用在结构体中声明的方法，因此当普通方法和特征方法在除了输出完全相同的时候，会执行普通方法。而对于g函数，这里在调用的时候是自动引用，自动引用不采用&mut，因此会调用特征方法。
## Quiz25
````rust
use std::fmt::{self, Display};

struct S;

impl Display for S {
    fn fmt(&self, formatter: &mut fmt::Formatter) -> fmt::Result {
        formatter.write_str("1")
    }
}

impl Drop for S {
    fn drop(&mut self) {
        print!("2");
    }
}

fn f() -> S {
    S
}

fn main() {
    let S = f();
    print!("{}", S);
}
````
这段代码比较长，主函数在````let S = f()````调用了一次````f()````，但是实际上这里的````S````应该是一个单元结构体而非一个变量，并不能作为函数````f()````中构建的S的所有者，因此这个S离开了作用域，激活了Drop，从而输出2。随后的那一句中又重新构建了一个S，激活了Display，在这个S被释放的时候又激活了Drop,因此打印12。
## Quiz26
````rust
fn main() {
    let input = vec![1, 2, 3];

    let parity = input
        .iter()
        .map(|x| {
            print!("{}", x);
            x % 2
        });

    for p in parity {
        print!("{}", p);
    }
}
````
map操作在值从迭代器当中消耗掉过后才会被调用，因此在这里闭包打印的数字将会和p的值交叉出现，并且是p值优先打印。
## Quiz28
````rust
struct Guard;

impl Drop for Guard {
    fn drop(&mut self) {
        print!("1");
    }
}

fn main() {
    let _guard = Guard;
    print!("3");
    let _ = Guard;
    print!("2");
}
````
这里的输出顺序是，正常打印一个3，然后在````let _ = Guard;````由于这里Guard没有赋予给任何变量，因此它被立即释放了，第一次激活了Drop,打印了一个1，之后又正常打印2，最后在main函数结束的时候，````_guard````被释放，第二次激活Drop。
## Quiz30
````rust
use std::rc::Rc;

struct A;

fn p<X>(x: X) {
    match std::mem::size_of::<X>() {
        0 => print!("0"),
        _ => print!("1"),
    }
}

fn main() {
    let a = &A;
    p(a);
    p(a.clone());
    
    let b = &();
    p(b);
    p(b.clone());
    
    let c = Rc::new(());
    p(Rc::clone(&c));
    p(c.clone());
}
````

## Quiz33
````rust
use std::ops::RangeFull;

trait Trait {
    fn method(&self) -> fn();
}

impl Trait for RangeFull {
    fn method(&self) -> fn() {
        print!("1");
        || print!("3")
    }
}

impl<F: FnOnce() -> T, T> Trait for F {
    fn method(&self) -> fn() {
        print!("2");
        || print!("4")
    }
}

fn main() {
    (|| .. .method())();
}
````
本题的重点在于理解main函数当中的语句是什么意思。这里让我想到了大一的时候有个老师跟我们开的玩笑，他说如果让他来出C语言的期末考试题，那么他一定会在第一题给一个超级长的表达式然后让大家判断优先级和输出结果——实际上这里就是这种情况。通过解析这个表达式的优先级，可以发现这个表达式在第一种特性的方法实现里面只会执行一次，但是在第二种方法的实现里面却由于````F: FnOnce() -> T````使得所有权出现转移，因此会再度执行。
