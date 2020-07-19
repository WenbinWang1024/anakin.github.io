# Linux线程

## 线程间的关系

1. 查看系统中线程的命令  ps -elLf

2. 进程间关系

   ![image-20200716100719075](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200716100719075.png)

3. 线程间的关系

   ![image-20200716100738197](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200716100738197.png)

## 线程的创建，退出和等待 

1. 线程的等待

   1. 一个线程退出时，其他线程可以通过pthread_join等待退出，同时拿到线程的退出码。

      ![image-20200716100134070](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200716100134070.png)

## 线程的取消和资源清理

1. 线程的取消，一个线程可以给其他线程发送cancel信号，收到cancel信号的线程可以按照下面的方式处理cancel信号。

   1. 忽略
   2. 立刻终止
   3. 运行到取消点再终止（默认）。

2. pthread_cancel（pthread_t thid）函数用来想目标线程发送cancel信号。发送信号之后立刻返回，不会阻塞。

   ![image-20200716103024587](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200716103024587.png)

3. 线程的资源清理函数

   1. 注册清理函数pthread_cleanup_push,必须和pthread_cleanup_pop配对使用。

      ![image-20200716114743147](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200716114743147.png)

   2. 清理函数的使用

      ![image-20200716114607972](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200716114607972.png)

   3. 清理函数得到执行的3种情况。

      1. 线程被cancel的时候，执行清理函数。
      2. 线程通过pthread_exit退出，清理函数也会得到执行。
      3. 显示调用pthread_cleanup_pop(1)，要求清理函数弹栈，执行。

   4. 清理函数不会得到执行的2种情况。

      1. 显示调用pthread_cleanup_pop(0)，清理函数弹栈，不执行。
      2. 线程通过return 的方式退出，不会执行清理函数。

   5. 清理函数采用先入后出的栈结构管理。

      ![image-20200716121246287](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200716121246287.png)

   

## 线程的同步和互斥

1. 线程的互斥

   1. 多个线程要访问临界资源时，需要保证线程对临界资源的互斥访问，不然会有冲突。
      1. 加锁，如果锁是解开的，加锁成功，可以访问临界资源；如果锁是锁住的，加锁不成功，代表有其他线程正在访问临界资源，当前线程就不能再去访问临界资源，线程会阻塞在加锁这里，要等正在访问临界区的线程解锁后，当前线程才能加锁成功，才能去访问临界资源。
      2. 加锁成功，访问临界资源。
      3. 临界资源访问完毕，解锁，让出临界资源，其他线程才能再去访问临界资源。
   2. mutex互斥体来实现对共享资源的互斥访问。
   3. mutex有两个状态：锁住的状态和未锁住的状态。锁初始化时是未锁住的状态。

2. 锁的使用

   1. 锁的初始化和销毁

      pthread_mutex_t mutex;
      //对锁进行初始化
      pthread_mutex_init(&mutex,NULL);

      //锁的销毁                                                        
      pthread_mutex_destroy(&mutex);

      ![image-20200716151320631](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200716151320631.png)

   2. 锁的操作

      1. 加锁:  pthread_mutex_lock(&mutex);如果对已经锁住的锁再去加锁，线程会阻塞。

      2. 解锁： pthread_mutex_unlock(&mutex);

      3. 尝试加锁：pthread_mutex_trylock(&mutex);

         1. 如果锁是锁住的，trylock加锁不成功，返回错误码，不会挂起等待（阻塞）。

            ![image-20200716151016950](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200716151016950.png)

         2. 如果锁是解开的，trylock的行为跟pthread_mutex_lock一样，加锁成功。

      4. 通过线程清理函数保证锁的资源能够被释放。

         ![image-20200716162719707](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200716162719707.png)
      
   3. 线程的同步
   
      1. 条件变量：是线程间进行同步的一种机制，往往要结合互斥锁一起使用。
   
      2. 条件的使用
   
         1. 初始化和销毁
   
         2. 等待和激发
   
            1. 等待：
   
               1. 无条件等待：pthread_cond_wait(&cond,&mutex);
   
                  ![image-20200717103358854](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200717103358854.png)
   
               2. 计时等待：pthread_cond_timedwait(&cond,&mutex,&abstime);
   
                  ![image-20200717111407073](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200717111407073.png)
   
            2. 激发
   
               1. 激活条件变量上等待的第一个线程。pthread_cond_signal(&cond);
   
               2. 激活条件变量上等待的所有线程。pthread_cond_broadcast(&cond);
   
                  ![image-20200717111921055](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200717111921055.png)
   
            3. pthread_cond_wait(）函数的上半部和下半部
   
               1. 上半部
                  1. **解锁**
                  2. 排队
                  3. 睡眠，等待资源就绪，条件变量被激发。
               2. 下班部
                  1. 资源就绪时，条件变量被激发，线程醒来。
                  2. 要访问资源，**加锁**，加锁不成功时（有其他线程占有了锁），线程继续阻塞，加锁成功，直接返回。
                  3. 从pthread_cond_wait函数返回。
   
            4. 等待在条件变量上的线程被唤醒之后，不代表会立刻返回，需要加锁成功，才能返回。
   
               ![image-20200717114612961](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200717114612961.png)



## 线程安全和线程的属性

1. 线程安全

   1. 线程安全函数：
   2. ![image-20200717152137423](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200717152137423.png)

   ![image-20200717152102789](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200717152102789.png)