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