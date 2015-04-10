---
layout: post
title: "简单的进程间Mutex和读写锁类实现"
---
#ShareLack.h

```c++
    #ifndef _SHARE_LOCK_H_
    #define _SHARE_LOCK_H_
    //进程间mutex,在父进程定义，fork之后加锁解锁对父子进程间有效
    class ShareMutex {
     public:
      ShareMutex::ShareMutex();
      ShareMutex::~ShareMutex();
      void Lock();
      void UnLock();
     private:
      pthread_mutex_t* m_pMutex;
    };

    //进程间读写锁,fork之后加锁解锁对父子进程间有效
    class ShareRwlock {
     public:
      ShareRwlock::ShareRwlock();
      ShareRwlock::~ShareRwlock();
      void WrLock();
      void RdLock();
      void UnLock();
     private:
      pthread_rwlock_t* m_pRwlock;
    };
    #endif
```
<!--more-->

#ShareLock.cpp

```c++
    #include "ShareMutex.h"

    ShareMutex::ShareMutex() {
      m_pMutex = (pthread_mutex_t *)mmap(NULL, sizeof(pthread_mutex_t), PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS, -1, 0);
      pthread_mutexattr_t attr_perf;
      pthread_mutexattr_init(&attr_perf));
      pthread_mutexattr_setpshared(&attr_perf, PTHREAD_PROCESS_SHARED);
      pthread_mutex_init(g_perf_mutex, &attr_perf);
    }

    ShareMutex::~ShareMutex() {
      m_pRwLocked_mutex_destroy(m_pMutex);
      munmap(m_pMutex, sizeof(pthread_mutex_t));
    }


    void ShareMutex::Lock() {
      if(0 != pthread_mutex_lock(m_pMutex)) {
        throw TCException("ShareMutex::Lock() exception!");
      }
    }


    void ShareMutex::UnLock() {
      if(0 != pthread_mutex_unlock(m_pMutex)) {
        throw TCException("ShareMutex::UnLock() exception!");
      }
    }

    ShareRwlock::ShareRwlock() {
      m_pRwlock = (pthread_rwlock_t *)mmap(NULL, sizeof(pthread_rwlock_t), PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS, -1, 0);
      pthread_rwlockattr_t attr_pub;
      pthread_rwlockattr_init( &attr_pub );
      pthread_rwlockattr_setpshared( &attr_pub, PTHREAD_PROCESS_SHARED );
      pthread_rwlock_init( m_pRwLock, &attr_pub );
    }


    ShareRwlock::~ShareRwlock() {
      pthread_rwlock_destroy(m_pRwlock);
      munmap(m_pRwlock, sizeof(pthread_rwlock_t));
    }


    void ShareRwlock::WrLock() {
      if(0 != pthread_rwlock_wrlock( m_pRwLock )) {
        throw TCException("ShareRwlock::WrLock() exception!");
      }
    }

    void ShareRwlock::RdLock() {
      if(0 != pthread_rwlock_rdlock( m_pRwLock )) {
        throw TCException("ShareRwlock::RdLock() exception!");
      }
    }

    void ShareRwlock::UnLock() {
      if(0 != pthread_rwlock_unlock( m_pRwLock )) {
        throw TCException("ShareRwlock::UnLock() exception!");
      }
    }
```
