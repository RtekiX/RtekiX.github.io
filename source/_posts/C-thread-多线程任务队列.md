---
title: C++ thread 多线程任务队列
header_image: /intro/post-bg.jpg
abstract: 本来想写线程池，但提交函数指针和参数有点复杂
categories: 学习
tags:
  - C++练习
abbrlink: f553d691
date: 2024-02-14 20:26:39
---

```c++
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <queue>

using namespace std;

class Semaphore
{
public:
    Semaphore(int value = 1) : cnt(value) {}
    void P()
    {
        unique_lock<mutex> lck(mtx);
        if (--cnt < 0)
            cv.wait(lck);
    }
    void V()
    {
        unique_lock<mutex> lck(mtx);
        if (++cnt <= 0)
            cv.notify_one();
    }

private:
    int cnt;
    mutex mtx;
    condition_variable cv;
};

class TaskQueue
{
public:
    TaskQueue(int n)
    {
        srand(time(0));
        max_size = n;
        notfull = new Semaphore(n);
        notempty = new Semaphore(0);
        rwlock = new Semaphore(1);
    }

    void add(int e)
    {
        notfull->P(); // 确保有空位
        rwlock->P();  // 获取互斥锁
        q.push(e);
        cout << "线程id " << std::this_thread::get_id() << " 执行了add操作，添加数字" << e << endl;
        rwlock->V();
        notempty->V();
    }

    int get()
    {
        notempty->P();
        rwlock->P();
        int e = q.front();
        q.pop();
        cout << "线程id " << std::this_thread::get_id() << " 执行了get操作，获取数字" << e << endl;
        rwlock->V();
        notfull->V();
        return e;
    }


private:
    int max_size;
    Semaphore* notfull, * notempty;
    Semaphore* rwlock; // 生产者和消费者的互斥锁
    queue<int> q;
};

void fadd(TaskQueue& task_queue)
{
    int cnt = 0;
    while (cnt < 10)
    {
        int rnd = rand() % 100;
        task_queue.add(rnd);
        cnt++;
    }
}

void fget(TaskQueue& task_queue)
{
    int cnt = 0;
    while (cnt < 10)
    {

        int x = task_queue.get();
        cnt++;
    }
}

int main()
{
    TaskQueue task_queue(50);
    thread tt1(fadd, ref(task_queue));
    thread tt2(fget, ref(task_queue));
    tt1.join();
    tt2.join();
    return 0;
}
```

用future替代thread，future

```c++

int main()
{
    TaskQueue task_queue(50);
    vector<future<void>> ts;
    for (int i = 0; i < 10; i++) {
        if (i % 2 == 0) {
            future<void> t = std::async(fadd, ref(task_queue));
            ts.emplace_back(std::move(t));
        }
        else {
            future<void> t = std::async(fget, ref(task_queue));
            ts.emplace_back(std::move(t));
        }
    }
    return 0;
}
```