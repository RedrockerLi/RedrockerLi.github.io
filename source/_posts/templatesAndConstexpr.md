---
title: 模板元编程和constexpr的比较
date: 2024-8-30 9:01:03
updated: 2024-8-30 9:01:03
tags: C++ 实验
excerpt: 时间和空间
---
本文要比较的是在使用两种方法实现编译期间计算常量时所耗费的编译时间，二进制文件大小和运行时间。

# 编译时间
``` shell
time g++ common_fib.cpp -o common.out
real    0m0.249s #时间太短，误差大

time g++ template_fib.cpp -o template.out
real    0m0.245s

time g++ constexpr_fib.cpp  -o constexpr.out -fconstexpr-ops-limit=100000000000000
real    1m2.543s #内存占用4G左右
```

# 二进制文件大小
``` shell
common.out    16520
constexpr.out 16520
template.out  16488
```
# 运行时间
**constexpr中只有fib(44)被提前计算**
``` shell
common.out    real    0m10.348s
constexpr.out real    0m6.397s
template.out  real    0m0.007s
```
# 测试完整代码
``` c++
#include <iostream>

int fib(int n){
    if(n<=1){
        return 1;
    }
    return fib(n-1)+fib(n-2);
}

int main(){
    std::cout << fib(20) << std::endl;
    //...
    std::cout << fib(44) << std::endl;
}
```

``` c++
#include <iostream>

constexpr int fib(int n){
    if(n<=1){
        return 1;
    }
    return fib(n-1)+fib(n-2);
}

int main(){
    std::cout << fib(20) << std::endl;
    //...
    std::cout << fib(43) << std::endl;
    constexpr int val = fib(44);
    std::cout << val << std::endl;
}
```

``` c++
#include <iostream>

template <unsigned n>
struct fib{
    enum{value=fib<n-1>::value+fib<n-2>::value};
};

template <>
struct fib<0>
{
    enum{value=1};
};

template <>
struct fib<1>
{
    enum{value=1};
};

int main(){
    std::cout << fib<20>::value << std::endl;
    //...
    std::cout << fib<44>::value << std::endl;
}
```