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

变量功能是存储用户的数据，常见的数据类型：整型、浮点型、布尔型、结构体等。

**变量声明**

```go
var a int //声明一个整型变量
var b string //声明一个字符串类型的变量
var c []float32 //声明一个32位浮点切片类型的变量
var d func()bool //声明一个返回值为布尔类型的函数变量
var e struct{ //声明一个结构体类型的变量，这个结构体有一个整型的x字段
    x int
}

var 变量名 变量类型 //声明标准格式
var( //批量声明格式
	a int
    b []float32
    c string
)
```



**初始化变量**

Go 语言在声明变量时，自动对变量对应的内存区域进行初始化操作，每个变量初始化其类型的默认值，当然也可以在变量声明时赋一个初始值。

| 变量类型         | 默认值   |
| ---------------- | -------- |
| 整型、浮点型     | 0        |
| 字符串型         | 空字符串 |
| 布尔型           | bool     |
| 切片、函数、指针 | nil      |

```go
var a int = 100 //标准格式
var a = 100 //将类型省略后，编译器会根据值推导变量的类型
a:=100 //声明的精简写法，变量必须是未定义过的变量
```



**变量交换**

```go
//传统方法借助中间变量交换
var a int = 100
var b int = 200
var t int

t=a
a=b
b=t

//避免使用中间变量交换
var a int = 100
var b int = 200
b,a=a,b
```



**匿名变量**

使用多重赋值时，如果不需要在左值中接受变量，可以使用匿名变量`_`；匿名变量不占命名空间，不会分配内存，也不会因为多次声明而无法使用。

```go
func GetData()(int,int){
    return 100,200
}

a,_=Getdata() //接收100
_,b=GetData() //接受200
```

## 2.2 数据类型

除了基本的整型、浮点型、布尔型、字符串外，还有切片、结构体、函数、map、通道等。

**整型**

按长度分为：int8、int16、int32、int64，以及对应的无符号整型 uint8、uint16、uint32、uint64。

其中，uint8 就是 byte，int16 就是 C 语言中的 short，int64 就是 C 语言中的 long。

> > 自动匹配平台的 int 和 uint
>
> 64位平台现已普及，但8位、16位、32位的操作系统依然存在，16位品台上依然可以使用64位的变量，但运行性能和内存性能上较差；同理在64位系统大量使用8位、16位与平台位数不等长的变量时，编译器也是尽量将内存对其以获得最好的性能。
>
> > 哪些情况下使用int和uint
>
> 逻辑对整数范围没有特殊需求，例如对象的长度使用内建`len()`函数返回，这个长度可以根据不同平台的字节长度进行变化，实际使用中，切片或map的元素数量都可以用int表示。
>
> 反之，在二进制传输、读写文件的结构描述时，为了保持文件的结构不会受到不同编译平台字节长度的影响时，不要使用int和uint。



**浮点型**

Go 支持两种浮点数类型：float32 和 float64，这两种浮点型格式均遵循 IEEE 754标准，打印时可以用 fmt 包的`%f`输出

```go
package main

import(
	"fmt"
    "math"
)

func main(){
    fmt.Printf("%f\n",math.Pi)		//按默认宽度和精度输出
    fmt.Printf("%.2f\n",math.Pi)   	//按默认宽度，2位精度输出
}
```

输出：

```go
3.141593
3.14
```



**示例：输出正弦函数图像**

Go语言中，正弦函数由 math 包提供，函数入口为 math.Sin，正弦函数的参数和返回值类型都是 float64。Go语言标准库支持对图片像素进行访问，并输出各种格式的图片。

> 1、设置图片背景色
>
> 灰度图是一种常见的图片格式，一般情况下颜色由8位组成，灰度范围为0~255，0表示黑色，255表示白色。

```go
const size = 300 //图片大小
pic := image.NewGrey(image.Rect(0,0,size,size)) //根据给定大小创建灰度图

for x:=0;x<size;x++{ //遍历每个像素
    for y:=0;y<size;y++{
        pic.SetGrey(x,y,color.Gray{255}) //填充为白色
    }
}
```

> 2、绘制正弦函数轨迹
>
> 正弦函数是一个周期函数，定义域是R，值域范围是[-1,1]，也就是 math.Sin 函数的参数支持任意浮点数范围，返回值的范围总是在-1~1之间
>
> 要将正弦函数放在图片上需要考虑：
>
> - math.Sin 的返回值在-1~1之间，需要考虑将争先的输出幅度变大，可以将math.Sin的返回值乘以一个常量进行放大。
> - 图片的坐标系原点在左上角，而 math.Sin 基于笛卡尔坐标系原点在左下角，需要对图像进行上下翻转和平移

```go
for x:=0;x<size;x++{ //从0到最大像素生成 x 坐标
    s:=float64(x) * 2 * mah.Pi / size //让 sin 值的范围在0~2 Pi 之间
    y:=size/2 - math.Sin(s) * size / 2 //sin的幅度为一半的像素，向下偏移一半像素并翻转
    pic.SetGray(x,int(y),color.Gray{0}) //用黑色绘制 sin 轨迹
}
```

> 3、写入图片文件
>
> 内存中的正弦函数图像是不可见的，选用 png 格式将图形输出为图片

```go
file,err:=os.Create("sin.png") //创建文件

if err!=nil{
    log.Fatal(err)
}

png.Encode(file,pic) //将图片信息写入文件中
file.Close() //关闭文件
```



**布尔型**

Go 语言中以 bool 类型声明布尔类型，只有 true 和 false 两个值，且不允许将整型强制转换为布尔型。布尔型无法参与数值运算，也无法与其他类型进行转换。



**字符串**

字符串为双引号中的内容，可以再 Go 的源码中直接添加非 ASCII 码字符

> 1、字符串转义符
>
> | 转义符 | 含义               |
> | ------ | ------------------ |
> | \r     | 回车符（返回行首） |
> | \n     | 换行符             |
> | \t     | 制表符             |
>
>在 Go 语言源码中使用转义符如下:

```go
package main

import "fmt"

func main(){
    fmt.Println("str:=\"c:\\Go\\bin\\go.exe\"")
}
```

输出：

```go
str:="c:\Go\bin\go.exe"
```

> 2、字符串实现基于 UTF-8编码
>
> Go 里的字符串内部使用 UTF-8编码，通过 rune 类型可以方便的对每个 UTF-8字符进行访问，当然也支持按传统的 ASCII 码进行逐字符访问

> 3、定义多行字符串
>
> 在源码中，定义多行字符串要用到`字符

```go
const str=`第一行
第二行
第三行`
```



**字符**
