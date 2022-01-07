# C++笔记

## 前言

将以前写的笔记逐步转移到博客上

## 记录

### 21.9.20(1) 编译器提示未定义标识符nullptr

错误： 'nullptr' was not declared in this scope。
解决办法：修改c++版本为c++11或以上。
查看不同版本的c++特性可以看这个网站：<https://en.cppreference.com/w/cpp/compiler_support>，当然最直接的方法还是百度。

### 21.9.20(2) C++实现四则运算

大话数据结构的实现方法：通过栈将中缀表达式转换为后缀表达式，转换为后缀表达式后，在通过栈就很容易求得结果。
难点是如何将中缀表达式转换为后缀表达式，主要步骤：遍历前缀表达式，如果是数字直接输出到后缀表达式中，如果是运算符号则要判断栈顶的运算先级是否**大于等于**当前运算符 ，若是的话，则要将栈中的元素逐个出栈，直到栈顶运算符优先级小于当前运算符，随后在将当前运算符入栈，若遇到左括号也直接入栈，但遇到右括号的话则将栈中元素逐个出栈，直到遇到栈中的左括号为止。
原来大话数据结构提到的示例里是只能求整数，且只有+-*/四种运算，先将其扩充，可以输入小数并且增加了幂运算。
程序如下：

```c
#include <iostream>
#include <stack>
#include <string>
#include <cstring>
#include <iomanip>
#include <cmath>

using namespace std;

class calculate
{
public:
    int result(string &str, double &res);

private:
    int priority(char c);
    int change(string &dst, string &src);
};
//求取结果
int calculate::result(string &str, double &res)
{
    if (!str.length())
        return -1;

    string str2;
    if (change(str2, str))
        return -1;
    cout << str2 << endl;

    size_t len = str2.length();
    const char *p = str2.c_str();
    stack<double> s;
    while (len--)
    {
        if (*p >= '0' && *p <= '9')
        {
            double temp = atof(p);
            len -= strlen(p);
            p += strlen(p);
            s.push(temp);
        }
        else
        {
            double input2 = s.top();
            s.pop();
            double input1 = s.top();
            s.pop();
            double temp;
            if (*p == '+')
                temp = input1 + input2;
            else if (*p == '-')
                temp = input1 - input2;
            else if (*p == '*')
                temp = input1 * input2;
            else if (*p == '/')
            {
                if (fabs(input2) < 1e-10) //判断除数是否为0
                    return -1;
                temp = input1 / input2;
            }
            else if (*p == '^')
                temp = pow(input1, input2);
            s.push(temp);
        }
        p++;
    }
    res = s.top();
    return 0;
}

int calculate::priority(char c)
{
    switch (c)
    {
    case '+':
        return 1;
    case '-':
        return 1;
    case '*':
        return 2;
    case '/':
        return 2;
    case '^':
        return 3;
    default:
        return 0;
    }
}
//将中缀表达式转换为后缀表达式
int calculate::change(string &dst, string &src)
{
    stack<char> s;
    for (string::iterator it = src.begin(); it != src.end(); it++)
    {
        if (*it >= '0' && *it <= '9')
        {
            bool dot_found = false;
            while (1)
            {
                if (*it >= '0' && *it <= '9')
                    dst += *it++;
                else if (*it == '.')
                {
                    if (dot_found)
                        return -1;
                    dst += *it++;
                    dot_found = true;
                }
                else
                {
                    dst += '\0'; //将数字隔开
                    break;
                }
            }
            if (it == src.end())
                break;
        }
        if (*it == '+' || *it == '-' || *it == '*' || *it == '/' || *it == '^')
        {
            while (!s.empty() && priority(s.top()) >= priority(*it))
            {
                dst += s.top();
                s.pop();
            }
            s.push(*it);
        }
        else if (*it == '(')
            s.push(*it);
        else if (*it == ')')
        {
            while (1)
            {
                if (s.empty())
                    return -1;
                else if (s.top() != '(')
                {
                    dst += s.top();
                    s.pop();
                }
                else
                {
                    s.pop();
                    break;
                }
            }
        }
    }
    while (!s.empty())
    
        dst += s.top();
        s.pop();
    }

    return 0;
}

int main(int argc, char **argv)
{
    class calculate cal;
    string str;
    double res;

    cin >> str;
    if (cal.result(str, res))
        cout << "input error" << endl;
    cout << "result:" << res << endl;

    return 0;
}
```

运行结果如图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/f4269b6bdaa941f18cef1c209808008e.PNG#pic_center)

### 21.9.25 KMP算法

大话数据结构中对kmp算法讲的不是很清楚，之后又看了一些其他资料算是明白了，资料链接为<http://data.biancheng.net/view/180.html>(说来奇怪，这个网站关于kmp算法这部分是收费的，但是如果是通过链接进去的就可以直接看，也是够牛逼的)。

以下为kmp匹配示例

```c
void kmp_next(const string &str, int *next)
{
    next[0] = -1;

    int i = 0, j = -1;
    while (i < str.length() - 1)
    {
        if (j < 0 || str[i] == str[j])
        {
            i++;
            j++;
            if (str[i] != str[j])
                next[i] = j;
            else
                next[i] = next[j];
        }
        else
            j = next[j];
    }
}

int kmp_match(const string &s, const string &t)
{
    if (!s.length() || !t.length())
        return -1;

    int *next = new int[t.length()];
    kmp_next(t, next);
    int i = 0;
    int j = 0;
    while (i < s.length() && j < (int)t.length())
    {
        if (j < 0 || s[i] == t[j])
        {
            i++;
            j++;
        }
        else
            j = next[j];
    }
    delete next;
    if (j == t.length())
        return i - t.length();
    else
        return -1;
}
```

kmp算法有很多让人困惑的地方，首先字符串匹配时，会有一个主串索引i，一个字串索引j，使用正常匹配算法，当匹配失败时，i和j都会回退。

### 21.11.27 template \<class T> 是什么东西

参考资料: <http://prglab.com/cms/pages/c-tutorial/advanced-concepts/templates.php>

template(模板)为c++中引入的概念，可以生成通用的函数，这些函数能够接受任意数据类型的参数，或者返回任意数据类型的值，一定程度上实现类似宏的作用。
使用格式为

```c
template <class Type>
```

### 21.11.28 二叉树

BinSearchTree.h

```c
#ifndef __BINSEARCHTREE_H__
#define __BINSEARCHTREE_H__

#include "iomanip"
#include <iostream>

using namespace std;

template <class T>
class BinSearchTreeNode
{
public:
    BinSearchTreeNode(T value);
    BinSearchTreeNode *left;
    BinSearchTreeNode *right;
    BinSearchTreeNode *parent;
    T value;
};

template <class T>
class BinSearchTree
{
public:
    BinSearchTree();
    ~BinSearchTree();
    void preOrder() const;
    void inOrder() const;
    void postOrder() const;
    BinSearchTreeNode<T> *search(int value) const;
    T max() const;
    T min() const;
    void insert(T value);
    void remove(int value);

private:
    class BinSearchTreeNode<T> *root;
    void preOrder(BinSearchTreeNode<T> *node) const;
    void inOrder(BinSearchTreeNode<T> *node) const;
    void postOrder(BinSearchTreeNode<T> *node) const;
    BinSearchTreeNode<T> *search(BinSearchTreeNode<T> *node, int value) const;
    BinSearchTreeNode<T> *max(BinSearchTreeNode<T> *node) const;
    BinSearchTreeNode<T> *min(BinSearchTreeNode<T> *node) const;
    void destroy(BinSearchTreeNode<T> *node);
};

template <class T>
BinSearchTreeNode<T>::BinSearchTreeNode(T value) : left(nullptr), right(nullptr), parent(nullptr), value(value)
{
}

template <class T>
BinSearchTree<T>::BinSearchTree() : root(nullptr)
{
}

template <class T>
BinSearchTree<T>::~BinSearchTree()
{
    destroy(root);
}

template <class T>
void BinSearchTree<T>::preOrder(BinSearchTreeNode<T> *node) const
{
    if (node)
    {
        cout << node->value << endl;
        preOrder(node->left);
        preOrder(node->right);
    }
}

template <class T>
void BinSearchTree<T>::inOrder(BinSearchTreeNode<T> *node) const
{
    if (node)
    {
        inOrder(node->left);
        cout << node->value << endl;
        inOrder(node->right);
    }
}

template <class T>
void BinSearchTree<T>::postOrder(BinSearchTreeNode<T> *node) const
{
    if (node)
    {
        inOrder(node->left);
        inOrder(node->right);
        cout << node->value << endl;
    }
}

template <class T>
BinSearchTreeNode<T> *BinSearchTree<T>::search(BinSearchTreeNode<T> *node, int value) const
{
    if (node)
    {
        if (node->value < value)
            return search(node->left, value);
        else if (node->value > value)
            return search(node->right, value);
        else
            return node;
    }
    else
        return nullptr;
}

template <class T>
BinSearchTreeNode<T> *BinSearchTree<T>::max(BinSearchTreeNode<T> *node) const
{
    if (node->left)
        return max(node->left);
    else
        return node;
}

template <class T>
BinSearchTreeNode<T> *BinSearchTree<T>::min(BinSearchTreeNode<T> *node) const
{
    if (node->right)
        return min(node->right);
    else
        return node;
}

template <class T>
void BinSearchTree<T>::destroy(BinSearchTreeNode<T> *node)
{
    if (node)
    {
        destroy(node->left);
        destroy(node->right);
        delete node;
    }
}

template <class T>
void BinSearchTree<T>::preOrder() const
{
    preOrder(root);
}

template <class T>
void BinSearchTree<T>::inOrder() const
{
    inOrder(root);
}

template <class T>
void BinSearchTree<T>::postOrder() const
{
    postOrder(root);
}

template <class T>
BinSearchTreeNode<T> *BinSearchTree<T>::search(int value) const
{
    return search(root, value);
}

template <class T>
T BinSearchTree<T>::max() const
{
    if (root)
        return max(root)->value;
    else
        return (T)NULL;
}

template <class T>
T BinSearchTree<T>::min() const
{
    if (root)
        return min(root)->value;
    else
        return (T)NULL;
}

template <class T>
void BinSearchTree<T>::insert(T value)
{
    BinSearchTreeNode<T> *new_node = new BinSearchTreeNode<T>(value);
    BinSearchTreeNode<T> *parent = nullptr;
    BinSearchTreeNode<T> *node = root;

    if (new_node == nullptr)
        return;

    while (node)
    {
        parent = node;
        if (node->value < value)
            node = node->left;
        else if (node->value > value)
            node = node->right;
        else
        {
            delete new_node;
            return;
        }
    }
    new_node->parent = parent;
    if (parent == nullptr)
        root = new_node;
    else if (parent->value < value)
        parent->left = new_node;
    else
        parent->right = new_node;
}

template <class T>
void BinSearchTree<T>::remove(int value)
{
    BinSearchTreeNode<T> *node = search(value);
    BinSearchTreeNode<T> *del_node;
    BinSearchTreeNode<T> *del_child;
    if (node)
    {
        if (node->left && node->right)
            del_node = min(node->left);
        else
            del_node = node;

        if (del_node->left)
            del_child = del_node->left;
        else
            del_child = del_node->right;

        if (del_child)
            del_child->parent = del_node->parent;

        if (del_node->parent == nullptr)
            root = del_child;
        if (del_node->parent->left == del_node)
            del_node->parent->left = del_child;
        else
            del_node->parent->right = del_child;

        if (del_node != node)
            node->value = del_node->value;

        delete del_node;
    }
}

#endif
```
