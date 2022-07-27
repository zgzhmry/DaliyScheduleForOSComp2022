# 配置环境
为了保证效果，笔者选用的是GithubClassroom，但是由于对这个东西并不熟悉，因此在使用中走了很多弯路。  
在配置环境的时候有以下注意事项：  
1.如果单纯按照<https://github.com/LearningOS/rust-based-os-comp2022/blob/main/scheduling.md>所给的步骤进行配置，会发现不能直接执行```rustlings```命令，这时候必须执行```cargo install --force --path .```(请注意不要遗漏掉最后面的小数点)才能够成功运行```rustlings```命令。  
2.请务必保证网络畅通，有条件最好科学上网，否则因为网络导致的错误非常令人头痛。

# 开始训练
在完成环境配置后，运行命令```rustlings watch```即可进入循环训练模式，从```exercises```文件夹中的每一章节的每一个文件开始，例如```variables```文件夹下的每个```.rs```文件，在完成任务后删除文件中的注释```// I AM NOT DONE```即可执行自动检查。
