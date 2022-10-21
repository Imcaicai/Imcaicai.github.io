# Go-net标准库应用


在go语言标准库中，net包提供了可移植的网络I/O接口，包括TCP/IP、UDP、域名解析和Unix域socket。

## 1 服务端

### 1. 解析地址

在TCP服务端我们需要监听一个TCP地址，因此建立服务端前我们需要生成一个正确的TCP地址，这就需要用到 `Resolve` 函数。

```go
// ResolveTCPAddr函数会输出一个TCP连接地址和一个错误信息
func ResolveTCPAddr(network, address string) (*TCPAddr, error)
// 解析IP地址
func ResolveIPAddr(net, addr string) (*IPAddr, error)
// 解析UDP地址
func ResolveUDPAddr(net, addr string) (*UDPAddr, error)
// 解析Unix地址
func ResolveUnixAddr(net, addr string) (*UnixAddr, error)
```

### 2. 监听请求

我们可以通过 `Listen` 方法监听我们解析后的网络地址。

```go
// 监听net类型，地址为laddr的地址
func Listen(net, laddr string) (Listener, error)
// 监听TCP地址
func ListenTCP(network string, laddr *TCPAddr) (*TCPListener, error) 
// 监听IP地址
func ListenIP(netProto string, laddr *IPAddr) (*IPConn, error)
// 监听UDP地址
func ListenMulticastUDP(net string, ifi *Interface, gaddr *UDPAddr) (*UDPConn, error)
func ListenUDP(net string, laddr *UDPAddr) (*UDPConn, error)
// 监听Unix地址
func ListenUnixgram(net string, laddr *UnixAddr) (*UnixConn, error)
func ListenUnix(net string, laddr *UnixAddr) (*UnixListener, error)
```

### 3. 接受请求

TCPAddr 实现了两个接受请求的 `Accept` 方法，两者代码实现其实是一样的，唯一的区别是第一种返回了一个对象，第二种返回了一个接口。

```go
func (l *TCPListener) AcceptTCP() (*TCPConn, error)
func (l *TCPListener) Accept() (Conn, error) 
```

### 4. 连接配置

```go
// 配置监听器超时时间：超过t之后监听器自动关闭，0表示不设置超时时间
func (l *TCPListener) SetDeadline(t time.Time) error
// 关闭监听器
func (l *TCPListener) Close() error
```

### 5. 编写一个服务器

```go
func main() {
	// 解析服务端监听地址，本例以tcp为例
	addr, err := net.ResolveTCPAddr("tcp", "127.0.0.1:8000")
	if err != nil {
		log.Panic(err)
	}
	// 创建监听器
	listen, err := net.ListenTCP("tcp", addr)
	if err != nil {
		log.Panic(err)
	}
	for {
		// 监听客户端连接请求
		conn, err := listen.AcceptTCP()
		if err != nil {
			continue
		}
		// 处理客户端请求 这个函数可以自己编写
		go HandleConnectionForServer(conn)
	}
}
```

## 2 客户端

### 1. 解析地址

在TCP服务端我们需要监听一个TCP地址，因此建立服务端前我们需要生成一个正确的TCP地址，这就需要用到 `Resolve` 函数了。

```go 
// ResolveTCPAddr函数会输出一个TCP连接地址和一个错误信息
func ResolveTCPAddr(network, address string) (*TCPAddr, error)
```

### 2. 发送连接请求

```go
// DialIP的作用类似于IP网络的拨号
func DialIP(network string, laddr, raddr *IPAddr) (*IPConn, error)
// Dial 连接到指定网络上的地址，涵盖
func Dial(network, address string) (Conn, error)
// 这个方法只是在Dial上面设置了超时时间
func DialTimeout(network, address string, timeout time.Duration) (Conn, error)
// DialTCP 专门用来进行TCP通信的
func DialTCP(network string, laddr, raddr *TCPAddr) (*TCPConn, error)
// DialUDP 专门用来进行UDP通信的
func DialUDP(network string, laddr, raddr *UDPAddr) (*UDPConn, error)
// DialUnix 专门用来进行 Unix 通信
func DialUnix(network string, laddr, raddr *UnixAddr) (*UnixConn, error)
```

### 3. 编写一个客户端

```go
func main() {
	// 解析服务端地址
	RemoteAddr, err := net.ResolveTCPAddr("tcp", "127.0.0.1:8000")
	if err != nil {
		panic(err)
	}
	// 解析本地连接地址
	LocalAddr, err := net.ResolveTCPAddr("tcp", "127.0.0.1")
	if err != nil {
		panic(err)
	}
	// 连接服务端
	conn, err := net.DialTCP("tcp", LocalAddr, RemoteAddr)
	if err != nil {
		panic(err)
	}
	// 连接管理
	HandleConnectionForClient(conn)
}
```

## 3 域名解析

### 1. DNS正向解析

CNAME 被称为规范名字。这种记录允许您将多个名字映射到同一台计算机。 通常用于同时提供WWW和MAIL服务的计算机。

```go
//域名解析到cname
func LookupCNAME(name string) (cname string, err error)
//域名解析到地址
func LookupHost(host string) (addrs []string, err error)
//域名解析到地址[]IP结构体.可以对具体ip进行相关操作(是否回环地址,子网,网络号等)
func LookupIP(host string) (addrs []IP, err error)
```

### 2. DNS反向解析

```go
// 根据ip地址查找主机名地址(必须得是可以解析到的域名)[dig -x ipaddress]
func LookupAddr(addr string) (name []string, err error)
```

## 4 其他常用接口

### 1. Conn接口

```go
type Conn interface {
    // Read从连接中读取数据
    // Read方法可能会在超过某个固定时间限制后超时返回错误，该错误的Timeout()方法返回真
    Read(b []byte) (n int, err error)
    // Write从连接中写入数据
    // Write方法可能会在超过某个固定时间限制后超时返回错误，该错误的Timeout()方法返回真
    Write(b []byte) (n int, err error)
    // Close方法关闭该连接
    // 并会导致任何阻塞中的Read或Write方法不再阻塞并返回错误
    Close() error
    // 返回本地网络地址
    LocalAddr() Addr
    // 返回远端网络地址
    RemoteAddr() Addr
    // 设定该连接的读写deadline，等价于同时调用SetReadDeadline和SetWriteDeadline
    // deadline是一个绝对时间，超过该时间后I/O操作就会直接因超时失败返回而不会阻塞
    // deadline对之后的所有I/O操作都起效，而不仅仅是下一次的读或写操作
    // 参数t为零值表示不设置期限
    SetDeadline(t time.Time) error
    // 设定该连接的读操作deadline，参数t为零值表示不设置期限
    SetReadDeadline(t time.Time) error
    // 设定该连接的写操作deadline，参数t为零值表示不设置期限
    // 即使写入超时，返回值n也可能>0，说明成功写入了部分数据
    SetWriteDeadline(t time.Time) error
}
```

### 2. PacketConn接口

```go
type PacketConn interface {
    // ReadFrom方法从连接读取一个数据包，并将有效信息写入b
    // ReadFrom方法可能会在超过某个固定时间限制后超时返回错误，该错误的Timeout()方法返回真
    // 返回写入的字节数和该数据包的来源地址
    ReadFrom(b []byte) (n int, addr Addr, err error)
    // WriteTo方法将有效数据b写入一个数据包发送给addr
    // WriteTo方法可能会在超过某个固定时间限制后超时返回错误，该错误的Timeout()方法返回真
    // 在面向数据包的连接中，写入超时非常罕见
    WriteTo(b []byte, addr Addr) (n int, err error)
    // Close方法关闭该连接
    // 会导致任何阻塞中的ReadFrom或WriteTo方法不再阻塞并返回错误
    Close() error
    // 返回本地网络地址
    LocalAddr() Addr
    // 设定该连接的读写deadline
    SetDeadline(t time.Time) error
    // 设定该连接的读操作deadline，参数t为零值表示不设置期限
    // 如果时间到达deadline，读操作就会直接因超时失败返回而不会阻塞
    SetReadDeadline(t time.Time) error
    // 设定该连接的写操作deadline，参数t为零值表示不设置期限
    // 如果时间到达deadline，写操作就会直接因超时失败返回而不会阻塞
    // 即使写入超时，返回值n也可能>0，说明成功写入了部分数据
    SetWriteDeadline(t time.Time) error
}
```

### 3. Error接口

```go
package net

type Error interface{
    Timeout() bool // 错误是否超时
    Temporary() bool // 是否是临时错误
}
```

## 5 示例

server.go

```go 
/*服务器代码*/
package main

import (
	"fmt"
	"net"
)

func main() {

	//创建listener
	listener, err := net.Listen("tcp", "localhost:50000") //用来监听和接收来自客户端的请求
	if err != nil {
		fmt.Println("Error Listening", err.Error()) //Error是个什么?
		return                                      //终止程序
	}

	//无限循环，监听并接受来自客户端的连接
	for {
		conn, err := listener.Accept()
		if err != nil {
			fmt.Println("Error accepting", err.Error())
			return //终止程序
		}
		go doServerStuff(conn) //?
	}
}

func doServerStuff(conn net.Conn) {
	for {
		buf := make([]byte, 512)
		len, err := conn.Read(buf) //获取客户端发送字节数
		if err != nil {
			fmt.Println("Error reading", err.Error())
			return //终止程序
		}
		fmt.Printf("Received data:%v\n", string(buf[:len]))
	}
}
```

client.go

```go 
package main

import (
	"bufio"
	"fmt"
	"net"
	"os"
	"strings"
)

func main() {
	//创建和服务器的连接
	conn, err := net.Dial("tcp", "localhost:50000")
	if err != nil {
		fmt.Println("Error dialing", err.Error()) //由于目标计算机积极拒绝而无法创建连接
		return                                    //终止程序
	}

	inputReader := bufio.NewReader(os.Stdin)	//接收来自键盘的输入
	fmt.Println("First,what is your name?")
	clientName, _ := inputReader.ReadString('\n')
	trimmedClient := strings.Trim(clientName, "\r\n") // Windows 平台下用 "\r\n"，Linux平台下使用 "\n"

	//给服务器发送信息知道程序退出
	for {
		fmt.Println("What to send to the server? Type Q to quit.")
		input, _ := inputReader.ReadString('\n')
		trimmedInput := strings.Trim(input, "\r\n")
		if trimmedInput=="Q"{
			return 
		}
		_,err=conn.Write([]byte(trimmedClient+" says: "+trimmedInput))
	}
}
```

socket.go

```go 
package main

import(
	"fmt"
	"io"
	"net"
)

func main(){
	var(
		host="www.apache.org"
		port="80"
		remote=host+":"+port
		msg string="GET / \n"
		data=make([]uint8,4096)
		read=true
		count=0
	)
	//创建一个socket
	conn,err:=net.Dial("tcp",remote)
	//发送我们的消息：一个http GET请求
	io.WriteString(conn,msg)
	//读取服务器的响应
	for read{
		count,err=conn.Read(data)
		read=(err==nil)
		fmt.Printf(string(data[0:count]))
	}
	conn.Close()
}
```

dial.go

```go
//make a connection with www.example.org:
package main

import(
	"fmt"
	"net"
	"os"
)

func main(){
	conn,err:=net.Dial("tcp","192.0.32.10:80")	//tcp ipv4
	checkConnection(conn,err)
	conn,err=net.Dial("udp","192.0.32.10:80")	//udp
	checkConnection(conn,err)
	conn,err=net.Dial("tcp","[2620:0:2d0:200::10]:80")	//tcp ipv6
	checkConnection(conn,err)
}


func checkConnection(conn net.Conn,err error){
	if err!=nil{
		fmt.Printf("error %v connecting!",err)
		os.Exit(1)
	}
	fmt.Printf("Connection is made with %v\n",conn)
}
```

server_simple.go

```go
package main

import(
	"flag"
	"fmt"
	"net"
	"syscall"
)

const maxread=25

func main(){
	flag.Parse()	//服务器地址和端口通过命令行传入参数，并通过flag包来读取这些参数
	if flag.NArg() != 2 {	//检查是否按照期望传入了2个参数
		panic("usage: host port")	//此函数停止执行，并将控制权返还给其调用者
	}
	hostAndPort := fmt.Sprintf("%s:%s", flag.Arg(0), flag.Arg(1))	//格式化成字符串
	listener := initServer(hostAndPort)
	for {
		conn, err := listener.Accept()	//接受请求，返回conn对象
		checkError(err, "Accept: ")
		go connectionHandler(conn)
	}
}

func initServer(hostAndPort string)net.Listener{
	serverAddr,err:=net.ResolveTCPAddr("tcp",hostAndPort)	//解析TCP地址
	checkError(err,"Resolving address:port failed: '"+hostAndPort+"'")
	listener,err:=net.ListenTCP("tcp",serverAddr)	//监听请求
	checkError(err,"ListenTCP: ")
	println("Listening to: ",listener.Addr().String())
	return listener
}

func connectionHandler(conn net.Conn){
	connFrom:=conn.RemoteAddr().String()	//获取客户端的地址
	println("Connection from: ",connFrom)
	sayHello(conn)
	for{
		var ibuf []byte=make([]byte, maxread+1)	//设置maxread防止溢出
		length,err:=conn.Read(ibuf[0:maxread])	//读取连接中的内容
		ibuf[maxread]=0
		switch err{
		case nil:
			handleMsg(length,err,ibuf)
		case syscall.EAGAIN:
			continue	//重新尝试连接
		default:
			goto DISCONNECT

		}
	}
DISCONNECT:
	err:=conn.Close()	//关闭连接
	println("Closed connection: ",connFrom)
	checkError(err,"Close: ")
}

func sayHello(to net.Conn) {
	obuf := []byte{'L', 'e', 't', '\'', 's', ' ', 'G', 'O', '!', '\n'}
	wrote, err := to.Write(obuf)	//发送message给客户端
	checkError(err, "Write: wrote "+string(wrote)+" bytes.")
}

func handleMsg(length int, err error, msg []byte) {
	if length > 0 {
		print("<", length, ":")
		for i := 0; ; i++ {
			if msg[i] == 0 {
				break
			}
			fmt.Printf("%c", msg[i])
		}
		print(">")
	}
}
func checkError(error error, info string) {
	if error != nil {
		panic("ERROR: " + info + " " + error.Error()) // terminate program
	}
}
```


