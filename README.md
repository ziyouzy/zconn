# zconn
为设计好ziyouzy/heartbeating,需要先设计出个基于net.Conn所实现结构类的demo，这样我才能有更多的思路和尝试实现功能的途径和平台
***

**2021年2月14日09点31分:**  
调用zadapter库的adapter的map实体时要注意学习go-logger的这段代码：  

    logFun, ok := adapters[adapterName]
	      if !ok {
		        printError("logger: adapter " + adapterName + "is nil!")
	      }
    adapterLog := logFun()
    
先取出预加载功能函数，然后通过()拿到对应适配器的实体对象(结构类所实现的接口包括了所有底层数据的功能需求)
