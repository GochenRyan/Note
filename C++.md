## 问题：“...”+“...”+int类型数据会出现问题

---

## stringstream

C++引入了ostringstream、istringstream、stringstream这三个类，要使用他们创建对象就必须包含sstream.h头文件

![20200218211141-image.png](https://raw.githubusercontent.com/GochenRyan/ImgHost/master/img/20200218211141-image.png)

1. istringstream是由一个string对象构造而来，istringstream类从一个string对象读取字符

2. stringstream同样是由一个string对象构造而来，ostringstream类向一个string插入字符

3. stringstream用于C++风格的字符串的输入输出，**常用于string与各种内置数据类型的转换**

### stringstream用于空格分割的字符串的切分

 [557. 反转字符串中的单词 III](https://leetcode-cn.com/problems/reverse-words-in-a-string-iii/)

```cpp
class Solution {
public:
    string reverseWords(string s) {
        //使用C++的stringstream类和，string的reverse函数
        if(s.size() == 0)
            return "";
        stringstream ss;
        ss << s;
        string p,str;
        while(ss >> p){
            reverse(p.begin(), p.end()); //string 可以调用reverse函数,string的表现像一个vecor<char>
            str = str + p + ' ';
        }
        string ans(str.begin(), str.begin()+str.size()-1);
        return ans;
    }
};
```

---

## c_str()

[https://www.cnblogs.com/bxynlbyx/p/11756140.html](https://www.cnblogs.com/bxynlbyx/p/11756140.html)

C++中的这个函数是为了与c语言兼容，c中无string类型，故必须通过string类对象的成员函数c_str()把string对象转换成c中的字符串样式。

```cpp
#include<iostream>
#include<cstring>
using namespace std;
int main(){
    const char* c;
    string s="1234";
    c=s.c_str();//直接在指针上操作
    cout<<c<<endl;
    s="abcd";
    cout<<c<<endl;
    return 0;
}
```

结果：1234  abcd

这样通过c指针进行操作，会是字符串发生改变。

```cpp
#include<iostream>
#include<cstring>
using namespace std;
int main(){
    char *c=new char[20];
    string s="1234";
    strcpy(c,s.c_str());//通过strcpy()函数  头文件#include<string.h>c里面的一个字符串复制的函数 返回char*
    cout<<c<<endl;
    s="abcd";
    cout<<c<<endl;
    return 0;
}
```

结果：1234   1234

通过strcpy()函数进行复制，之后的操作并不会改变字符串

c_str()返回的是一个**临时指针**，不能对其进行操作，不需要手动释放或删除。

---

## srand()、rand()

**srand()** 用来设置 rand() 产生随机数时的随机数种子。参数 seed 必须是个整数，如果每次 seed 都设相同值，rand() 所产生的随机数值每次就会一样。

**rand()** 不需要参数，它会返回一个从0到最大随机数的任意整数，最大随机数的大小通常是固定的一个大整数。

一般性：`rand() % (b-a+1)+ a  `   就表示  a~b 之间的一个随机整数。

---

## 函数参数的默认值

[https://blog.csdn.net/YL970302/article/details/83686117](https://blog.csdn.net/YL970302/article/details/83686117)

1. 调用时实参与形参的结合次序是从左往右

2. 如果一个函数有原型声明，且原型声明在定义之前，则默认参数值应在函数原型声明中给出

3. 如果只有函数的定义或函数定义在前，则默认参数值可以在函数定义中给出
