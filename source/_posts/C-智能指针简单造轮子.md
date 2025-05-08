---
title: C++智能指针简单造轮子
header_image: /intro/post-bg.jpg
abstract: 只实现了加减计数
categories: 学习
tags:
  - C++
abbrlink: 39e4cbdd
date: 2024-03-12 17:11:32
---
```c++
#include <iostream>

using namespace std;

class Count
{
public:
    Count(int cnt = 1) : _count(cnt){};
    int getCount()
    {
        return _count;
    }
    int add()
    {
        _count++;
        return _count;
    }
    int minus()
    {
        _count--;
        return _count;
    }

private:
    int _count;
};

template <typename T>
class SharedPtr
{
public:
    SharedPtr(T *ptr = nullptr)
    {
        _ptr = ptr;
        if (ptr != nullptr)
        {
            _cnt = new Count(1);
        }
    }
    ~SharedPtr()
    {
        release();
    }
    SharedPtr(const SharedPtr &other)
    {
        _ptr = other._ptr;
        _cnt = other._cnt;
        if (_cnt->getCount() != 0)
        {
            _cnt->add();
        }
    }
    SharedPtr &operator=(const SharedPtr &other)
    {
        if (this != other)
        {
            release();
            _ptr = other._ptr;
            _cnt = other._ptr;
            _cnt->add();
        }
        return *this;
    }
    T operator*()
    {
        return *_ptr;
    }
    T &operator->()
    {
        return _ptr;
    }
    int use_count()
    {
        return _cnt->getCount();
    }

private:
    void release()
    {
        if (_ptr != nullptr && _cnt->minus() == 0)
        {
            delete _ptr;
            delete _cnt;
        }
    }
    T *_ptr;
    Count *_cnt;
};

int main()
{
    SharedPtr<int> p(new int(5));
    cout << *p << endl
         << p.use_count();
    {
        SharedPtr<int> q(p);
        cout << *q << endl
             << q.use_count();
    }
    cout << *p << endl
         << p.use_count();
}
```
