# 日常bug记录
        
* 有关导航控制器的bug： App莫名的卡住按住home进入后台之后在回来就OK了

    bug发生时导航控制器的状况：导航条被隐藏，用一`view`代替了导航条
    猜测原因一：
    
    1. 是系统的 NavigationController 的缺陷, 在 NavigationController 的根页面往右滑两三下, 再执行 push 的时候就会出现这种问题, 可以重写 NavigationController 解决
    
    [解决方式](http://www.cocoachina.com/bbs/read.php?tid-335552-page-2.html)
    
    >       - (void)navigationController:
        (UINavigationController )navigationController didShowViewController:    
        (UIViewController )viewController animated:(BOOL)animated {
        if ([navigationController respondsToSelector:@selector(interactivePopGestureRecognizer)]) {
        navigationController.interactivePopGestureRecognizer.enabled = YES;
        }
        /* if rootViewController, set delegate nil */
           if (navigationController.viewControllers.count == 1) {
        navigationController.interactivePopGestureRecognizer.enabled = NO;
        navigationController.interactivePopGestureRecognizer.delegate = nil;
              }
                     }
        在判断当前为rootController, 设置手势无效, 而且必须设置delegate为nil, 这样的话就可以解决卡死的问题了. 
    
    猜测原因一：
    
    1. 底部Tabbar隐藏、显示调用频繁，或者重复调用
    2. 右划返回手势冲突
    
    1、2导致 在RootViewController右划返回手势也可以响应，因为没有上一级页面，导致整个程序页面不响应
    对应的解决方式[在一级页面进入和离开时 分别添加禁用和开启右滑返回手势](http://blog.csdn.net/yuanpeng1014/article/details/71425725)


    >       -(void)viewDidAppear:(BOOL)animated{
            [super viewDidAppear:animated];
            [self forbiddenSideBack]; 
           }
          - (void)viewDidDisappear:(BOOL)animated {
            [super viewDidDisappear:animated];
            [self resetSideBack];   
        }
        -(void)forbiddenSideBack{
            self.isCanSideBack = NO;
            //关闭iOS右滑返回
            if([self.navigationController   
            respondsToSelector:@selector(interactivePopGestureRecognizer)]) {
           self.navigationController.interactivePopGestureRecognizer.delegate=self;
            }
        }
        - (void)resetSideBack {
            self.isCanSideBack=YES;
            //开启ios右滑返回
            if([self.navigationController 
            respondsToSelector:@selector(interactivePopGestureRecognizer)]) {
                self.navigationController.interactivePopGestureRecognizer.delegate = nil;
            }
        }
        - (BOOL)gestureRecognizerShouldBegin:(UIGestureRecognizer*)gestureRecognizer {   
            return self.isCanSideBack;
        }
        
        
    上面两种情况分析的解决方式都是在`rootViewController`的时候，设置导航控制器的左滑手势交互
    `FALSE`,并设置了代理为`NIL`。项目 **qubic**中采用了第一种方式，但是没有设置`navigationController.interactivePopGestureRecognizer.delegate = nil`


* tableView的删除动画

    >       deleteRows(at indexPaths: [IndexPath], with animation: 
        UITableViewRowAnimation)
        //调用之前确定先移除数据源中相应列的数据，否则crash信息类似如下Invalid update: 
        invalid number of rows in section 0.  The number of rows contained in an 
        existing section after the update (2) must be equal to the number of rows 
        contained in that section before the update (2), plus or minus the number 
        of rows inserted or deleted from that section (0 inserted, 1 deleted) and 
        plus or minus the number of rows moved into or out of that section (0 
        moved in, 0 moved out)
        

    同比tableView的插入动画

* SVN cleanup 问题和lock问题
    bug复现的步骤：更新**QUBIC**项目的时候，更新进度阻塞不前，点击cancle之后，选择强制退出。然后出现如下bug，点击open没有反应，且clean处于不可点击状态
    ![svnbug图片](https://github.com/neverstoplearn/Note/blob/master/ImgsForiOS/Img_4.png?raw=true)
    [解决方案地址](http://blog.csdn.net/luochao_tj/article/details/46358145)
    1. 显示隐藏文件`com.apple.finder AppleShowAllFiles -bool true`
    2. 本地项目中到.svn文件夹，在此文件下找到**wc.db 数据库文件**
    3. 安装[NavicatPremium](https://www.waitsun.com/navicat-premium-11-2-18.html)
    4. 使用Navicat打开**wc.db 数据库文件**中的**wc_lock表**和**work_queue**表，并且删除这两个表中的所有数据
    
* mac 安装提示文件损坏的问题
    安装之后，提示文件损坏不能打开，大都是因为mac的安装来源设置，设置为`允许所有`
    但是比较奇葩的情况是安装来源设置中没有`允许所有`，使用终端命令`sudo spctl --master-disable`可以显示`允许所有`这一选项设置


