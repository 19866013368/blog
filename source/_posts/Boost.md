---
title: Boost
date: 2023-07-07T17:08:13+08:00
tags: C++
---
# 时间与日期
## timer库
timer库有两个组件

- timer(v1)
- cpu_timer		在第十章介绍
### timer(v1)
timer(v1)库包含三个小组件

- timer			计时器
- progress_timer	计时器
- progress_display	进度指示器
#### timer类
timer类用于测量时间的流逝
需要包含头文件<boost/timer.cpp>
位于 命名空间boost中
##### 方法

- timer.elapsed_max()			可度量的最大时间,以秒为单位
- timer.elapsed_min()			可度量的最小时间,以秒为单位
- tiiimer.elapsed()				输出从timer类创建到现在已经流逝的时间
#### progress_timer类
progress_timer类继承于timer类,接口与timer类一样
与timer不同的是,progress_timer不需要手动显示时间,在progress_timer对象被析构时会自动输出
同时,progress_timer的构造函数可以传入一个输出流,当progress_timer对象被析构时,会输出到对应的流中

#### progress_display类
progress_display类是一个独立的类,与其他前面两个类没有关系
progress_display类的构造函数接受一个long类型的参数,表示用于进度显示的基数
progress_display类重载了+=和++运算符
### date_time库
date_time库包含两部分

- gregorian		处理日期
- posix_time		处理时间

date_time库使用枚举special_values定义了如下特殊时间概念

- pos_infin			表示正无限
- neg_infin			表示负无限
- not_a_date_time		无效时间
- min_date_time		表示最小日期或时间
- max_date_time		表示最大日期或时间
## gregorian
#### date类
date_time库的日期基于格里高利历,支持从1400-01-01到9999-12-31
需要包含头文件<boost/date_time/gregorian/gregorian.hpp>
位于命名空间boost::gregorian中
> date支持比较操作和流输入输出
> date支持加减运算,但是两个date对象的加操作时无意义的
> date可以与日期长度类进行加减运算

构造函数:date(year, month, day)
> 如果不希望date缺省构造出无效日期,可以在包含头文件前定义宏DATE_TIME_NO_DEFAULT_CONSTRUCTOR

##### day_clock类
day_clock类有两个静态成员函数

- loack_day()			
- universal_day()	

这两个成员函数都会返回当天的日期,分别是本地日期和UTC日期
##### 成员函数

- year_month_day()		返回一个ymd_type结构,ymd_type有三个成员变量year, month, day
- day_of_week()			返回date的星期数
- day_of_year()			返回date是当年的第几天
- end_of_month()			返回当月的最好一天date对象
- week_number()			返回date对象所在的周是当年的第几周
- is_xxx()					
   - is_infinity()			是否是一个无限日期
   - is_neg_infinity()		是否是一个负无限日期
   - is_pos_infinity()		是否是一个正无限日期
   - is_not_a_date()		是否是一个无效日期
   - is_special()			是否是一个特殊日期
##### 自由函数

- to_simple_stirng(date d)			转换为YYYY-mmm-DD格式的字符串
- to_iso_stirng(date d)				转换为YYYYMMDD格式的数字字符串
- to_iso_extended_string(date d)	转换为YYYY-MM-DD格式的数字字符串
- to_tm(date)						转换到c标准库中tm结构
- date_form_tm(tm datetm)		从tm转换到date对象
##### 迭代器
date_time库为日期处理提供了迭代器

- day_iterator
- week_iterator
- month_iterator
- year_iterator

只支持前置运算符
#### date_duration类
日期长度类
别名days
支持比较,加减法和递增递减操作
但不能除以另一个days对象,也不支持乘法,取模,取余
##### 成员函数

- days()			返回时长的天数
- is_special()		判断是否为特殊值
- is_negative()	判断是否为负值
- unit()			返回时长的最小值,即days(1)
##### years months weeks类
months和years类全面支持加减乘除运算
使用number_of_monthss()和number_of_years()获得表示的月数和年份
weeks继承自days
#### date_period类
日期期间类
左闭右开
端点是两个date对象
构造函数有两个

- period(date, date)	以两个日期构造
- period(date, days)	以起始日于日期长度构造

可以进行比较,但是是依据第一个区间的end与第二个区间的begin
如果两个日期期间相交或者包含,那么比较操作没有意义
支持输入输出
##### 成员函数

- begin()			返回起始日
- last()			返回截止日
- end()			返回last后一天
- length()			返回日期区间长度
- is_null()			判断是否非法
- shift()			将日期区间平移n天
- expand()		将日期区间向两端延伸n天,相当于扩大2n天
- is_before()/is_after()	日期区间是否在日期前或后
- contains()		日期区间是否包含另一个区间或日期
- intersects()		两个日期区间是否存在交集
- intersection()	返回两个区间的交集,如果无交集返回一个无效区间
- is_adjacent()	两个区间是否相邻
- merge()			求并集,如无交集或不相邻则返回无效区间
- span()			求并集,包括间隔
#### calendar类
##### 静态成员函数

- is_leap_year()			判断年份是否为闰年
- end_of_month_day()		返回该月最后一天

---

## posix_time
需要包含头文件<boost/date_time/posix_time/posix_time.hpp>
位于命名空间boost::posix_time中
> 需要注意的是,posix_time类中并没有引用boost::gregorian,需要手动声明

#### dateduration类
date_duration是一个时间长度类
构造函数 : time_duration(hour_type, min_type, sec_type = 0, fractional_seconds_type = 0);
时分秒等值可以是任意的数字,当超出所对应的限度时,会自动进位或借位
使用字符串创建 : duration_from_string("YY:MM:SS:MS");
支持比较运算和四则运算
可以转换到tm结构,但不能反向转换
##### 子类

- hours
- minutes
- seconds
- millisec
##### 成员函数

- hours()					获取对应的时间
- minutes()
- seconds()
- total_seconds()			返回总秒数
- total_milliseconds()		返回总毫秒数
- total_microseconds()		返回总微秒数
- is_negative()			判断正负号
- invert_sign()				返回对象的相反值
- is_xxx					判断是否为特殊对象
##### 静态成员函数

- unit()					返回最小的time_duration对象
- resolution() 				返回一个枚举值,表示时间长度的分辨率
- num_fractional_digits()	返回秒的小数部分位数
#### ptime类
构造函数 : ptime(const date_type&, const time_duration_type &, dst_flags = not_dst);
使用日期和时间长度构造
支持全序比较和加减运算
使用字符串构造 : time_from_string("YYYY-MM-DD HH:MM:SS")
##### 时钟类
因为ptime有不同的分辨率,所以有两个时钟类

- second_clock			秒级
- microsec_clock			微秒级

通过local_time()函数获得本地当前时间
##### 成员函数

- date()			返回日期
- time_of_day()	返回时间长度
##### 自由函数

- to_simple_string()				转换为YYYY-mmm-DD HH:MM:SS.ffffff格式
- to_iso_string()					转换为数字格式
- to_iso_extended_string()			转换为YYYY-MM-DDTHH:MM:SS,ffffff格式
- to_tm()							转换为tm结构
- from_time_t(time_t)				从time_t结果创建ptime对象
#### time_period类
以两个ptime作为区间的端点,同样是左闭右开
##### 成员函数

- begin()				返回开始端点
- last()				返回结束端点
- length()				返回区间的长度
- shift()				平移
- expand()			扩大
##### 时间迭代器
时间迭代器只有一个 : time_iterator
构造时穿日一个起始时间点ptime对象和一个步长time_duraion对象
# 内存管理
boost提供了一个smart_ptr库,用来智能管理内存,不需要开发者自己手动释放内存
同时提供了一个pool库,用于高效分配管理内存
## smart_ptr库
### scoped_ptr类
scoped_ptr是一个独占指针对象,不能进行拷贝构造和移动构造,所以无法共享和转让
同时,包含scoped_ptr的对象也是不可拷贝和移动的
不能使用scoped_ptr管理一个数组
scoped_ptr不支持比较运算,仅支持与nullptr比较
支持在bool语境中自动转化为bool值
#### 构造函数
构造函数 : `scoped_ptr(T *p);`
scoped_ptr的构造函数接受一个由new操作符生成的指针或空指针
#### 成员函数

- reset()				重置管理的指针
- swap()				交换两个scoped_ptr的指针
- get()				返回对象内部保存的原始指针

scoped_ptr重载了*和->运算符,这让scoped_ptr能够像普通指针一样使用
但是不能对scoped_ptr进行指针算数操作,这是不被定义的
### scoped_array类
scoped_array与scoped_ptr基本相同
scoped_array只接受new[]的结果,不能是new的表达式结果
没有begin(),end()等类似容器的迭代器操作函数
scpoed_array的速度与原始数组同样快,但是它的功能有限,不能动态增长,也没有边界检查,迭代器支持等
除非对性能有非常苛刻的要求或特殊需求,否则最好使用vector与scoped_ptr的结合
### shared_ptr类
boost库中的shared_ptr与C++标准库中的基本一致
在if/assert/for等逻辑判断语境下,shared_ptr会自动转换成bool类型,但是在其他情形必须手动转换
shared_ptr接受void *类型指针
#### 成员函数

- unique()			判断指针所有者是否唯一
- use_count()		返回当前指针的引用计数

shared_ptr支持比较运算
#### 工厂函数
`make_shared()`用于在堆区开辟内存并返回一个shared_ptr指针
这个函数是为了消除显式的new调用而诞生的
#### 删除器
shared_ptr的构造函数除了接受一个指针,还能接受一个函数指针,或函数对象
当对象被析构时,会执行这个函数对象或函数指针
```cpp
void p(void *p)
{
    cout << "对象销毁" << endl;
}

int main()
{
    {
        shared_ptr<void> ptr(nullptr, p);
    }
    cout << "进程退出" << endl;
}
```
#### 指针转型函数
指针转型不能直接使用static_cast<T*>(p.get()),这会导致转型后的指针无法再被正确管理
`dynamic_pointer_cast<T>(shared_ptr p)`
`static_pointer_cast<T>(shared_ptr p)`
`const_pointer_cast<T>(shared_ptr p)`
这三个函数用于转换shared_ptr类型
### shared_array类
与shared_ptr类类似,包装了new[]
多数情况下最好使用shared_ptr<vector>或vector<shared_ptr>替代
#### 循环引用
当两个强指针互指形成循环链表时,每个指针的引用计数都是2,将导致内存泄露
这个情况需要使用弱指针替换,在需要使用指针的时候调用lock()函数
### weak_ptr类
weak_ptr时为了配合shared_ptr而引入的,它没有重载*和->运算符
weak_ptr没有共享资源,所以不会引起计数器的增加和减少
#### 成员函数

- use_count()			获得观测资源的引用计数
- expired()			判断被观察资源是否存在
- lock()				返回一个指向被观测对象的shardd_ptr
### intrusive_ptr类
intrusive_ptr也是一种引用计数智能指针,同样支持static_pointer_cast()dynamic_pointer_cast()等操作
但它自身不直接管理引用计数,而是调用下面两个函数来间接管理:

- void instrusive_ptr_add_ref(T *p)		增加引用计数
- void instrusive_ptr_release(T *p)		减少引用计数

这两个函数需要用户自定义
instrusive_ptr的构造函数还有一个add_ref参数,表示是否增加引用计数,如果为fasle,则相当于weak_ptr
```cpp
struct counted : public boost::intrusive_ref_counter<counted>
{
    int count = 0;
};

void intrusive_ptr_add_ref(counted *p)
{
    ++p->count;
}

void intrusive_ptr_release(counted *p)
{
    if (--p->count == 0)		//检查引用计数,当为0时销毁对象
    {
        delete p;
    }
}

int main()
{
    typedef boost::intrusive_ptr<counted> counted_ptr;
    counted_ptr p(new counted);
    cout << p->count<< endl;

    counted_ptr p2(p);
    cout << p->count << endl;

    counted_ptr weak_p(p.get(), false);
    cout << p->count << endl;
}
```
除了用户自定义以外,在头文件<boost/smart_ptr/intrusive_ref_counter.hpp>中定义了一个辅助类instrusive_ref_counter
当我们需要使用intrusive_ptr时,只需要让需要被管理的类继承instrusive_ref_counter<T>即可,不需要自行定义计数变量与计数行为函数
```cpp
struct counted : public boost::intrusive_ref_counter<counted>
{
    //int count = 0;
};

// void intrusive_ptr_add_ref(counted *p)
// {
//     ++p->count;
// }

// void intrusive_ptr_release(counted *p)
// {
//     if (--p->count == 0)
//     {
//         delete p;
//     }
// }

int main()
{
    typedef boost::intrusive_ptr<counted> counted_ptr;
    counted_ptr p(new counted);
    cout << p->use_count() << endl;

    counted_ptr p2(p);
    cout << p->use_count() << endl;

    counted_ptr weak_p(p.get(), false);
    cout << p->use_count() << endl;
}
```
## poo库
### pool类
需要包含头文件<boost/pool/pool.hpp>
pool能够管理内置数据类型
为了避免不必要的链接错误,可以在包含头文件之前定义宏BOOST_SYSTEM_NO_DEPRECATED
#### 构造与析构
pool的构造函数接受一个size_type类型的整数,指示每次pool分配的内存大小
析构时,pool将自动释放它所持有的所有内存块
#### 成员函数

- get_requested_size()			获得内存池每次分配内存的大小
- malloc()						分配内存
- ordered_malloc()				分配内存的同时合并块链表,同时可以接受一个参数,指示需要分配内存块的数量
- is_form()					测试内存块是否是从这个内存池分配出去的
- free()						释放内存(必须是这个内存池分配出去的)
- release_memory()			让内存池是否所有未被分配的内存
- purge_memory()				强制释放所有内存
```cpp
#define BOOST_SYSTEM_NO_DEPRECATED
#include <iostream>
#include <boost/pool/pool.hpp>

using namespace std;
using namespace boost;

int main()
{
    pool<> pl(sizeof(int));			//先创建内存池对象
    int *p = static_cast<int *>(pl.malloc());//分配内存
    cout << pl.is_from(p) << endl;
	
    pl.free(p);		//释放内存
    for (int i = 0; i < 100; ++i)
    {
        pl.ordered_malloc(10);//批量分配内存
    }
    *p = 2;
    cout << *p << endl;
}
```
pool在分配内存失败时不会抛出异常,所以应检查malloc的返回值
### object_pool类
需要包含头文件<boost/pool/object_pool.hpp>
object_pool是用于类对象的内存池,所以在析构时会对所有分配的内存块调用析构函数
object_pool的模板类型参数T指定了要分配元素类型
#### 成员函数

- malloc()					分配一块内存块
- free()					释放一块内存块

则两个函数是对原始内存的操作,并不会调用类的构造函数和析构函数,应该用以下两个函数

- constructc()				先分配内存,再传入构造函数,返回一个已经初始化的对象指针
- destory()				调用对象的析构函数,然后再释放内存
```cpp
class demo_class
{
public:
    int a, b, c;
    demo_class()
    {
        a = 1;
        b = 2;
        c = 3;
    }
};

int main()
{
    object_pool<demo_class> pl;
    auto p = pl.malloc();
    cout << p << endl;
    cout << p->a << endl;		//输出未知值,因为没有调用构造函数
    p = pl.construct();
    cout << p << endl;
    cout << p->a << endl;

    object_pool<string> pls;
    for (int i = 0; i < 10; i++)
    {
        string *ps = pls.construct("hello world");
        cout << *ps << "\t" << i << endl;
    }
}
```
### singleton_pool类
需要包含头文件<boost/pool/singleton_pool.hpp>
默认提供线程安全,如果不使用多线程,可以在头文件前定义宏BOOST_POOL_NO_MT
#### 构造函数
第一个参数TAG仅仅用于标记不同的单件,可以是空类
第二个参数等同于pool构造函数中的整数,指示pool分配内存块的大小
#### 成员函数
singleton_pool的接口与pool完全一致
但是成员函数都是静态的,所以不需要声明singleton_pool的实力,直接用域操作符操作
singleton_pool是单件,所以它的生命周期与整个程序同样长
### pool_alloc
需要包含头文件<boost/pool/pool_alloc.hpp>
pool_alloc提供了两个可以用于标准容器模板参数的内存分配器,分别是

- pool_alloc
- fast_pool_allocator

当内存分配失败时会抛出异常std::bad_alloc
```cpp
#include <iostream>
#include <boost/pool/pool_alloc.hpp>
#include <vector>

using namespace boost;
using namespace std;

int main()
{
    vector<int, pool_allocator<int> > v;
    v.push_back(10);
    cout << v.size() << endl;
}
```
# 实用工具
## noncopyable类
noncopyable允许程序轻松实现一个举止拷贝的类
位于名称空间boost中
需要包含头文件<boost/noncopyable.hpp>或<boost/utility.hpp>(包含多个小工具的实现)
### 原理
在C++中，如果不明确定义拷贝构造函数和拷贝构造符，编译器会自动为我们生成这两个函数
如果我们不需要拷贝语义，可以通过将拷贝构造函数和拷贝构造符私有化来实现
我们也可以通过继承noncopyable类来实现，这样效果是相同的，但是代码更少一些，也更清晰
在C++11中定义了新的default和delete关键字，也可以使用这两个关键字实现
## assign库
assign为容器初始化和赋值提供了便利
assign库只支持c++98标准容器(vector,list,set)等
assign库位于名称空间boost::assign中
需要包含头文件<boost/assign.hpp>
### list_inserter类
list_inserter类类似于std::back_inserter
list_liserter类重载了+=和,运算符
提供了三个工厂函数,供拥有同名成员函数的容器使用,返回对应的list_liserter对象

- push_back()
- push_front()
- insert()
```cpp
using namespace boost::assign;

vector<int> v;
v += 1, 2, 3, 4, 5, 6 * 6;
for (auto &i : v)
{
	cout << i << endl;
}

set<string> s;
s += "cpp", "java", "c#", "python";
for (auto &i : s)
{
	cout << i << endl;
}

map<int, stirng> m;
m += make_pair(1, "one"), make_pair(2, "two");
```
```cpp
using namespace boost::assign;
vector<int> v;
push_back(v)(1)(2)(3)(4)(5);
// push_back(v),1,2,3,4,5;

map<int, string> m;
insert(m)(1, "one")(2, "two");
```
### generic_list类
提供了三个工厂函数,返回对应的generic_list对象

- list_of()
- map_list_of()/pair_list_of()
- tuple_list_of()
```cpp
using namespace boost::assign;
vector<int> stk = (list_of(1), 2, 3);
for(;!stk.empty();){
	cout<<stk.top()<<endl;
	stk.pop();
}

map<int, int> m1 = map_list_of(1, "one")(2, "two");
```
### 减少重复输入
list_inserter类和generic_list类都提供了成员函数

- repeat()			把第二个参数作为要填入的值,重复第一个参数指定的次数
- repeat_fun()		与repeat一样,但是第二个参数接受无参的函数或函数对象
- range()			可以把一个序列的全部或部分元素插入到另一个序列中
```cpp
using namespace boost::assign;

vector<int> b = list_of(1).repear(3, 2)(3)(4)(5);
//v = 1, 2, 2, 2, 3, 4, 5
```
stack,queue和priority_queue没有push_back/push_front函数,所以assign库提供了push()函数
但stack可以使用+=
初始化的list_of表达式最后调用to_adapter()成员函数来适配到非标准容器
```cpp
stack<int> stk = (list_of(1), 2, 3).to_adapter();
stk += 4, 5, 6;
for(;!stk.empty();){
	cout<<stk.top()<<endl;
	stk.pop();
}
```
## operators库
operators位于名称空间boost中
需要包含头文件<boost/operators.hpp>
### 算术运算符重载

- equality_comparable			要求提供==,自动实现!=
- less_than_comarable			要求提供<,自动实现>,<=,>=
- addable						要求提供+=,自动实现+
- subtractable					要求提供-=,自动实现-
- incrementable				要求实现前置++,自动实现后置++
- decrementable				要求提供前置--,自动实现后置--
- equivalent					要求提供<,自动实现==
- totally_ordered				全序概念,组合了equality_comparable,less_than_comarable
- additive						可加减概念,组合了addable和subtractable
- multiplicative				可乘除概念,组合了multipliable和dividable
- arithmetic					算术运算概念,组合了additive,multiplicative
- unit_stoppable				可步进概念,组合了incrementable,decrementable
```cpp
class point : boost::totally_ordered<point, additive<point> >
{
    int x, y, z;

public:
    explicit point(int a = 0, int b = 0, int c = 0) : x(a), y(b), z(c) {}
    void print() const
    {
        cout << x << "," << y << "," << z << endl;
    }

    friend bool operator<(const point &l, const point &r)
    {
        return (l.x * l.x + l.y * l.y + l.z * l.z) < (r.x * r.x + r.y * r.y + r.z * r.z);
    }

    friend bool operator==(const point &l, const point &r)
    {
        return (l.x * l.x + l.y * l.y + l.z * l.z) == (r.x * r.x + r.y * r.y + r.z * r.z);
    }

    point &operator+=(const point &r)
    {
        x += r.x;
        y += r.y;
        z += r.z;
        return *this;
    }

    point &operator-=(const point &r)
    {
        x -= r.x;
        y -= r.y;
        z -= r.z;
        return *this;
    }
};

int main()
{
    point p0(1, 2, 3), p1(2, 3, 4);
    cout << (p0 < p1) << endl;
    cout << (p0 > p1) << endl;
    (p0 + p1).print();
}
```
### 解引用操作符
operators库使用dereferenceable类提供了对解引用操作符*,->的支持
temple <calss T, class P, class B = ...>

- 第一个参数T是要实现operator->的子类
- 第二个参数P事operator->所返回的类型,通常应该是T*
- 最后一个蚕食B是用于基类链技术的父类,无需关心

**使用时必须使用public继承**
**operator*定义必须是常函数**
```cpp
template <typename T>
class my_smart_ptr : public boost::dereferenceable<my_smart_ptr<T>, T *>
{
    T *p;

public:
    my_smart_ptr(T *x) : p(x) {}
    ~my_smart_ptr() { delete p; };
    T &operator*() const
    {
        return *p;
    }
};

int main()
{
    my_smart_ptr<string> str(new string("hello world"));
    cout << str->size() << endl;
}
```
### 下标运算符
与解引用运算符类似
```cpp
template <typename T>
class my_smart_arrat : public boost::indexable<my_smart_arrat<T>, int, T &>
{
    T *p;

public:
    typedef my_smart_arrat<T> this_type;
    typedef T *iter_type;

    my_smart_arrat(T *x) : p(x) {}
    ~my_smart_arrat() { delete[] p; }
    friend iter_type operator+(const this_type &a, int n)
    {
        return a.p + n;
    }
};

int main()
{
    my_smart_arrat<double> ma(new double[10]);
    ma[0] = 1.0;
    *(ma + 1) = 2.0;
    cout << ma[1] << endl;
}
```
### bool转型操作符
explicit_operator_bool不属于operators库,而是core库的一部分
位于头文件<boost/core/explicit_operator_bool.hpp>
要求类实现!运算符
提供三个操作符定义宏

- BOOST_EXPLICIT_OPERATOR_BOOL()				安全bool转型
- BOOST_EXPLICIT_OPERATOR_BOOL_NOEXCEPT()	异常保证bool转型
- BOOST_CONSTEXPR_EXPLICIT_OPERATOR_BOOL()	编译器常量bool转型

![截屏2022-06-21 下午4.40.48.png](https://cdn.nlark.com/yuque/0/2022/png/26391976/1655800864400-6552a4ab-8fbb-4a4c-9d10-322e3854c219.png#clientId=u7e38095f-71db-4&from=drop&height=259&id=jK7BY&originHeight=345&originWidth=1016&originalType=binary&ratio=1&rotation=0&showTitle=false&size=117481&status=done&style=shadow&taskId=u45a3892e-be4c-49fa-982f-c1eecbaa2ac&title=&width=762)

