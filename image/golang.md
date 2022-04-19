###go包管理
一般来说go支持的是绝对路径导入，配合go mod使用
一般是项目根目录下go mod init / go mod tidy
然后按照项目根目录作为根目录进行搜索自定义的包，例如import "lottry/test/thrift/gen-go/demo"

###Context
>type key string  
now := time.Now()  
later,_:=time.ParseDuration("10s")  
withValue需要注意子ctx在类型key相同时会覆盖父ctx的value,  
例如  
ctx1 := context.WithValue(context.Background(),"key"",1)  
ctx2 := ctx1  
ctx2 = context.WithValue(context.Background(),"key"",2)  
ctx1.Value("key")和ctx2.Value("key")都是2 
解除方法是设置成不同的自定义类型
- context的父节点cancel()后，所有的子context都会被cancel()  
- ctx := context.WithValue(context.Background(),key("asong"),"Golang梦工厂") --- ctx.Value(k).(string);
- ctx,cancel := context.WithCancel(context.Background()) --- 调用cancel()时，select里面的case <- ctx.Done():会被执行
- ctx,cancel := context.WithDeadline(context.Background(),now.Add(later)) --- 超时自动取消，或者调用cancel()也会取消，select里面的case <- ctx.Done():会被执行
- ctx,cancel := context.WithTimeout(context.Background(),10 * time.Second)
###new 和 make 的区别
首先我们得知道，Go分为数据类型分为值类型和引用类型，其中值类型是 int、float、string、bool、struct和array，它们直接存储值，分配栈的内存空间，它们被函数调用完之后会释放
引用类型是 slice、map、chan和值类型对应的指针 它们存储是一个地址（或者理解为指针）,指针指向内存中真正存储数据的首地址，内存通常在堆分配，通过GC回收

####区别
- new 的参数要求传入一个类型，而不是一个值，它会申请该类型的内存大小空间，并初始化为对应的零值，返回该指向类型空间的一个指针
- make 也用于内存分配，但它只用于引用对象 slice、map、channel的内存创建，返回的类型是类型本身

####值传递和指针传递有什么区别
- 值传递：会创建一个新的副本并将其传递给所调用函数或方法  
- 指针传递：将创建相同内存地址的新副本  
- 需要改变传入参数本身的时候用指针传递，否则值传递
- 另外，如果函数内部返回指针，会发生内存逃逸

golang内存逃逸&&分析
####什么是内存逃逸
在程序中，每个函数块都会有自己的内存区域用来存自己的局部变量（内存占用少）、返回地址、返回值之类的数据，这一块内存区域有特定的结构和寻址方式，寻址起来十分迅速，开销很少。这一块内存地址称为栈。栈是线程级别的，大小在创建的时候已经确定，当变量太大的时候，会"逃逸"到堆上，这种现象称为内存逃逸。简单来说，局部变量通过堆分配和回收，就叫内存逃逸。
####内存逃逸的危害
堆是一块没有特定结构，也没有固定大小的内存区域，可以根据需要进行调整。全局变量，内存占用较大的局部变量，函数调用结束后不能立刻回收的局部变量都会存在堆里面。变量在堆上的分配和回收都比在栈上开销大的多。对于 go 这种带 GC 的语言来说，会增加 gc 压力，同时也容易造成内存碎片。
####如何分析程序是否发生内存逃逸
build时添加-gcflags=-m 选项可分析内存逃逸情况,比如输出./main.go:3:6: moved to heap: x 表示局部变量x逃逸到了堆上。
####内存逃逸发生时机
内存逃逸是发生在行为上，也就是说golang不知道你在函数外还会不会用到这个值，这时候这个值就会被放到堆上，也就是内存逃逸
- 向 channel 发送指针数据。因为在编译时，不知道channel中的数据会被哪个 goroutine 接收，因此编译器没法知道变量什么时候才会被释放，因此只能放入堆中。
- 局部变量在函数调用结束后还被其他地方使用，比如函数返回局部变量指针或闭包中引用包外的值。因为变量的生命周期可能会超过函数周期，因此只能放入堆中。
- 在 slice 或 map 中存储指针。比如 []*string，其后面的数组可能是在栈上分配的，但其引用的值还是在堆上。（原因是如果出现s[:1]的截断情况后面的指针指向的内容就可能找不到对应的值了，假设在函数结束后，slice里面的值没有及时被GC）
- 切片扩容后长度太大，导致栈空间不足，逃逸到堆上。
- 在 interface 类型上调用方法。 在 interface 类型上调用方法时会把interface变量使用堆分配， 因为方法的真正实现只能在运行时知道。
####避免内存逃逸的办法
- 对于小型的数据，使用传值而不是传指针，避免内存逃逸。
- 避免使用长度不固定的slice切片，在编译期无法确定切片长度，只能将切片使用堆分配。
- interface调用方法会发生内存逃逸，在热点代码片段，谨慎使用。  

// 下面的temp会被分配到栈中（假设栈的容量可以分配下）  
`func F() {
	temp := make([]int, 0, 20)
	...
}`  
// 下面的temp会被分配到堆中  
`func F() []int{
	a := make([]int, 0, 20)
	return a
}`
###自己总结一下
`不能盲目相信网上博客的说法`  
`那现在就可以理解为是否会发生逃逸取决于你当前函数在return的时候：`  
`1. 局部变量能不能确定是否不会再被用到`  
`2. 是否分配的内存过大导致突破栈的最大容量` 



了解过golang的内存管理吗
>/>32KB 的对象，直接从mheap上分配；  
<=16B 的对象使用mcache的tiny分配器分配；  
(16B,32KB] 的对象，首先计算对象的规格大小，然后使用mcache中相应规格大小的mspan分配；  
如果mcache没有相应规格大小的mspan，则向mcentral申请  
如果mcentral没有相应规格大小的mspan，则向mheap申请  
如果mheap中也没有合适大小的mspan，则向操作系统申请  

#####总结
- Go在程序启动时，会向操作系统申请一大块内存，之后自行管理。
- Go内存管理的基本单元是mspan，它由若干个页组成，每种mspan可以分配特定大小的object。
- mcache, mcentral, mheap是Go内存管理的三大组件，层层递进。mcache管理线程在本地缓存的mspan；mcentral管理全局的mspan供所有线程使用；mheap管理Go的所有动态分配内存。
- 极小对象会分配在一个object中，以节省资源，使用tiny分配器分配内存；一般小对象通过mspan分配内存；大对象则直接由mheap分配内存。









###线程有几种模型？Goroutine 的原理你了解过吗，将一下实现和原理
1. 内核级线程模型(1:1) 
2. 用户级线程模型(M:1) 
3. 两级线程模型(M:N)  

Linux历史上线程的3种实现模型： 线程的实现曾有3种模型：
多对一(M:1)的用户级线程模型
一对一(1:1)的内核级线程模型
多对多(M:N)的两级线程模型

###goroutine的原理
基于CSP并发模型开发了GMP调度器，
- 其中 * G（Goroutine） : 每个 Goroutine 对应一个 G 结构体，G 存储 Goroutine 的运行堆栈、状态以及任务函数
- M（Machine）: 对OS内核级线程的封装，数量对应真实的CPU数(真正干活的对象).
- P (Processor): 逻辑处理器,即为G和M的调度对象，用来调度G和M之间的关联关系，其数量可通过 GOMAXPROCS()来设置，默认为核心数。
- 在单核情况下，所有Goroutine运行在同一个线程（M0）中，每一个线程维护一个上下文（P），任何时刻，一个上下文中只有一个Goroutine，其他Goroutine在runqueue中等待。
- 一个Goroutine运行完自己的时间片后，让出上下文，自己回到runqueue中（如下图所示）。
- 当正在运行的G0阻塞的时候（可以需要IO），会再创建一个线程（M1），P转到新的线程中去运行。
- 当M0返回时，它会尝试从其他线程中“偷”一个上下文过来，如果没有偷到，会把Goroutine放到Global runqueue中去，然后把自己放入线程缓存中。 上下文会定时检查Global runqueue。
- GMP 要是M中增加的P没有G，则顺序是 全局P队列（需加锁）、其他P队列拿一半


####goroutine的优势
上下文切换代价小：从GMP调度器可以看出，避免了用户态和内核态线程切换，所以上下文切换代价小
内存占用少：线程栈空间通常是 2M，Goroutine 栈空间最小 2K；
goroutine 什么时候发生阻塞
channel 在等待网络请求或者数据操作的IO返回的时候会发生阻塞
发生一次系统调用等待返回结果的时候
goroutine进行sleep操作的时候
在GPM调度模型，goroutine 有哪几种状态？线程呢？
有9种状态

- _Gidle：刚刚被分配并且还没有被初始化
- _Grunnable：没有执行代码，没有栈的所有权，存储在运行队列中
- _Grunning：可以执行代码，拥有栈的所有权，被赋予了内核线程 M 和处理器 P
- _Gsyscall：正在执行系统调用，拥有栈的所有权，没有执行用户代码，被赋予了内核线程 M 但是不在运行队列上
- _Gwaiting：由于运行时而被阻塞，没有执行用户代码并且不在运行队列上，但是可能存在于 Channel 的等待队列上
- _Gdead：没有被使用，没有执行代码，可能有分配的栈
- _Gcopystack：栈正在被拷贝，没有执行代码，不在运行队列上
- _Gpreempted：由于抢占而被阻塞，没有执行用户代码并且不在运行队列上，等待唤醒
- _Gscan：GC 正在扫描栈空间，没有执行代码，可以与其他状态同时存在
- 上述状态中比较常见是 _Grunnable、_Grunning、_Gsyscall、_Gwaiting 和 _Gpreempted 五个状态
####线程(M)有几种状态 -> (自旋、非自旋。)
目前的 Go 的调度器实现中设计了工作线程的自旋（spinning）状态：
如果一个工作线程的本地队列、全局运行队列或网络轮询器中均没有可调度的任务，则该线程成为自旋线程；
满足该条件、被复始的线程也被称为自旋线程，对于这种线程，运行时不做任何事情。
自旋线程在进行暂止之前，会尝试从任务队列中寻找任务。当发现任务时，则会切换成非自旋状态， 开始执行 Goroutine。而找到不到任务时，则进行暂止。

当一个 Goroutine 准备就绪时，会首先检查自旋线程的数量，而不是去复始一个新的线程。

如果最后一个自旋线程发现工作并且停止自旋时，则复始一个新的自旋线程。 这个方法消除了不合理的线程复始峰值，且同时保证最终的最大 CPU 并行度利用率。

总的来说，调度器的方式可以概括为： 如果存在一个空闲的 P 并且没有自旋状态的工作线程 M，则当就绪一个 G 时，就复始一个额外的线程 M。 这个方法消除了不合理的线程复始峰值，且同时保证最终的最大 CPU 并行度利用率。

######线程和协程内存多少
线程一般是2M，协程一般是2K

######如果 goroutine 一直占用资源怎么办，GMP模型怎么解决这个问题
如果有一个goroutine一直占用资源的话，GMP模型会从正常模式转为饥饿模式，通过信号协作强制处理在最前的 goroutine 去分配使用

######如果若干个线程发生OOM，会发生什么？Goroutine中内存泄漏的发现与排查？项目出现过OOM吗，怎么解决

如果线程发生OOM，也就是内存溢出，发生OOM的线程会被kill掉，其它线程不受影响。

######Goroutine中内存泄漏的发现与排查
go中的内存泄漏一般都是goroutine泄露，就是goroutine没有被关闭，或者没有添加超时控制，让goroutine一只处于阻塞状态，不能被GC。

######在Go中内存泄露分为暂时性内存泄露和永久性内存泄露

暂时性内存泄露

- 获取长字符串中的一段导致长字符串未释放
- 获取长slice中的一段导致长slice未释放
- 在长slice新建slice导致泄漏
- string相比切片少了一个容量的cap字段，可以把string当成一个只读的切片类型。获取长string或者切片中的一段内容，由于新生成的对象和老的string或者切片共用一个内存空间，会导致老的string和切片资源暂时得不到释放，造成短暂的内存泄漏

永久性内存泄露

- goroutine永久阻塞而导致泄漏
- time.Ticker未关闭导致泄漏
- 不正确使用Finalizer导致泄漏
- 使用pprof排查
######Go的垃圾回收算法
###Go 1.5 后，采取的是并发标记和并发清除，三色标记的算法

######Go 中的 gc 基本上是标记清除的过程：
Go 的垃圾回收是基于标记清除算法，这种算法需要进行 STW （stop the world），这个过程就是会导致程序是卡顿的，频繁的 GC 会严重影响程序性能
Go 在此基础上进行了改进，通过三色标记清除扫法与写屏障来减少 STW 的时间
GC 的过程一共分为四个阶段：

栈扫描（STW），所有对象开始都是白色
- 从 root 开始找到所有可达对象（所有可以找到的对象），标记灰色，放入待处理队列
- 遍历灰色对象队列，将其引用对象标记为灰色放入待处理队列，自身标记为黑色
- 清除（并发）循环步骤3 直到灰色队列为空为止，此时所有引用对象都被标记为黑色，所有不可达的对象依然为白色，白色的就是需要进行回收的对象。三色标记法相对于普通标记清除，减少了 STW 时间。这主要得益于标记过程是 “on-the-fly”的，在标记过程中是不需要 STW的，它与程序是并发执行的，这就大大缩短了 STW 的时间。
Go GC 优化的核心就是尽量使得 STW(Stop The World) 的时间越来越短。

####写屏障:
当标记和程序是并发执行的，这就会造成一个问题. 在标记过程中，有新的引用产生，可能会导致误清扫.  
清扫开始前，标记为黑色的对象引用了一个新申请的对象，它肯定是白色的，而黑色对象不会被再次扫描，那么这个白色对象无法被扫描变成灰色、黑色，它就会最终被清扫，而实际它不应该被清扫.
这就需要用到屏障技术，golang采用了写屏障，其作用就是为了避免这类误清扫问题. 写屏障即在内存写操作前，维护一个约束，从而确保清扫开始前，黑色的对象不能引用白色对象.  

- 条件 1: 赋值器修改对象图，导致某一黑色对象引用白色对象；（通俗的说就是A突然持有了B的指针，而B在并发标记的过程中已经被判定为白色对象要被清理掉的）
- 条件 2: 从灰色对象出发，到达白色对象且未经访问过的路径被赋值器破坏；（通俗的说就是A持有B的指针，这个持有关系被释放）
#####只要能够避免其中任何一个条件，则不会出现对象丢失的情况，因为：
- 如果条件 1被避免，则所有白色对象均被灰色对象引用，没有白色对象会被遗漏；
- 如果条件 2 被避免，即便白色对象的指针被写入到黑色对象中，但从灰色对象出发，总存在一条没有访问过的路径，从而找到到达白色对象的路径，白色对象最终不会被遗漏。  
可能的解决方法： 整个过程STW，浪费资源，且对用户程序影响较大，由此引入了屏障机制；
  
破坏条件1 => Dijistra写屏障  （黑色节点的引用变灰色）
- 满足强三色不变性：黑色节点不允许引用白色节点
- 当黑色节点新增了白色节点的引用时，将对应的白色节点改为灰色  
破坏条件2 => Yuasa写屏障（白色节点被删除引用后变灰色）
- 满足弱三色不变性：黑色节点允许引用白色节点，但是该白色节点有其他灰色节点间接的引用(确保不会被遗漏)
- 当白色节点被删除了一个引用时，悲观地认为它一定会被一个黑色节点新增引用，所以将它置为灰色  

######每个线程/协程占用多少内存知道吗？
- 线程是有固定的栈的，基本都是2MB，当然，不同系统可能大小不太一样，但是的确都是固定分配的。
- 协程 go采用了动态扩张收缩的策略：初始化为4KB，最大可扩张到1GB。

######Goroutines在线程上的优势。
- 与线程相比，Goroutines非常便宜。它们只是堆栈大小的几个kb，堆栈可以根据应用程序的需要增长和收缩，而在线程的情况下，堆栈大小必须指定并且是固定的
- Goroutines被多路复用到较少的OS线程。在一个程序中可能只有一个线程与数千个Goroutines。如果线程中的任何Goroutine都表示等待用户输入，则会创建另一个OS线程，剩下的Goroutines被转移到新的OS线程。所有这些都由运行时进行处理，我们作为程序员从这些复杂的细节中抽象出来，并得到了一个与并发工作相关的干净的API。
- 当使用Goroutines访问共享内存时，通过设计的通道可以防止竞态条件发生。通道可以被认为是Goroutines通信的管道。

如果若干个Goroutine，其中有一个panic，会发生什么
之前的goroutine正常运行，但是发生panic之后其他goroutine就停止了

defer不可以捕获到其Goroutine的子Goroutine的panic

gin如何进行参数校验
https://juejin.cn/post/6950269300309491748 可以通过 struct tag或者在init中自定义校验函数路由

nil != nil: 的情况是  
var a *int = nil  
var b interface{} = nil  
a != b,因为a的type是*int, 而b的type是<nil>  

为什么Go不能实现得更健壮些，多次执行Unlock()也不要panic？  
仔细想想Unlock的逻辑就可以理解，这实际上很难做到。Unlock过程分为将Locked置为0，然后判断Waiter值，如果值>0，则释放信号量。  
如果多次Unlock()，那么可能每次都释放一个信号量，这样会唤醒多个协程，多个协程唤醒后会继续在Lock()的逻辑里抢锁，势必会增加Lock()实现的复杂度，也会引起不必要的协程切换。



Go数据竞争怎么解决
Data Race 问题可以使用互斥锁解决，或者也可以通过CAS无锁并发解决

中使用同步访问共享数据或者CAS无锁并发是处理数据竞争的一种有效的方法.

golang在1.1之后引入了竞争检测机制，可以使用 go run -race 或者 go build -race来进行静态检测。

其在内部的实现是,开启多个协程执行同一个命令， 并且记录下每个变量的状态.

竞争检测器基于C/C++的ThreadSanitizer运行时库，该库在Google内部代码基地和Chromium找到许多错误。这个技术在2012年九月集成到Go中，从那时开始，它已经在标准库中检测到42个竞争条件。现在，它已经是我们持续构建过程的一部分，当竞争条件出现时，它会继续捕捉到这些错误。

竞争检测器已经完全集成到Go工具链中，仅仅添加-race标志到命令行就使用了检测器。

$ go test -race mypkg    // 测试包
$ go run -race mysrc.go  // 编译和运行程序
$ go build -race mycmd  // 构建程序
$ go install -race mypkg // 安装程序
要想解决数据竞争的问题可以使用互斥锁sync.Mutex,解决数据竞争(Data race),也可以使用管道解决,使用管道的效率要比互斥锁高.

Go:反射之用字符串函数名调用函数
package main

import (
    "fmt"
    "reflect"
)

type Animal struct {
}

func (m *Animal) Eat() {
    fmt.Println("Eat")
}
func main() {
    animal := Animal{}
    value := reflect.ValueOf(&animal)
    f := value.MethodByName("Eat") //通过反射获取它对应的函数，然后通过call来调用
    f.Call([]reflect.Value{})
}
开发用过gin框架吗？参数检验怎么做的？中间使怎么使用的
gin框架使用http://github.com/go-playground/validator进行参数校验 在 struct 结构体添加 binding tag，然后调用 ShouldBing 方法，下面是一个示例

type SignUpParam struct {
    Age        uint8  `json:"age" binding:"gte=1,lte=130"`
    Name       string `json:"name" binding:"required"`
    Email      string `json:"email" binding:"required,email"`
    Password   string `json:"password" binding:"required"`
    RePassword string `json:"re_password" binding:"required,eqfield=Password"`
}

func main() {
    r := gin.Default()
    r.POST("/signup", func(c *gin.Context) {
        var u SignUpParam
        if err := c.ShouldBind(&u); err != nil {
            c.JSON(http.StatusOK, gin.H{
                "msg": err.Error(),
            })
            return
        }
        // 保存入库等业务逻辑代码...
        c.JSON(http.StatusOK, "success")
    })
    _ = r.Run(":8999")
}
中间件使用use方法，Gin的中间件其实就是一个HandlerFunc,那么只要我们自己实现一个HandlerFunc，下面是一个示例

func costTime() gin.HandlerFunc {
    return func(c *gin.Context) {
        //请求前获取当前时间
        nowTime := time.Now()

        //请求处理
        c.Next()

        //处理后获取消耗时间
        costTime := time.Since(nowTime)
        url := c.Request.URL.String()
        fmt.Printf("the request URL %s cost %v\n", url, costTime)
    }
}
以上我们就实现了一个Gin中间件，比较简单，而且有注释加以说明，这里要注意的是c.Next方法，这个是执行后续中间件请求处理的意思（含没有执行的中间件和我们定义的GET方法处理），这样我们才能获取执行的耗时。也就是在c.Next方法前后分别记录时间，就可以得出耗时。

goroutine的锁机制了解过吗？Mutex有哪几种模式？Mutex 锁底层如何实现
互斥锁的加锁是靠 sync.Mutex.Lock 方法完成的, 当锁的状态是 0 时，将 mutexLocked 位置成 1：

// Lock locks m.
// If the lock is already in use, the calling goroutine
// blocks until the mutex is available.
func (m *Mutex) Lock() {
    // Fast path: grab unlocked mutex.
    if atomic.CompareAndSwapInt32(&m.state, 0, mutexLocked) {
        if race.Enabled {
            race.Acquire(unsafe.Pointer(m))
        }
        return
    }
    // Slow path (outlined so that the fast path can be inlined)
    m.lockSlow()
}
Mutex：正常模式和饥饿模式
在正常模式下，锁的等待者会按照先进先出的顺序获取锁。
但是刚被唤起的 Goroutine 与新创建的 Goroutine 竞争时，大概率会获取不到锁，为了减少这种情况的出现，一旦 Goroutine 超过 1ms 没有获取到锁，它就会将当前互斥锁切换饥饿模式，防止部分 Goroutine 被饿死。
饥饿模式是在 Go 语言 1.9 版本引入的优化的，引入的目的是保证互斥锁的公平性（Fairness）。
在饥饿模式中，互斥锁会直接交给等待队列最前面的 Goroutine。新的 Goroutine 在该状态下不能获取锁、也不会进入自旋状态，它们只会在队列的末尾等待。
如果一个 Goroutine 获得了互斥锁并且它在队列的末尾或者它等待的时间少于 1ms，那么当前的互斥锁就会被切换回正常模式。
相比于饥饿模式，正常模式下的互斥锁能够提供更好地性能，饥饿模式的能避免 Goroutine 由于陷入等待无法获取锁而造成的高尾延时。
#####go的调度阻塞有哪些方式
阻塞：https://qiankunli.github.io/2020/11/21/goroutine_system_call.html
在 Go 里面阻塞主要分为以下 4 种场景：
- 由于原子、互斥量或通道操作调用导致 Goroutine 阻塞，调度器将把当前阻塞的 Goroutine 切换出去，重新调度 LRQ 上的其他 Goroutine；
- 由于网络请求和 IO 操作导致 Goroutine 阻塞。Go 程序提供了网络轮询器（NetPoller）来处理网络请求和 IO 操作的问题，其后台通过 kqueue（MacOS），epoll（Linux）或 iocp（Windows）来实现 IO 多路复用。通过使用 NetPoller 进行网络系统调用，调度器可以防止 Goroutine 在进行这些系统调用时阻塞 M。这可以让 M 执行 P 的 LRQ 中其他的 Goroutines，而不需要创建新的 M。执行网络系统调用不需要额外的 M，网络轮询器使用系统线程，它时刻处理一个有效的事件循环，有助于减少操作系统上的调度负载。用户层眼中看到的 Goroutine 中的“block socket”，实现了 goroutine-per-connection 简单的网络编程模式。实际上是通过 Go runtime 中的 netpoller 通过 Non-block socket + I/O 多路复用机制“模拟”出来的。
- 当调用一些系统方法的时候（如文件 I/O），如果系统方法调用的时候发生阻塞，这种情况下，网络轮询器（NetPoller）无法使用，而进行系统调用的 G1 将阻塞当前 M1。调度器引入 其它M 来服务 M1 的P。
- 如果在 Goroutine 去执行一个 sleep 操作，导致 M 被阻塞了。Go 程序后台有一个监控线程 sysmon，它监控那些长时间运行的 G 任务然后设置可以强占的标识符，别的 Goroutine 就可以抢先进来执行。