GCD的快速迭代


GCD快速迭代(遍历)

格式:
dispatch_apply(<#size_t iterations#>, <#dispatch_queue_t queue#>, <#^(size_t)block#>)

<#size_t iterations#>: 进行迭代的次数
<#dispatch_queue_t queue#>: 迭代执行的队列
<#^(size_t)block#>: 进行迭代的代码, 注意要在block中设置一个索引i, 用来控制迭代
迭代的注意点

虽然快速迭代会使用子线程来进行主要任务, 但是主线程也会参与到迭代任务当中, 但是调用的频率不足以影响主线程执行他的任务

注意不能使用主队列, 由于主线程也会参与迭代, 这样会造成主队列与主线程之间的死锁

执行的顺序不是固定的

如果使用了串行队列, 那么所有的任务都将会在主线程中进行, 这样快速迭代就没有任何的意义了


```
- (void)testMoveFile {
    NSString *fromPath = @"/Users/hufeng/Desktop/222";
    NSString *toPath = @"/Users/hufeng/Desktop/111";
    
    NSArray *fileArray = [[NSFileManager defaultManager] subpathsAtPath:fromPath];
    NSLog(@"%@", fileArray);
    
    dispatch_queue_t queue = dispatch_queue_create("123", DISPATCH_QUEUE_CONCURRENT);
    
    dispatch_apply(fileArray.count, queue, ^(size_t i) {
        NSString *fullFromPath = [fromPath stringByAppendingPathComponent:fileArray[i]];
        NSString *fullToPath = [toPath stringByAppendingPathComponent:fileArray[i]];
        
        [[NSFileManager defaultManager] moveItemAtPath:fullFromPath toPath:fullToPath error:nil];
        
        NSLog(@"%@ --- %@ --- %@", fullFromPath, fullToPath, [NSThread currentThread]);
        
    });
}
```