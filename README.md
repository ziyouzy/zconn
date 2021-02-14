# zconn
为设计好ziyouzy/heartbeating,需要先设计出个基于net.Conn所实现结构类的demo，这样我才能有更多的思路和尝试实现功能的途径和平台
***

**2021年2月14日09点53分:**
设计zconn的内部字段时有种感受，zconn本身其实是个数据源，或者说，适配器+上游数据=数据源  
也就是说，在zadapter包的演示举例中最初的：

    go func(){
        defer close(testBytesSenderCh)
        for i := 1;i < 20;i++{
            testBytesSenderCh <- []byte{0x01, 0x02, 0x03,}
            if i < 10{
                time.Sleep(time.Second)
            }else{
                time.Sleep(60*time.Second)
                i = 1
                fmt.Println("ReStart")
            }
        }
    }()
    
这个环节本身在逻辑上也需要把他设计成一个数据源，或者说适配器+上游数据的形式  
或者说根本就不该存在testBytesSenderCh <- []byte{0x01, 0x02, 0x03,}这句  
不该存在testBytesSenderCh这个管道，而是直接与下一个节点融合：  

    go func(){
        //defer close(testBytesSenderCh)
	defer close(mainTestHbRawinCh)
        defer close(mainTestStampsRawinCh)
        for i := 1;i < 20;i++{
	    //testBytesSenderCh <- []byte{0x01, 0x02, 0x03,}
            bytes := []byte{0x01, 0x02, 0x03,}  /*融合*/
	    mainTestHbRawinCh <- struct{}{}     /*融合*/
            mainTestStampsRawinCh <- bytes      /*融合*/
            if i < 10{
                time.Sleep(time.Second)
            }else{
                time.Sleep(60*time.Second)
                i = 1
                fmt.Println("ReStart")
            }
        }
    }()

还是以前的写法好，这么写体现不出数据源的独立性，也感受不到数据可能会中断的
**2021年2月14日09点31分:**  
调用zadapter库的adapter的map实体时要注意学习go-logger的这段代码：  

    logFun, ok := adapters[adapterName]
	      if !ok {
		        printError("logger: adapter " + adapterName + "is nil!")
	      }
    adapterLog := logFun()
    
先取出预加载功能函数，然后通过()拿到对应适配器的实体对象(结构类所实现的接口包括了所有底层数据的功能需求)
