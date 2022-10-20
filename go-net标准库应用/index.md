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


