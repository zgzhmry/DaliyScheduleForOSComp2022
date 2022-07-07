# 请注意，在阅读后续内容之前，请务必确保您已完成32 Rust Quizes，这些内容不能也不应用于学术不当的行为，本文仅为已完成32 Rust Quizes但对部分题目存在疑惑的同学提供参考（尽管在回答题目完成后会有解答，但是它是全英文的，这里将结合英文解答进行讲解）。受个人水平限制，不足和错误之处也请大家多多指正

**由于Github不支持自动生成目录，因此大家只能Ctrl+F查找想看的题目了。**

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
本质上是unsafe的匹配，1u8
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
这段代码比较长，主函数在````let S = f()````调用了一次````f()````，但是实际上这里的````S````应该是一个结构体而非一个变量，并不能作为函数````f()````中构建的S的所有者，因此这个S离开了作用域，激活了Drop，从而输出2。随后的那一句中又重新构建了一个S，激活了Display，在这个S被释放的时候又激活了Drop,因此打印12。
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
