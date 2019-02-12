# 以撒期权程序化交易接口
本软件是HDCX(汇点交易客户端)增强系统，需要将必需文件放入HDCX目录!接口依赖汇点交易客户端，客户端需登录才能正常使用下单/撤单/持仓/查询等功能接口。
## 安装使用方法
### 1、将Plugin目录下所有文件放入HDCX目录即可
### 2、修改order_engine.ini，并申请token,填入可用的token
```
token=YYY_xxxxxx
```
其中:xxxxxx即为token，可以申请试用token; YYY为客户标识，同样需要特别开通。
也可以修改HTTP接口绑定本地端口号：
```
http_port=12964
```
端口冲突或者开启多个汇点客户端需要绑定不同的端口号，则修改此配置。

根据需要，可能修改汇点真实EXE的名称，默认是HDCX.EXE,如果是其他的，请在[service]段下添加如下设置：
```
exe=HDCX.exe // 也可能修改成其他如main.exe
```
### 3、启动IsaacLaucher.exe
启动后，HDCX.EXE会被自动调起，不需要用户手动启动HDCX.EXE！
### 4、自行输入用户名密码登录交易

## 接口调用说明
接口全部以本地http服务的方式调用，原则上支持所有语言, 根服务地址：
```
http://localhost:12964
```
12964就是配置的绑定端口号。

### 1、下单接口
```
/placeorder?symbol=10001617&price=0.0001&volume=1&type=B&isopen=o&pricetype=0
```
参数说明：
```
 symbol:合约编号
 price:委托价格
 volume:委托数量，（张）
 type:委托方式，B为买，S为卖
 isopen:是否开仓，o为开仓，c为平仓
 pricetype:可选参数，0 限价委托，o 市价剩余转限GFD, p 市价IOC剩余撤单， r 市价FOK全成或撤
```

返回说明：
返回值为json格式，正确的情况：
```
{
   "encoding" : "gb2312",
   "orderNumber" : 17901,		// 用来追踪委托的整型数值，撤单的时候用到, 当status为ERROR的时候忽略
   "status" : "OK"				     // 状态，正确都是"OK",错误都是"ERROR"
}
```

错误的情况：
```
{
   "encoding" : "gb2312",
   "info" : "",	             // 错误详情
   "status" : "ERROR"		      // 状态，正确都是"OK",错误都是"ERROR"
}
```

### 2、撤单接口
```
/cancelorder?ordernumber=1
```
参数说明：
ordernumber:为下单时返回的整型数值

### 3、委托列表
```
/orderlist
```
返回json格式，如：
```
{
   "encoding" : "gb2312",
   "list" : [										              // 多个委托，所以数组类型
      {
         "dealtPrice" : 0.0,					   	// 成交价格
         "dealtVol" : 0,							      // 成交数量
         "marketAndType" : "SHQQ-A",	// 市场
         "orderDate" : 20190129,					// 下单日期
         "orderId" : "1072000049",			// 委托编号
         "orderNumber" : 17446,						// 委托ID
         "orderPrice" : 0.0001,						// 委托价格
         "orderVol" : 1,							      // 委托数量
         "status" : "已撤",							    // 委托状态，（已撤，已报，未报，已成等）
         "symbol" : "10001675",						// 合约编号
         "symbolName" : "50ETF购2月2600",			// 名称
         "time" : "10:28:37"						   // 下单时间
      }
   ],
   "status" : "OK"
}
```
注意：当有委托时，status为OK，否则都为ERROR.

### 4、持仓查询
```
/positions
```
返回也是json数据格式，如：
```
{
   "encoding" : "gb2312",
   "list" : [								         // 多个持仓，所以数组类型
      {
         "buysell" : "0",					// 头寸方向，"0"多头，”1“空头
         "symbol" : "10001675",	// 合约编号
         "symbolName" : "50ETF购2月2600",	// 名称
         "volume" : 1						   // 所持合约张数
      }
   ],
   "status" : "OK"
}
```
status为ERROR时，list无效。
### 5、获取行情
```
/getquote?market=SHQQ-A&code=10001536
```
返回为json数据格式。
