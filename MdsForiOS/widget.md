#Widget

iOS测试证书：
* 如果勾选了`Automatically manage signing`, xcode 会自动配置APPid、证书、描述文件，要把`Team`填写上。不要到`Build Setting`中配置证书和描述文件，否则会冲突,`Build Setting`中使用,描述文件使用`Automic`,`sign`使用xcode自己生成的值就好。
![冲突信息描述图片](https://raw.githubusercontent.com/neverstoplearn/Note/master/ImgsForiOS/Img_2.png)
* 如果自己在开发者后台创建APPid、证书、描述文件，那就不要勾选`Automatically manage signing`
![不适用Xcode自动配置](https://raw.githubusercontent.com/neverstoplearn/Note/master/ImgsForiOS/Img_1.png)


HostApp 和 ContainerApp是独立存在的APP，都要配置APPid、证书、描述文件，通过配置`App Group`实现数据共享，因此这两个都要开通`App group`功能。

细节：App,Widget,Frame 支持的最低iOS版本要相同

