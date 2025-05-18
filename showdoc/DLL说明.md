**DLL接口**

DLL有两个`WxWorkLoader.dll`和`WxWorkHelper.dll`

**DLL文件说明：** 

|文件名|说明|
|:----    |:---|
|WxWorkLoader.dll | 管理端，用于多开企业微信和与企业微信交互  |
|WxWorkHelper.dll |客户端，用于注入企业微信内部，接收指令并发送数据给管理端  |

`WxWorkHelper.dll可以以企业微信版本号做为名称，先使用GetUserWxWorkVersion函数获取用户电脑上的企业微信版本，判断是否支持，如果支持，使用InjectWxWork注入dll`


**WxWorkLoader.dll的导出函数:**

1. GetUserWxWorkVersion
	`获取当前用户的电脑上安装的企业微信版本，如： 3.0.0.1001`
	函数原型：
	`BOOL __stdcall GetUserWxWorkVersion(OUT LPSTR szVersion);`
	传一个ANSI字符串缓冲区的指针，长度30即可， 这个函数可以先获取当前用户电脑上安装的微信版本，然后判断我们的dll是否支持，如果不支持就提示用户下载我们支持的版本。

2. UseUtf8
	`在所有接口前执行，执行后接口全部使用utf8编码传输`
	函数原型:
	`BOOL __stdcall UseUtf8();`
	
3. UseRecvJsUnicode
	`在初始化的时候执行，设置后接收消息回调那里，会将中文转换为的形式传递给接口, 推荐js和C#用户使用这个接口`
	函数原型:
	`BOOL __stdcall UseRecvJsUnicode();`
	
4. InitWxWorkSocket
	`用于socket的回调处理`
	函数原型：
	`BOOL __stdcall InitWxWorkSocket(IN DWORD dwConnectCallback, IN DWORD dwRecvCallback, IN DWORD dwCloseCallback)`
	其中dwConnectCallback是一个函数指针类型, 在有新客户端加入时调用，结构如下：
		`void __stdcall MyConnectCallback(int iClientId)` 传入的一个参数是socket的客户ID,返回值为空 
	dwRecvCallback是一个函数指针类型,在接收到新消息时调用，结构如下：
		`void __stdcall MyRecvCallback(int iClientId, char* szJsonData, int iLen)` 
	dwCloseCallback是一个函数指针类型，在客户端退出时调用，结果如下:
		`void __stdcall MyCloseCallback(int iClientId)`
	
5. InjectWxWork
	`用于智能多开，并注入dll, 参数1：WxWorkHelper.dll路径，参数2：WXWork.exe路径，可传空，会读取企业微信安装目录， 注入成功返回企业微信的进程ID, 失败返回0`
	函数原型：
	`DWORD __stdcall InjectWxWork(IN LPCSTR szDllPath，IN LPCSTR szWxWorkExePath);`
	如果需要一个软件，管理多个企业微信，多次调用这个函数实现，通过socket回调管理客户端

6. InjectWxWorkPid
	`注入指定的企业微信进程，参数1： WXWork.exe进程id, 参数2： dll路径`
	函数原型：
	`DWORD __stdcall InjectWxWorkPid(IN DWORD dwPid, IN LPCSTR szDllPath)`
	
7. InjectWxWorkMultiOpen
	`多开一个新的企业微信并注入，不维护已经打开的企业微信，需要两个参数，参数1：WxWorkHelper.dll的路径，参数2：指定要启动企业微信（WxWork.exe）的完整路径，如果不提供，可以设置0或空字符串，将自动读取企业微信的安装目录`
	函数原型：
	`DWORD __stdcall InjectWxWorkMultiOpen(IN LPCSTR szDllPath, IN LPCSTR szWxWorkExePath);`·
	
8. SendWxWorkData
	用于向企业微信发送指令，指令内容参考功能类，·
	函数原型:
	` BOOL __stdcall SendWxWorkData(IN CONNID dwClientId, IN LPCSTR szJsonData);`
	
9. DestroyWxWork
	`主程序退出前，执行释放函数，用于卸载DLL和关闭socket服务端`
	函数原型:
	`BOOL __stdcall DestroyWxWork();`
	
10. SetDataLocationPath 
    `设置要启动企微的主数据目录，用于多账号登录后，下次自动恢复登录`
	函数原型：
	`BOOL __stdcall SetDataLocationPath(LPCSTR szPath);`


### 关于SetDataLocationPath详细说明

在调用InjectWxWork相关函数前， 先通过SetDataLocationPath设置下用户的文档存储目录， 这里分成两种情况，
   1. 首次扫码登录，那就通过SetDataLocationPath指定一个新的空目录 如: D:\\WXWorkData\\WXWork_1, 当用户登录成功
   收到type=11026消息时，取document_root指定的部分与user_id进行关联持久存储
   
   2. 用户恢复登录，先从持久存储中通过user_id取目录，通过SetDataLocationPath设置

