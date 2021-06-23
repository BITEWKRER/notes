

# c++

## c++ 编程流程

![image-20200416145332795](D:\notes\images\image-20200416145332795.png)

### 多态性

> 准确的反应分层之后的对象集合中的每个**对象**行为的**个性**。

- 重载
  - 相同函数名，不同参数
- 重写
  - 子类重写父类方法
- 重定义
  - 不同作用域，一个父类，一个子类，函数名相同，且不构成重写。

## 基本编程语句注意

**switch** 中条件只能是**整型，字符型，枚举型**，**case** 常量表达式的值必须**互不相同**，否则**编译错误**。报错信息：**duplicate case value**

**if\~，if\~else，if~else if ~else**

## 素数判断

```c++
for(int j = 2;i<sqrt(m);j++){
    if(m%j==0){
        cout<<"不是素数";
    }
}
```

## 流状态

![image-20200309211438725](D:\notes\images\image-20200309211438725.png)

### 三个常用流状态

```c++
#include <iostream>
using namespace std;

int main()
{
    cout.width(10);
    cout.fill('!');
    cout << 23;
}
//	!!!!!!!!23
```

#### 与<<连用

```c++
#include <iostream>
#include <iomanip>	//导包，作用：控制格式输出

using namespace std;

int main()
{
    cout << setw(10) << setfill('!') << 23;
}
//	!!!!!!!!23
```



![image-20200309211505573](D:\notes\images\image-20200309211505573.png)

![image-20200309211523879](D:\notes\images\image-20200309211523879.png)

## 文件流

> #include<fstream>	可以打开或关闭文件流，两种方式ios::in	ios::out
>
> #include<ifstream>	输入文件流	默认打开方式ios::in	
>
> #include<ofistream>	输出文件流	默认打开方式ios::out

![image-20200309211611921](D:\notes\images\image-20200309211611921.png)

- P71

## goto

![image-20200309215127730](D:\notes\images\image-20200309215127730.png)

## 数据类型

**内部数据类型**

- int
- char
- bool
- float
- double

**变异类型**

整型数组	**int[]**

浮点引用	**double&**

字符指针	**char***

**自定义数据类型**：用class关键字构造的数据类型。

### 二进制补码

$$
00001010=0*2^{0}+1*2^{1}+0*2^{2}+1*2^{3}+0*2^{4}... = 10
$$

**二进制加法**
$$
00001010 + 00001100 = 00010110
$$
**二进制负整数**：**取反加一，0代表正数，1代表负数**
$$
-15 \\= -00001111 \\= 11110001
$$
**二进制减法**，将数**转换为正**然后**相加**，在**取反加一**得到结果
$$
3-5 \\
= 00000011 - 00000101 \\
= {00000011 + 11111011} \\
= {11111110} \\
= {-00000010} \\
= -2
$$

**二进制乘法**：**左移，相加**，未解决，实在不行就按照十进制相乘之后转换成二进制
$$
3*5\\=00000011 * 00000101\\=00000011 * 00000001 + 0000001*00000100\\=00000011左移0位+00000011左移2位\\=00000011+00001100\\=00001111\\=15
$$
**二进制除法：右移，相加**，未解决，实在不行就按照十进制相除之后转换成二进制
$$
13/3\\=00001101/00000011\\=(00001100+00000001)/00000011\\=00001100/00000011 + 00000001/00000011\\=00000100余00000001\\=4
$$

## 占用字节

- char	1	上限255
- unsigned int    2    上限65535
- short int	2 	上限32767
- int	4 	
- long int	4
- float   4
- double	8
- long double   10
- 指针	8

## 运算注意事项

**余数的正负性**取决于**被除数的正负性**

```c++
11/(-5)= -2
-11/(-5)= 2	
```

```c++
bool a = 3;
bool b = 1;
bool d = a-b;	// 1-1
count << boolalpha<< d <<endl;
// false
```

**浮点型**
$$
-306.5 = -0.3065*10^{3},其中-是符号，指数3是阶码，0.3065是小数部分\\左右端非0部分包起来的部分为有效值3065.
$$
**十进制规格化：**浮点数通过调整阶码，使得小数点前不含有效数字，小数点后一位由非0数字表示。即，-306.5规格化为-0.3065*10^3

**十进制浮点数转换为二级制浮点数：乘2取整法**

![image-20200417185556249](D:\notes\images\image-20200417185556249.png)

## 编译执行过程

**书写（\*.cpp/\*.c++文件）->预处理（*.ii 文件）->预编译（\*.s文件）->目标文件(\*.o)->连接->执行**

![image-20200312180830909](D:\notes\images\image-20200312180830909.png)

## 字符指针

> *str（字符指针）：指将所指空间上的值当成**字符**来操作
>
> str（字符指针变量）：输出字符指针就是输出c--串，即输出str时，从h开始到0结束

```c++
#include<iostream>
using namespace std;
int main(){
    char * str = "hello";
    cout<<*str<<endl;// 输出h
    cout<<str<<endl;//输出hello
}
```

### 常见库函数操作

![image-20200315182432505](D:\notes\images\image-20200315182432505.png)

## string

![image-20200315182705074](D:\notes\images\image-20200315182705074.png)

### 重点，未解

![image-20200417193953892](D:\notes\images\image-20200417193953892.png)

## char和string的输入输出

> 注意：**cin**无法识别**空格**和**回车**的差异，而**getline()可以识别空格**

- cin>>s
- getline(cin,s)
- cin.gteline(a,40) // char a [40];
- 读取单个字符 cin.get()

## string流

> #include<sstream>

![image-20200315183944569](D:\notes\images\image-20200315183944569.png)

## 数组

数组定义：

**类型名	数组名[常量表达式]**

**常量表达式**表示数组元素的**个数**，并不表示**最大下标志**

```c++
    int a[5];
    int n = 100;
    int b[n]; 	//错，数组元素个数必须是常量
    const int x = 100;
    int c[x];	//可以
```

**可以访问的元素的个数a[0]~a[4]，但是不能访问a[5]**

### 数组初始化

> **一维数组**：int num[10] = {1,2,3,4,5};
>
> ![image-20200315183944569](D:\notes/images/image-20200316225725226.png)
>
> 如果**初始值个数**不足等号左边方括号中**规定的元素的个数**，这后面的元素值全为**0**。
>
> 如：array5[3],array[4]全为0
>
> **二维数组**：int a[2] [5] ;
>
> int a[2] [5] = {{1,2,3},{2,4,5}};

### 默认值

全局数组：函数外定义或加上static修饰的数组定义，其元素总是被清0

静态数组：内部的值是不确定的。

```c++
#include <iostream> 
using namespace std; 
int main(void) 
{ 
    const int a = 10; 
    int * p = (int *)(&a); 
    *p = 20; 
    cout<<"a = "<<a<<", *p = "<<*p<<endl; 
    return 0; 
} 
	//a = 10,*p = 20
```



## 向量

> #include<vector>

![image-20200315183944569](D:\notes/images/image-20200316230251702.png)

向量元素的位置也属于一种类型（**vector<int>::iterator**），称为遍历器。

如下输出向量中所有元素

```c++
#include <iostream>
#include <vector>

using namespace std;

int main(int argc, char const *argv[])
{
    vector<int> a(10, 1);
    for (vector<int>::iterator it = a.begin(); it != a.end(); ++it)
    {
        cout << *it << " ";
    }
}
```

### 对象成员函数方法

![image-20200315183944569](D:\notes/images/image-20200316230415215.png)

## 指针与引用

**指针定义**，**&**表示实体的地址

```c++
	//普通指针
	int * a;
	char *b;
	float * c;
	double*d;
	//二级指针
	int value = 1;
	int *tmp = &value;	//普通指针
	int**e = &tmp;	//指向指针的指针
	cout<<**e<<*tmp<<value<<endl;	//1，1，1
	//指针赋值
	int *tmp1;
	int value = 10;
	int *tmp2 = &value;	//定义时赋值
	tmp1 = &value;	//定义后赋值
```

**指针的0值**不是表示指向地址0空间，而是**表示空指针**，不指向任何空间。

**强转操作**

```c++
	float f = 3.15;	
	int *p = (int *)&f;
```

**地址转换**

```c++
	reinterpret_cast<type>();	//重解释
    static_cast<type>();		//静态转换
	dynamic_cast<type>();		//动态转换
	const_cast<type>();			//写开禁
```

**指针常量**：指针值不能修改的指针。

**常量指针**：指向常量的指针。

若**const修饰指针本身**，则为**指针常量**，否则就为**常量指针**。

```c++
	int b = 1;	
	//注意const的位置
	int const * a = &b;	//指针常量
	int * const c = &b;	//指针常量

	const int* const f = &b;	//常量指针常量

	const int * d = &b; //常量指针
```

**区别：**

**常量指针：**

- 常量指针不能修改数据
- 常量指针可以指向其他变量

**指针常量：**

- 指针常量可以修改数据
- 指针常量不能指向其他变量
- 在定义时赋值

**常量指针常量：**

- 不可以修改内存数据
- 不可以指向其他变量
- 定义时赋值

![image-20200419210709535](D:\notes\images\image-20200419210709535.png)

赋值时只能赋值给相同类型，即`const int *b = &a; int * p =&b ;`是**错误**的，应该是`const int *p = &b;`

### 引用

> 引用是个**别名**，当建立引用时，用一个**具体类型的实体**去**初始化别名**，之后便与关联实体变量或对象享受**相同待遇**

**定义**时必须初始化，可以修改值，但**地址永不会变**，即定义时的地址。

```c++
	int someInt = 5;
	int& rInt = someInt;	//初始化
	int & rInt = someInt;
	int &rInt = someInt;
```

## 计算表达

**逗号表达式**:求值过程由左至右

```c++
	d = (a = 2 , b = a + 5 , a * b);	//14
```

**操作符优先级和结合性**

![image-20200421091540645](D:\notes\images\image-20200421091540645.png)

```c++
	++a++;	//++a(++),右结合性
```

注意：

> **无符号数**和**有符号数**在计算时都是以**256为模**的。

```c++
	unsigned int b1 = 255,b2 = 3;
	cout << b1 + b2 << endl;	// 258 % 256 =2
```

### 相容类型的转换

#### 隐式转换

![image-20200420202932598](D:\notes\images\image-20200420202932598.png)

#### 显式转换：不太懂

通过函数`static_const<type>(),reinterpret_cast<type>()`等进行数据类型转换。

### 条件表达

> 关系表达式的值只有两种，成立1，否则0

```c++
	int x = 9;
	x = (x == 9);
	cout << x << endl; 	//1
```

### 位操作

> 整数特有的操作，包括 `<<,>>,&,|,^,~`六个操作。

#### 左移一位（乘二），高位挤掉，右端补零，无论是否有符号

```c++
    int a = -2;
    a = a << 1;
    cout << a << endl;	//-4
    a = a << 2;
    cout << a << endl;	//-16
```

#### 右移一位（除二），高位挤0或1，即：最高位是1，挤1，最高位0，挤0。无符号数统一挤0

```c++
	short int a = -16;
    a = a >> 2;
    cout << a << endl;	// -4
```

#### 位与操作

> 位于操作将`&`两个操作数的每一位做与操作，**都为一则为一，否则为零**

![image-20200421092320901](D:\notes\images\image-20200421092320901.png)

#### 位或操作

> 位或操作`|`是将两个操作数每一位做或操作，**都为零为零，否则为一**

![image-20200420221729849](D:\notes\images\image-20200420221729849.png)

#### 位异或操作

> 位异或是将两个操作数的每一次做异或操作，**都为0或1，值为0，否则为1**

![image-20200420221919580](D:\notes\images\image-20200420221919580.png)

#### 位反操作

> 位反是将每一个操作数取反，**0变成1，1变成0**

![image-20200420222012319](D:\notes\images\image-20200420222012319.png)	

习题4：

```c++
#include <iostream>
#include <cmath>
#include <fstream>
#include <vector>
#include <string>

using namespace std;

long double jiecheng(int num);
vector<long double> getRead();

int main(int argc, char const *argv[])
{
    long double result = jiecheng(16);
    vector<long double> list = getRead();
    for (int i = 0; i < list.size(); i += 2)
    {
        if (fabs(list[i] * list[i + 1]) == result)
        {
            cout << list[i] << " " << list[i + 1] << endl;
        }
    }
}
vector<long double> getRead()
{
    vector<long double> list;
    ifstream file("abc.txt");
    for (long double tmp = 0; file >> tmp;)
    {
        list.push_back(tmp);
    }
    return list;
}
long double jiecheng(int num)
{
    if (num == 0)
    {
        return 1;
    }
    return num * jiecheng(--num);
}
```

























































