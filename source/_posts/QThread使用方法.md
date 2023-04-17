---
title: QThread使用方法
tags:
  - qt
categories:
  - qt
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-04-13 07:12:20/QThread使用方法.html
mathJax: false
date: 2023-04-13 15:12:20
thumbnail:
---
实例化`QThread`这个类会开辟一个新线程，默认运行`run()`，还可以使用`QObject::moveToThread`方法将任务对象移动至新线程，此时该任务对象的槽函数将运行在新线程中，可以自由的建立相关信号连接，默认是以`Qt::QueuedConnection`方式，是线程安全的。

这样，我们可以有几种方式来创建线程完成工作：

1. 任务对象继承`QThread`，重写`run()`方法；实例化对象后会自动运行`run()`函数工作

2. 任务对象继承`QObject`，将工作任务实现在这个类的槽中；

   创建`QThead`，自定义类调用`QObject::moveToThread`方法移动至新开辟的线程；

   绑定该任务对象的槽，任意信号触发时，将在新线程中执行槽函数；

<!-- more -->

##### **example**

**方法1：**

```cpp
 class WorkerThread : public QThread
 {
     Q_OBJECT
     void run() override {
         QString result;
         /* ... here is the expensive or blocking operation ... */
         emit resultReady(result);
     }
 signals:
     void resultReady(const QString &s);
 };

 void MyObject::startWorkInAThread()
 {
     WorkerThread *workerThread = new WorkerThread(this);
     connect(workerThread, &WorkerThread::resultReady, this, &MyObject::handleResults);
     connect(workerThread, &WorkerThread::finished, workerThread, &QObject::deleteLater);
     workerThread->start();
 }
```

**方法2：**

```cpp
 class Worker : public QObject
 {
     Q_OBJECT

 public slots:
     void doWork(const QString &parameter) {
         QString result;
         /* ... here is the expensive or blocking operation ... */
         emit resultReady(result);
     }

 signals:
     void resultReady(const QString &result);
 };

 class Controller : public QObject
 {
     Q_OBJECT
     QThread workerThread;
 public:
     Controller() {
         Worker *worker = new Worker;
         worker->moveToThread(&workerThread);
         connect(&workerThread, &QThread::finished, worker, &QObject::deleteLater);
         connect(this, &Controller::operate, worker, &Worker::doWork);
         connect(worker, &Worker::resultReady, this, &Controller::handleResults);
         workerThread.start();
     }
     ~Controller() {
         workerThread.quit();
         workerThread.wait();
     }
 public slots:
     void handleResults(const QString &);
 signals:
     void operate(const QString &);
 };
```

