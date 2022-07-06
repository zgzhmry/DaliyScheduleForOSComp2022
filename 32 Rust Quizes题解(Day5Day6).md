# 请注意，在阅读后续内容之前，请务必确保您已完成32 Rust Quizes，这些内容不能也不应用于学术不当的行为，本文仅为已完成32 Rust Quizes但对部分题目存在疑惑的同学提供参考（尽管在回答题目完成后会有解答，但是它是全英文的，这里将结合英文解答进行讲解）。受个人水平限制，不足和错误之处也请大家多多指正

**由于Github不支持自动生成目录，因此大家只能Ctrl+F查找想看的题目了。**

## 第一题
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
按照题目的解答，本题考查Rust的语法边界问题。**macro_rule**给出了一个输入规则，在这里````($($s:stmt)*)````的一个作用就是对零个或多个语句进行匹配（类似于match）。````stringify!($s)````的意思是将被匹配的语句($s)转化为字符串，````$( )<<*````则是对每个语句的分隔符。因此，依照这个规则，在mian函数当中的第一个输出````m! { return || true }````相当于就是
## 第二题
````rust
struct S;

fn main() {
    let [x, y] = &mut [S, S];
    let eq = x as *mut S == y as *mut S;
    print!("{}", eq as u8);
}
````
本题
## 第三题
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
