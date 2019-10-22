- 1.base
```c++
#include <iostream>
//头文件 <iostream>
using namespace std;
//有些名字容易冲突，所以会使用命名空间的方式进行区分，具体来说就是加个前缀。比如 C++ 标准库里面定义了 vector 容器，
//你自己也写了个 vector 类，这样名字就冲突了。于是标准库里的名字都加上 std:: 的前缀，你必须用 std::vector 来引用。
//同理，你自己的类也可以加个自定义的前缀。


// main() 是程序开始执行的地方

int main()
//1  int main(void) 指的是此函数的参数为空，不能传入参数，如果你传入参数，就会出错。
//2 int main() 表示可以传入参数。

//标准规范中规定 main 函数的返回值为 int，一般约定返回 0 值时代表程序运行无错误，其它值均为错误号，但该约定并非强制。
{
	cout << "Hello World"; // 输出 Hello World
	return 0;
}
```
c语言 出现的#if 0 是预编译指令，表示之后的代码不执行。

C语言中预编译指令#if、#else和#endif指令一般配合使用。#if  后面的参数为真（非0）则执行#if  后面的模块。#if 后面的参数为假，则不执行#if 后面的模块。

此指令多用在调试的时候，有段代码不想删除，怕后面用到所以用 #if 0 来暂时注释掉，如果想用的话就用#if 1 来开启；例如： #if true 执行 #endif  #if false 跳过 #endif。
```c++
#if condition
  code1
#else
  code2
#endif
```
