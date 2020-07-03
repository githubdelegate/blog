# 纯swift 项目集成opencv. 

1. 直接把opencv2.framework 拖到项目中去
2. 然后创建一个OC类，比如OpenCVWrapper，在头文件上面 添加`#import <opencv2/opencv.hpp>`， 这个一定要添加到最上面
3. 创建OC类的时候Xcode会提示创建桥接文件
4. 在桥接文件里面 `#import "OpenCVWrapper.h"`
5. cmd+b 编译一下，会提示错误，`Core.hpp header must be compiled as C++`
6. 把OpenCVWrapper.m 修改为 `.mm`
7. 再重新编译 ok
8. `重要` 如果`OpenCVWrapper.h`里面引入了`opencv`的头文件，就会一直编译失败 提示 `找不到c++`