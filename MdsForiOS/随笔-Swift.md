#随笔-Swift
1. 对闭包最言简意赅的解释：可以捕获自身作用域之外的变量的函数，所以闭包就是函数，函数就是闭包

    * 案例1

            func abc(a: Int) -> String {
                 return String(a)
            }
            func test(e: (Int) -> (String)) {
                print(e(3) + "100")
            }
            test(e: abc)
            
    * 案例2斐波列函数生成器

            //这是AnySequnce提供的一个初始化方法，可以接受返回值为迭代器的函数作为输入
            let sequnce = AnySequence(<#T##makeUnderlyingIterator: () -> IteratorProtocol##() -> IteratorProtocol#>)
            // 直接用Next函数构建AnyIterator
            func fibIterator() -> AnyIterator<Int> {
                var state = (0, 1)
                return AnyIterator {
                    let comingUpNumber = state.0
                    state = (state.1, state.0 + state.1)
                    return comingUpNumber
                }
            }
            // 直接用遵循遍历器(遵循IteratorProtocol)构建Sequnce
            let fibSequnce = AnySequence(fibIterator)
            //序列创建数组
            print(Array(fibSequnce.prefix(10)))
            //[0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
            
            
2. 控制器交互过程

    1. 控制器A -> 控制器B的过程(push + 过程不打断)

            A: viewWillDisappear -> B: viewWillAppear -> A: viewDidDisappear -> B: 
            viewDidAppear
    2. 控制器B -> 控制器A的过程(pop + 过程不打断)

            B: viewWillDisappear -> A: viewWillAppear -> B: viewDidDisappear -> A: 
            viewDidAppear
    3. 控制器B -> 控制A的过程(pop + 用户左滑 + pop失败：pop到一半松手)

            B: viewWillDisappear -> A: viewWillAppear -> A: viewWillDisappear -> 
            A: viewDidDisappear -> B: viewWillAppear -> B: viewDidAppear

3. 设置手势的优先级

        longPress.minimumPressDuration = 0.1
        longPress.require(toFail: tap)
        
    设置`tap`的优先级高于`longPress`,此时设置`longPress`的`minimumPressDuration`为0.1s(默认是0.5s),没有什么效果，但是设置更长的时间是有效果的比如10s，估计是系统在决定响应这两个手势响应优先策略的时候出现，耗费了一些时间
    
4. iOS版本版本号适配
    项目 *QUBIC*适配与iOS9之后,所以Widget中需要对iOS9和iOS10进行单独适配
    
    1. `#available(iOSApplicationExtension 10.0, *)`
    2. `#available(iOS 10.0, *)`
    3. `@available(iOSApplicationExtension 10.0, *)`、
    4. `@available(iOS 10.0, *)`

    1、2用于条件语句中
    
        if #available(iOSApplicationExtension 10.0, *) {
          _ = Timer(timeInterval: 1.0, repeats: true, block: { (_) in
          print("doing something")
            })
        } else {
                    // Fallback on earlier versions
        }

    3、4用于修饰方法
    
        @available(iOSApplicationExtension 10.0, *)
        private func test() {
            _ = Timer(timeInterval: 1.0, repeats: true, block: { (_) in
            print("doing something")
            })
        }

    Xcode会智能填充2、4语句

5. 导出该文件及其子文件中所有类需要国际化的内容

        cd /Users/zhouweike/Desktop/国际化测试/国际化测试
        find ./ -name "*.swift" -print0 | xargs -0 genstrings -o en.lproj


    开发细节: 数组`isEmpty = true`的时候`removeLast`会导致crash，开发中用到此方法的时候，需要加以判断这个数据的`isEmpty` 或者是用`popLast`, `removeLast`不返回被删除的值，`popLast`会返回可选的被删除的值
    
         fatal error: can't remove last element from an empty collection
5. 删除文件之后，SVN更新之前，要现在SVN上把删除的文件移出版本控制，否则update之后，这些文件又回来了

6. 字符和ascii之间的转换

        let A_ASCII = UnicodeScalar("A")
        Character(unicodeScalarLiteral: self)

7. `deinit`函数
    TestViewController 和 BaseViewContorller都实现了`deinit`,即使TestViewController继承自BaseViewController，但是TestViewController被释放的时候，执行完TestViewContoller.deinit，依然会执行BaseViewController.deinit,没有`super.deinit`

8. 关于 **!** 和 **?**

    写NetworkingClient中删除设备的API的时候发现
    
        var name: String!
        name = "zwk"
        let result = "my name is \(name)" // my name is Optional("zwk")
        var name = ""
        name = "zwk"
        let result = "my name is \(name)" // my name is zwk
        let result = "\(name)" // Optional("zwk")
        print(result) // zwk 
        
    对于**!** 和 **?**修饰的字符串(其他类型应该同样)， 如果进行了字符串拼接，那么会自动加上
    **Optional**关键字修饰，如果不进行拼接，那么就只输入原内容, 所以**所以对于Not Optional 
    变量尽可能赋默认值，不用 ！修饰**, 不过也可以对这些变量进行`if let`解析(针对？和！)，代码
    如下
       
        if let name = name {
            let result: String = "my name is\(name)"
            print(result) // my name is zwk
        }
9. talbeviewdidsele不放到主队列中问题，1. push 不执行 2. push 执行慢， 3. 点击alert 的action无法push 4. 点击alert 的action， aler消失， 再次点击row， alert 不弹出， 在点击才能弹出

