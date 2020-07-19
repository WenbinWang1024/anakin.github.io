# Linuxday13-进程创建

1. 进程的创建

   1. fork

      ![image-20200707102412778](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200707102412778.png)

      

      

      1. ![image-20200707111704535](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200707111704535.png)

      

      1. 写时复制技术（Copy On Write）

         写时拷贝是一种可以推迟甚至免除拷贝数据的技术。内核此时并不复制整个进程地址空间，而是让父进程和子进程共享同一个拷贝。**只有在需要写入的时候，数据才会被复制**，从而使各个进程拥有各自的拷贝。也就是说，资源的复制只有在需要写入的时候才进行，在此之前，只是以只读方式共享。

      2. 进程对文件的使用。

         1. 代码

            ![image-20200707115310408](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200707115310408.png)

            执行结果 ，子进程先写world到文件中，父进程sleep1秒之后，又去写hello，hello写在world后面。不同的进程可以用不同的文件描述符操作同一个文件对象。

            文件描述符的值，只跟当前进程未使用的最小的文件描述符的值有关，不同进程之间文件描述符各自独立。一切皆文件。
            
            ![image-20200707115354660](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200707115354660.png)

   2. execl

      1. 鸠占鹊巢，移魂大法。
      2. 成功时不返回，失败时返回-1。
      3. ![image-20200707153655388](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200707153655388.png)

   3. system   

      1. 优势：可以嵌套其他语言的可执行程序。
      2. 缺陷：开销比较大。
         1. ![image-20200707153459038](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200707153459038.png)
      3. ![image-20200707153523666](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200707153523666.png)

2. 进程的终止和等待

   1. 父子进程的生命周期，结束的顺序问题。

   2. 孤儿进程：父进程创建子进程之后，父进程退出，此时子进程还没有结束，子进程就会被1号进程接管，子进程的父进程编程1号进程。

   3. 僵尸进程：如果子进程先退出，父进程没有用wait去清理子进程的资源，子进程就是僵尸进程，TASK_ZOMBIE状态。如果系统中僵尸进程比较多，会浪费系统资源，影响性能，需要避免产生僵尸进程。

      ![image-20200707163542886](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200707163542886.png)

      

   4. 进程的终止

      1. main函数的返回

      2. 通过exit函数退出。

      3. 通过_exit函数退出。

         ![image-20200707164844759](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200707164844759.png)

      4. 调用abort函数。

      5. 进程收到信号导致终止，比如CTRL+C，2号信号，CTRL+\3号信号。

   5. 进程的等待

      1. 父进程调用wait等待子进程

         1. 如果子进程没有结束，父进程会挂起，等待子进程结束。

         2. 如果子进程退出，父进程通过wait回收子进程的资源，拿到子进程的退出码，wait函数返回。

            ![image-20200707170212268](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200707170212268.png)

         3. 父进程打印子进程的退出码

            ![image-20200707171124772](C:\Users\82171\AppData\Roaming\Typora\typora-user-images\image-20200707171124772.png)



父子进程的执行顺序：  不一定  
邮件组  子进程  父进程   

linus 
Finally, we've never made any guarantees, 
because the timeslice for the parent might be just about to end, 
so child-first vs parent-first is never a guarantee, it's always just a preference.

我们永远不应该让自己的代码依靠哪个进程先运行，而应该采用更好的进程间通讯机制进行保证