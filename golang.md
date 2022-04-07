####很好的总结：https://zhuanlan.zhihu.com/p/360306642
- 值类型：基本数据类型 int 系列,float 系列,bool,string 、数组和结构体struct
- 引用类型：指针、slice切片、map、管道chan、interface 等都是引用类型
####make 和 new 的区别？
make 只适用于映射、切片和信道且不返回指针。若要获得明确的指针， 请使用 new 分配内存。
####了解过golang的内存管理吗
https://draveness.me/golang/docs/part3-runtime/ch07-memory/golang-memory-allocator/
####线程有几种模型
- 1. 内核级线程模型(1:1) 
- 2. 用户级线程模型(M:1) 
- 3. 两级线程模型(M:N)
- https://www.golangroadmap.com/class/goadvanced/3-4.html#%E4%B8%80%E3%80%81%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%9E%8B
- GMP 要是M中增加的P没有G，则顺序是 全局P队列（需加锁）、其他P队列拿一半
#### Goroutine有哪几种状态
https://studygolang.com/articles/11861
####线程(M)有几种状态 -> (自旋、非自旋。)
https://golang.design/under-the-hood/zh-cn/part2runtime/ch06sched/mpg/
####每个线程/协程占用多少内存知道吗？
- 线程是有固定的栈的，基本都是2MB，当然，不同系统可能大小不太一样，但是的确都是固定分配的。
- go采用了动态扩张收缩的策略：初始化为4KB，最大可扩张到1GB。
####Goroutines在线程上的优势。
- 与线程相比，Goroutines非常便宜。它们只是堆栈大小的几个kb，堆栈可以根据应用程序的需要增长和收缩，而在线程的情况下，堆栈大小必须指定并且是固定的
- Goroutines被多路复用到较少的OS线程。在一个程序中可能只有一个线程与数千个Goroutines。如果线程中的任何Goroutine都表示等待用户输入，则会创建另一个OS线程，剩下的Goroutines被转移到新的OS线程。所有这些都由运行时进行处理，我们作为程序员从这些复杂的细节中抽象出来，并得到了一个与并发工作相关的干净的API。
- 当使用Goroutines访问共享内存时，通过设计的通道可以防止竞态条件发生。通道可以被认为是Goroutines通信的管道。
- 如果若干个Goroutine，其中有一个panic，会发生什么
- 之前的goroutine正常运行，但是发生panic之后其他goroutine就停止了
####defer可以捕获到其Goroutine的子Goroutine的panic吗
不行
####gin如何进行参数校验
- https://juejin.cn/post/6950269300309491748 可以通过 struct tag或者在init中自定义校验函数路由
####nil != nil: 的情况是
- var a *int = nil
- var b interface{} = nil
- a != b,因为a的type是*int, 而b的type是<nil>
####为什么Go不能实现得更健壮些，多次执行Unlock()也不要panic？
- 仔细想想Unlock的逻辑就可以理解，这实际上很难做到。Unlock过程分为将Locked置为0，然后判断Waiter值，如果值>0，则释放信号量。
- 如果多次Unlock()，那么可能每次都释放一个信号量，这样会唤醒多个协程，多个协程唤醒后会继续在Lock()的逻辑里抢锁，势必会增加Lock()实现的复杂度，也会引起不必要的协程切换。
####MySQL锁机制详解
####MySQL分库分表方案
####git 使用总结：
撤销git add .操作 -> git reset HEAD .
####Redis分布式锁
关于Golang中database/sql包的学习笔记
####结构体标签（struct tag）