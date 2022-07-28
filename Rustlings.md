# 配置环境
为了保证效果，笔者选用的是GithubClassroom，但是由于对这个东西并不熟悉，因此在使用中走了很多弯路。  
在配置环境的时候有以下注意事项：  
1.如果单纯按照<https://github.com/LearningOS/rust-based-os-comp2022/blob/main/scheduling.md>所给的步骤进行配置，会发现不能直接执行```rustlings```命令，这时候必须执行```cargo install --force --path .```(请注意不要遗漏掉最后面的小数点)才能够成功运行```rustlings```命令。  
2.请务必保证网络畅通，有条件最好科学上网，否则因为网络导致的错误非常令人头痛。  


# 开始训练
在完成环境配置后，运行命令```rustlings watch```即可进入循环训练模式，从```exercises```文件夹中的每一章节的每一个文件开始，例如```variables```文件夹下的每个```.rs```文件，在完成任务后删除文件中的注释```// I AM NOT DONE```即可执行自动检查。 默认的第一道题目为```intro```文件夹下的```intro1.rs```。
## 注意事项：
1.执行完自动检查后，需要自行点击下一道题目，而不是自动跳转，跳转方式为点击报错信息中的题目链接，也可以自行点击其它题目完成，但是就不会在完成本题后自动测评下一题。  
2.如果出现网络问题，导致在线桌面重新启动，有一定概率需要重新执行```cargo install --force --path .```后再执行```rustlings watch```才能继续。  
3.建议在涉及到指定字符串输出的地方直接复制粘贴题目中的输出，否则如果由于字符输入错误导致输出不正确，排查起来是比较困难的。  
# 一些特殊的题目  
**这些题目是笔者个人认为有可能造成解题者困惑的题目，仅供大家参考，受水平所限，请诸位多多指教。**  
1.if2
```rust
// if2.rs

// Step 1: Make me compile!
// Step 2: Get the bar_for_fuzz and default_to_baz tests passing!
// Execute the command `rustlings hint if2` if you want a hint :)


pub fn fizz_if_foo(fizzish: &str) -> &str {
    if fizzish == "fizz" {
        "foo"
    } else {
        1
    }
}

// No test changes needed!
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn foo_for_fizz() {
        assert_eq!(fizz_if_foo("fizz"), "foo")
    }

    #[test]
    fn bar_for_fuzz() {
        assert_eq!(fizz_if_foo("fuzz"), "bar")
    }

    #[test]
    fn default_to_baz() {
        assert_eq!(fizz_if_foo("literally anything"), "baz")
    }
}

```
本题问题在于一些边看手册边练习的同学可能不理解```mod tests```中代码的意义。实际上，这里的意思是检测给定输入是否产生了指定输出，例如，```fn foo_for_fizz()```的意义就是若函数```fizz_if_foo()```的输入为```"fuzz"```的情况下输出是否为```"foo"```，也就是说，只要依照三个函数的要求修改条件表达式即可通过测试。
