# 《Essential c++》——第七章
---


### 异常处理

### 抛出异常（[代码展示](../code/essential/exception.cpp)）

为了保证程序具有一定的容错能力，在出现意外的情况下不至于出现灾难性的后果，因此在程序中最好要考虑各种意外，并给出处理方法。

- 异常就是程序中的突发性事件。

- 设计异常的基本思想：
让一个函数在出现了自己无法处理的错误时，抛出（throw）一个异常，然后它的调用者能够处理这些问题。

 C++之父Bjarne Stroustrup在《The C++ Programming Language》中讲到：
 > 一个库的作者可以检测出发生了运行时错误，但一般不知道怎样去处理它们（因为和用户具体的应用有关）；另一方面，库的用户知道怎样处理这些错误，但却无法检查它们何时发生（如果能检测，就可以再用户的代码里处理了，不用留给库去发现）。

也就是《C++ primer》中说的：**将问题检测和问题处理相分离。**

具体可以查看本节的代码展示。
程序抛出异常之后进行处理，然后继续执行剩下的代码。

**注意**：
- try后面可以跟着多条catch语句进行不同异常类型的处理。
- 但是catch语句的参数必须和throw后面的参数类型一致，不然将无法正常捕获程序抛出的异常。
- 如果异常在try语句之前就已经产生，那么后面的try语句块将不在执行，

### 面向对象的异常处理
C++可以在一个类A中定义一个内隐类B，也叫嵌套类，作为类A函数成员出现异常时的输出对象。
此时，可以根据内隐类B保存导致异常的信息。但是内隐类的作用域就是封装它的那个类，一旦超出这个范围，内隐类将失效。

考虑如下程序：
```c++
#include <iostream>
using namespace std;
class Devide
{
private:
    int lower;
    int upper;
    int input;
public:
    //定义内隐类
    class RangeOut
    {
    public:
        int val;
        //构造函数，保存异常时的数据
        RangeOut(int a){val = a;}
    };
    Devide(int l,int u){lower = l;upper = u;}
    //获取用户输入并判断是否超出范围
    int getInput(void)
    {
        cin>>input;
        if(input < lower || input > upper)
            throw RangeOut(input);
        return input;
    }
};

int main()
{
    Devide first(1,10);
    int useVal;
    cout << "pleae input a num range from 1 to 10:" << endl;
    try
    {
        useVal = first.getInput();
        cout<<"the num you input is:"<<useVal<<endl;
    }
    catch(Devide::RangeOut ex)
    {
        //catch内部就是用户处理异常的代码
        cout<<"the num "<<ex.val<<" out of range\n\n";
    }
    cout<<"end of the program\n\n";
    return 0;
}
```
在上面的例子中，一旦用户输入的数据超出既定的范围，函数就会抛出异常，但是这个异常是一个内隐类的对象，并使用导致异常的这个值初始化内隐类。
然后在用户的catch语句中利用刚刚的内隐类输出这个异常值。

**注意**：
- 一旦程序抛出异常，在执行了异常处理程序之后，程序再也不能回到抛出点继续执行，因为c++采用的是不可恢复发异常处理模型。
- 一旦程序抛出异常，执行throw语句的函数也将立即停止执行，如果该函数被别的函数调用，那么调用者也立即停止。
- 如果是对象的函数成员抛出的异常，那么立即对该对象调用析构函数。
- 如果try语句中创建有对象，但是在函数抛出异常时，这些对象还未来得及析构，那么立即调用这些对象的析构函数。

### 异常的再次抛出
```c++
try{
    doSomething();
}catch(exception1){
    //处理异常1的代码
}catch(exception2){
    //处理异常2的代码
}
//该函数不能处理异常1，将再次抛出异常给它的调用者继续执行
void doSomething()
{
    try{
        doElse()
    }catch(exception1){
        throw;  //再次抛出异常
    }catch(exception3){
        //处理异常3的代码
    }
}
```
这种嵌套的try块适合处理内部的异常处理者传递给外部的异常处理者的异常对象。