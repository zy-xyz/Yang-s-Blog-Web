# Windows 内存利用率优化

此优化针对大内存用户
首先可以管理员身份打开[PowerShell]，
运行命令`get-mmagent`查看系统预读取状况，True就是开启False就是关闭：
每一项对应的是什么意思呢？

ApplicationLaunchPrefetching程序预加载进内存(内存大就开)

ApplicationPreLaunch程序预运行(CPU速度快就开)

MaxOperationAPIFiles最大程序预加载进内存的文件数(内存越大开得越多，默认256)(这个就是在C:WindowsPrefetch里面的文件数量，如果你内存非常大，比如16G或32G就可以把这个设置大一些，可以设置从1~8192)只需要运行set-mmagent之后输入数字即可

Set-MMAgent -MaxOperationAPIFiles 8192

MemoryCompression内存压缩(CPU速度快就开)

OperationAPI(内存大就开，可以让内存预读取非系统的任何程序)

PageCombining(让系统合并具有相同内容的内存，可以节省内存，CPU速度快就开)

1. 如果想关闭某选项就使用Disable-MMAgent加上项目名称

2. 如果想开启某选项就使用Enable-MMAgent加上项目名称
