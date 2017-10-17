# cloc 代码统计

## Chap 1: cloc 安装
   
在mac终端中执行安装命令.
```c
sudo npm install -g cloc
```

> NPM (Node Package Manager)
> NPM的全称是Node Package Manager ，是一个NodeJS包管理和分发工具，已经成为了非官方的发布Node模块（包）的标准。
>
> 安装 
> 1. 通过Homebrew
>  ```c
>  brew install node
>  ```
> 2. npm官网下载安装 [官网链接]()
>

## Chap 2: cloc 使用
   
在mac终端下 `cloc + 文件or目录`

```C
// 统计一个文件的代码
scott:~ scott$ cloc /Users/scott/Desktop/index.html 
       1 text file.
       1 unique file.                              
       0 files ignored.
 
github.com/AlDanial/cloc v 1.70  T=0.01 s (96.3 files/s, 2119.5 lines/s)
-------------------------------------------------------------------------------
Language                     files          blank        comment           code
-------------------------------------------------------------------------------
HTML                             1              6              0             16
-------------------------------------------------------------------------------
 
 
// 统计目录下的所有文件代码
scott:~ scott$ cloc /Users/scott/Desktop/BS\(16.11.15\) 
       5 text files.
       5 unique files.                              
       0 files ignored.
 
github.com/AlDanial/cloc v 1.70  T=0.03 s (144.8 files/s, 13584.7 lines/s)
-------------------------------------------------------------------------------
Language                     files          blank        comment           code
-------------------------------------------------------------------------------
HTML                             5             82             24            363
-------------------------------------------------------------------------------
SUM:                             5             82             24            363
-------------------------------------------------------------------------------

```

## Chap 3: cloc 官方手册
   
