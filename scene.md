###工作方法总结
####最大的收获：
1. 团队协作，分工完成，如何达到1+1=甚至>2的效果，时刻保持沟通，信息同步
2. 时间规划，使用notion等软件，规划自己的时间，精确到小时或者将一天划为3等分
3. 遇到问题先Google，但是遇到查询不到的业务问题可以先记录下来，在不阻塞熟悉业务的情况下可以先汇总，后面统一再问同事
具体：比如一个大的项目，我们不只是要关注自身角色所完成的任务，也要兼顾其他角色的完成时间，从而能够合理得安排时间和buffer，才能更好地
   完成项目。还有就是得对业务有充分的了解才开始设计服务框架，数据库表、coding等等。比如要对产品的需求文档进行熟读，，遇到疑惑的地方
   先记在文档里面（最好每个需求都有自己的开发过程的一个文档，可以记录问题&&记录进度&&后期总结），之后拉上PM和QA进行讨论
   这样能够提前解决一些问题，也能提前暴露风险。
开会效率：会前需要充足准备，会中需要专心听其他同学的观点，合理发表自己的观点，会后总结提出的问题和TODO，群里同步
   

#### 两个go协程交替打印出一个数组
>func main() {<br>
  // 创建3个channel,A,B和Exit<br>
  A := make(chan bool)<br>
  B := make(chan bool)<br>
  Exit := make(chan bool)<br>
  go func() {<br>
    // 如果A通道是true,我就执行<br>
    for i := 1; i <= 10; i += 2 {<br>
      if ok := <-A; ok {<br>
        fmt.Println("A 输出", i)<br>
        B <- true<br>
  go func() {<br>
    defer func() { Exit <- true }() // 这个协程的活干完之后，向主goroutine发送信号<br>
    // 如果B通道是true,我就执行<br>
    for i := 2; i <= 10; i += 2 {<br>
      if ok := <-B; ok {<br>
        fmt.Println("B 输出", i)<br>
        if i != 10 { // r如果i等于10了，就不要再向A通道写数据了，否则将导致A通道死锁，至于为什么，坦白说我很疑惑<br>
          A <- true<br>
  A <- true // 启动条件<br>
  <-Exit    // 结束条件<br>
}
####gin中间件
自定义的中间件只能捕获主goroutine的异常，子goroutine还是需要对应的function里面写defer func去捕获
- 中间件代码最后即使没有调用Next()方法，后续中间件及handlers也会执行；
- 如果在中间件函数的非结尾调用Next()方法当前中间件剩余代码会被暂停执行，会先去执行后续中间件及handlers，等这些handlers全部执行完以后程序控制权会回到当前中间件继续执行剩余代码；
- 如果想提前中止当前中间件的执行应该使用return退出而不是Next()方法；
- 如果想中断剩余中间件及handlers应该使用Abort方法，但需要注意当前中间件的剩余代码会继续执行。

gin文档：https://www.topgoer.com/gin%E6%A1%86%E6%9E%B6/gin%E8%B7%AF%E7%94%B1/api%E5%8F%82%E6%95%B0.html  
mysql和redis的数据同步问题：https://www.cnblogs.com/xiaozengzeng/p/10872290.html  
- 读的场景：先去读redis,没有读到的话再去读数据库。更新可以自己设置一个时间间隔（比如几分钟)，然后针对redis中的key值去访问MySQL，不一样的话就更新reids的值
- 增删改的的情况：不经过redis，直接对数据库进行操作，可以设置一些触发器，当执行对应的增删改操作时就刷新redis的值。
