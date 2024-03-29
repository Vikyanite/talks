# chmod 777是什么意思

Linux chmod（英文全拼：change mode）命令是控制用户对文件的权限的命令

## 为什么是chmod 777而不是77也不是7

Linux/Unix 的文件调用权限分为**三级** : 文件所有者（Owner）、用户组（Group）、其它用户（Other Users）

所以chmod 后面跟的一般都是三个数字，而不是77或者7

## 为什么是7，7代表什么

因为Linux把操作分为三种 **r(read), w(write),x(execute)**，然后分别给这三种权限分配了值——**r=4,w=2,x=1**

#### 为什么是4，2，1而不是其他的组合？

因为这三个数字十分巧妙。4，2，1可以组合出0-7的任一数字（4，2，1每个都只能选一次或者不选），那么我们就可以**用一个数字就能看出是4，2，1哪几个的组合**。

以下给出全部的权限表：

| #    | 权限           | rwx  | 二进制 |
| :--- | :------------- | :--- | :----- |
| 7    | 读 + 写 + 执行 | rwx  | 111    |
| 6    | 读 + 写        | rw-  | 110    |
| 5    | 读 + 执行      | r-x  | 101    |
| 4    | 只读           | r--  | 100    |
| 3    | 写 + 执行      | -wx  | 011    |
| 2    | 只写           | -w-  | 010    |
| 1    | 只执行         | --x  | 001    |
| 0    | 无             | ---  | 000    |

[chmod 777是什么意思](https://www.cnblogs.com/Vikyanite/p/17085448.html)