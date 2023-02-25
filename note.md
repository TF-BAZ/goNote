## 包声明

大写开头的外部能访问，小写开头的只有内部能访问

## 引入包

import sPackName

## 函数

```go
func function_name( [parameter list] ) [return_types] {
   // 函数体
}
// 有返回值时，return_types必须设置。

// return_types可以设置多个。这个时候需要使用()括起来
fun sum(x,y int)(int,int){
	return x+y,x
}


// 参数形式：值传递。上面那个就是
// 参数形式：引用传递。类似指针
func swap(x, y *int) {
	temp := *x
	*x = *y
	*y = temp
}

func main() {
	a := 1
	b := 2
	fmt.Println(a, b)
	swap(&a, &b)
	fmt.Println(a, b)
}


```



## 变量&常量

### 变量

**类型**：

类型前加chan为对应的通道类型，如 chan int为int通道类型。make(chan int)创建一个int通道

**定义**：

```
var i int		
var i = 1		//有数值可以忽略类型
var i int = 1	//类型和数值都有
i := 1			//只能用于定义局部变量
var vname1, vname2, vname3 = v1, v2, v3			//支持多变量
// 这种因式分解关键字的写法一般用于声明全局变量
var (
    vname1 v_type1
    vname2 v_type2
)
```

### 常量

**定义**：

```
const WIDTH int = 1
const WIDTH = 1			//省略类型
const a, b, c = 1, false, "str" //多重赋值
const (				//因式分解
	yes=true
	no=false
)
const (			//省略赋值
	a=1
	b					//b、c、d都为1。默认和上一个值相同的求法
	c
	d
)
const (			//省略赋值使用iota
	a=iota				//iota表示因式分解语法中当前索引。所以a=0,b=1,c=2,d=3
	b
	c
	d
)
const (			//省略赋值活用iota
	a=3<<iota			//a=3<<0=3,b=3<<1=6,c=3<<2=12,d=3<<3=24
	b
	c
	d
)
```

### 通道

**定义**：

```
var a chan int = make(chan int)	//类型前加chan为对应的通道类型，如 chan int为int通道类型。make(chan int)创建一个int通道
a := make(chan int) 
a := make(chan int,100)			//指定缓冲区大小
```

**使用**：

```go
n:=<-ch			//通过通道获取数据
ch<-n			//将数据给到通道
close(ch)		//关闭通道
n,ok:=<-ch		//当通道被关闭时，ok不为nil
```



## 语句 & 表达式

### 条件语句

#### if

```
if true {
//	
}
else if false{
//
}
else{
//
}
```

#### switch

**特点**：

1.go的switch像默认每个case追加break，要不像break，需要在最后加**fallthough**
2.可以不给switch的内容
3.匹配第一个true的case

```go
// 不加switch内容
switch {
    case true:
	    fmt.Println("a")
    case false:
	    fmt.Println("b")
    case true:
   	    fmt.Println("c")
    default:
	    fmt.Println("d")
}
```

**type-switch**

```go
 	var a interface{}	// 只能用于interface
	switch a.(type) {
		case nil:
			fmt.Println("nil")
		case int:
			fmt.Println("int")
	}

```



#### select

### 循环语句

go只有for循环，没有while循环

**使用**

```
for init; condition; post { }		
for i:=0;i<10;i++{ }				// 可以使用:=定义变量
for condition { }					// 和C的while相同
for { }								// 和 C 的 for(;;) 一样：
for key,value := range lst{ }				// foreach，无论是lst，string还是map。都是获得key,value。可以使用_忽略key。或者不写value忽略value。
```



## 注释

// 或/**/

## 其他

### 字符串操作

**连接**：+，例如"a"+"b"

### 格式化输出

fmt.Println(args)
fmt.Print(args)
fmt.Sprintf(args)	根据格式化参数生成格式化的字符串并返回该字符串。
fmt.Printf(args)		根据格式化参数生成格式化的字符串并写入标准输出。
%s 字符串格式，%d 十进制的整数格式。**%T** 输出变量类型。

### 逃逸分析

go会根据局部变量在函数结束后是否还会被使用到决定这个局部变量是分配在栈上还是堆上。


```
func f() *int{
	i:=1
	return &i		//返回地址，导致go编译器认为可能会在函数结束后使用到，一次i分配在堆上
}

func g() int{
	i:=1
	return i		//直接返回变量，go编译器知道i只在函数内可使用，分配在栈上
}					//go直接返回局部变量时是返回副本

func main(){
	i:=f()
}
```

### init()

包的init会在第一次导入包的时候执行

## 包

### runtime

runtime.GOMAXPROC(n int)			设置go的最大的逻辑处理器数量
runtime.NumCPU()							返回机器的cpu核数
runtime.Gosched()							yield thread

### sync

#### sync.WaitGroup

wg.Add(n int)				添加n个计数
wg.Done()					减一个计数
wg.Wait()					阻塞直到计数为0

#### sync.Mutex

mutex.Lock()		互斥锁加锁
mutex.UnLock()	互斥锁解锁

### sync/atomic

atomic.AddInt64(num *int64 ,n int)		num+n的原子操作。保证执行这个操作时只有一个goroutine在运行
atomic.LoadInt64(num *int64)				读取num的数值并返回的原子操作
atomic.StoreInt64(num *int64,n int64)	保存num=n的原子操作
