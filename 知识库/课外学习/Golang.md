## 1.1 Go语言特性
Go 语言是 Google 公司开发的一种静态型、编译型、自带垃圾回收、并发的编程语言。

**特点1：上手简单**

> 例1：HTTP 文件服务器
>
> ```go
> package main //main包为Go程序的入口
> 
> import "net/http" //导入net/http包，这个包负责HTTP的基础封装和访问
> 
> func main(){ //程序入口main()
>     http.Handle("/",http.FileServer(http.Dir("."))) //使用http.FileServer文件服务器，将当前目录作为根目录（"/"）的处理器，访问根目录就会进入当前目录
>     http.ListenAndServer(":8080",nil) 侦听本机8080端口
> }
> ```
>
> 保存源码为 main.go，在命令行中输入`go run main.go`，访问`http://127.0.0.1/`即可访问



**特点2：编译输出可执行文件**

Go 语言不使用虚拟机，直接输出目标平台的原生可执行文件；Go 语言用自己的链接器，不依赖其他编译器、链接器，集合可以直接运行在所有的操作系统上。在 Go 1.5以后实现自举，用 Go 语言编写 Go 编译器及所有工具链。

> 例2：输出 hello world
>
> ```go
> package main
> 
> import "fmt"
> 
> func main(){
>     fmt.Println("hello world")
> }
> ```
>
> 将源码保存为 hello.go，在命令行中运行`go build hello.go`，得到可以输出 hello world 的可执行文件



**特点3：工程结构简单**

Go 语言源码无需头文件，无需解决方案、工程文件和 Make File 



**特点4：编译速度快**

Go 语言可以并发编译，从 Go 1.9开始，最小并发编译元素为函数。

Go 语言语法简单，不允许包的交叉依赖，加速了编译的过程。



**特点5：高性能**

**特点6：原生支持并发**

Go 语言特性就是从语言层原生支持并发，无需第三方库。Go 语言并发基于 goroutine，可以看成虚拟线程；Go 语言运行时参与调度 goroutine，并将 goroutine 合理地分配到每个 CPU 中，最大限度发挥 CPU 性能。

在多个 goroutine 中，Go 语言使用通道 channel 通信，程序可以将需要并发的程序设计为生产者和消费者的模式，将数据送入通道。

> 例3：下面代码的生产者每秒生成一个字符串，通过通道传给消费者，生产者使用两个 goroutine 并发运行，消费者在 main() 函数的 goroutine中进行处理
>
> ```go
> package main
> 
> import(
> 	"fmt"
>     "math/rand"
>     "time"
> )
> 
> func producer(header string,channel chan<-string){ //数据生产者
>     for{ //无限循环
>         channel<-fmt.Sprintf("%s:%v",header,rand.Int31()) //将随机数和字符串格式化为字符串发送给通道
>         time.Sleep(time.Second) //等待1秒
>     }
> }
> 
> func customer(channel<-chan string){ //数据消费者
>     for{
>         message:=<-channel //从通道中取出数据，此处会阻塞直到信道中返回数据
>         fmt.Println(message) //打印数据
>     }
> }
> 
> func main(){ 
>     channel:=make(chan string) //创建一个字符串类型的通道
>     go producer("cat",channel) //创建producer函数的并发goroutine
>     go producer("dog",channels)
>     customer(channel) //数据消费函数
> }
> ```
>
> 代码输出如下：
>
> ```go
> dog:2019727887
> cat:1298763731
> dog:908778668
> cat:23487859
> ...
> ```
>
> 



**特性7：性能分析**

使用 Go 语言工具链可以直接进行性能分析，Go 的性能分析工具将数据以二进制文件输出，配合 Graphviz 可以将数据以图形化的方式展现出来，根据图示开发人员可迅速定位问题代码位置。



**特性8：强大的标准库** 

Go语言标准库涵盖网络、系统、加密、编码、图形各个方面，在标准库上可以完成几乎大部分的需求



**特性9：代码风格清晰简单**

## 1.2 使用Go语言的项目

**Docker**

操作系统层面的虚拟化技术，可以在操作系统和应用程序之间进行隔离，也可以称之为容器。



**Golang**

Go 语言从1.5版本后使用自身编写



**Kubernetes**

Google 公司开发的构建于 Docker 之上的容器调度服务



**Codis**

国产的优秀分布式 Redis 方案

## 1.3 安装Go语言

**Windows版**

从 Github 页面直接下载安装包，安装过程中会自动设置 GOPATH



**Linux版**

使用包管理器安装，例如`apt install golang`

或者下载 Linux 版的压缩包（以`.tar.gz`结尾），将这个压缩包解压到`/usr/local/go`下（`tar -C /usr/local -xzf go1.9.1.linux-amd64.tar.gz`）

然后将`/usr/local/go/bin`目录添加到PATH环境变量中（`export PATH=$PATH:/usr/local/go/bin`）

最后使用`go env`指令，查看是否安装成功

## 2.1 变量

