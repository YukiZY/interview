###线程同步与互斥定义:
>
  * 线程同步是指线程之间所具有的一种制约关系，一个线程的执行依赖另一个线程的消息，当它没得到另一个线程的消息时应等待，直到消息到达时才被唤。   
>
  * 互斥是指对于共享的进程系统资源，在各单个线程访问时的排它性。当有若干个线程都要使用某一共享资源时，任何时刻最多只允许一个线程去使用，其它要使用该资源的线程必须等待，直到占用资源者释放该资源。线程互斥可以看成是一种特殊的线程同步
  
[不同操作系统和语言的同步方式差异](http://blog.2baxb.me/archives/407)

###1.[线程同步方式](http://blog.csdn.net/iw1210/article/details/8509629)
* 互斥锁mutex:
锁机制是同一时刻只允许一个线程执行一个关键部分的代码。
 * 初始化锁：pthread_mutex_init(锁，锁属性[普通锁、嵌套锁、检测锁、适应锁]);
 * 阻塞加锁/非阻塞加锁：pthread_mutex_lock()/pthread_mutex_trylock(),非阻塞锁在锁被占用时返回EBUSY,而不是挂起等待
 * 解锁：pthread_mutex_unlock();要求锁是lock状态，并且只能由加锁线程解锁
 * 销毁：pthread_mutex_destroy();此时锁必须是unlock状态，否则返回EBUSY;
 
* 条件变量cond：
条件变量是利用线程间共享全局变量进行同步的一种机制，条件变量操作：触发条件(当条件边为true),等待条件，挂起线程直到其他条件触发；
 * 初始化条件变量: pthread_cond_init();
 * 等待条件：
   * 无条件等待：pthread_cond_wait();
    * 计时等待：pthread_Cond-timewait(); 如果在给定时刻前条件没有满足，则返回ETIMEOUT，结束等待
 * 激发条件：
   * 激活一个等待该条件的线程（存在多个等待线程时按入队顺序激活其中一个）：pthread_cond_signal()
    * 激活所有等待线程:pthread_cond_broadcast()
 * 销毁条件变量：只有在没有线程在该条件变量上等待的时候才能销毁这个条件变量，否则返回EBUSY,  pthread_cond_destroy().

* 信号量：
如同进程一样，线程也可以通过信号量来实现通信，虽然是轻量级的。
 * 初始化信号量：sem_init()
 * 信号量加1：sem_post();
 * 信号量减1：sem_wait();
 * 信号量销毁：sem_destroy();

###2.PV操作的含义：
* PV操作由P操作原语和V操作原语组成（原语是不可中断的过程），对信号量进行操作，具体定义如下：
 * P（S）：
  * ①将信号量S的值减1，即S=S-1；
  * ②如果S<=0，则该进程继续执行；否则该进程置为等待状态，排入等待队列。
 * V（S）：
  * ①将信号量S的值加1，即S=S+1；
  * ②如果S>0，则该进程继续执行；否则释放队列中第一个等待信号量的进程。
* 使用PV操作实现进程互斥时应该注意的是：
 *（1）每个程序中用户实现互斥的P、V操作必须成对出现，先做P操作，进临界区，后做V操作，出临界区。若有多个分支，要认真检查其成对性。
 *（2）P、V操作应分别紧靠临界区的头尾部，临界区的代码应尽可能短，不能有死循环。
 *（3）互斥信号量的初值一般为1。
  
  
###3.信号量与互斥锁
* 信号量用在多线程多任务同步，一个线程完成了某一个动作就通过信号量告诉别的线程，别的线程再进行某些动作（大家都在
  semtake的时候，就阻塞在那里）

* 而互斥锁是用在多线程多任务互斥的，一个线程占用了某一个资源，那么别的线程就无法访问，直到这个线程unlock，其他的线程
才开始可以利用这个资源,比如对全局变量的访问，有时要加锁，操作完了，在解锁。有的时候锁和信号量会同时使用的。

* 也就是说，信号量不一定是锁定某一个资源，而是流程上的概念

>
 比如：有A,B两个线程，B线程要等A线程完成某一任务后再进行自己下面的步骤，这个任务并不一定是锁定某一资源，还可以
 是进行一些计算或者数据处理之类
 而线程互斥量则是“锁住某一资源”的概念，在锁定期间内，其他线程无法对被保护的数据进行操作。在有些情况下两者可以互换

* 互斥量和信号量的区别
 * 1. 互斥量用于线程的互斥，信号量用于线程的同步。这是互斥量和信号量的根本区别，也就是互斥和同步之间的区别。
    
  >
   互斥：是指某一资源同时只允许一个访问者对其进行访问，具有唯一性和排它性。但互斥无法限制访问者对资源的访问顺序，
      即访问是无序的。   
   同步：是指在互斥的基础上（大多数情况），通过其它机制实现访问者对资源的有序访问。
      在大多数情况下，同步已经实现了互斥，特别是所有写入资源的情况必定是互斥的。少数情况是指可以允许多个访问者同时访问资源

 * 2.互斥量值只能为0/1，信号量值可以为非负整数。
  也就是说，一个互斥量只能用于一个资源的互斥访问，它不能实现多个资源的多线程互斥问题。
  信号量可以实现多个同类资源的多线程互斥和同步。当信号量为单值信号量是，也可以完成一个资源的互斥访问。

 * 3.互斥量的加锁和解锁必须由同一线程分别对应使用，信号量可以由一个线程释放，另一个线程得到。


###4.死锁、检测、解除：
  * __死锁4条件__：
   * a.__互斥条件__：  
      进程对所分配资源有排他性，即在一定的时间段内某资源只能被一个进程独享，其他进程申请占有该资源，只能等待
   * b.__不可剥夺条件__：  
      即一个进程获取某资源之后，在没有主动释放之前，其他进程不能强制剥夺该资源， 只能有当前占有该资源的进程主动释放才可以
   * c.__请求与保持条件__：  
      即一个进程已经获得了某个资源，此时又去申请另外一个资源，待申请资源又被其他进程所占有且没有释放，
      此时请求进程被阻塞但是又没有释放自身占有的资源
   * d.__循环等待条件__：  
      存在一个进程资源的循环等待链，链中每一个进程已经获得的资源又被链中下一个进程请求
   
  * __死锁预防__：
   * a.__破坏互斥条件__：  
      允许资源都可以共享使用，但是有些资源根本不能同时访问，比如打印机等临界资源只能互斥访问，因此不太行
   * b.__破换不可剥夺条件__：  
      当一个已经保持了某些不可剥夺资源的进程，在请求新的资源而不可满足时，就必须释放已经占有的资源，
      待以后需要时重新申请，但是实现比较复杂，且会导致前一阶段的工作的失效，反复申请和释放也会增加系统调度开销
   * c.__破坏请求与保持条件__：  
      预先分配资源，进程执行之前先预先申请所有资源，只有所以资源都申请ok之后才开始执行，
      执行过程中不再申请资源，实现简单，但是会造系统资源的浪费，因为某些资源可能只有在进程开始或者结束时才使用，一直占有就会导致浪费
   * d.__破坏循环等待条件__：  
      给系统资源编号，只要进程提出申请分配资源Ri，则该进程在以后的资源申请中，只能申请编号大于Ri的资源，
      但是增加编程麻烦
       
  * __死锁避免__：不是破坏死锁的4条件，而是在资源分配的过程中动态检测
   * __系统安全状态__：

   > 是指系统能按某种进程推进顺序( P1, P2, ...,Pn)，为每个进程Pi分配其所需资源，直至满足每个进程对资源的最大需求，
     使每个进程都可顺序地完成。此时称 P1, P2, ..., Pn 为安全序列。如果系统无法找到一个安全序列，则称系统处于不安全状态。
     
   * __银行家算法__：
   
    > 银行家算法是最著名的死锁避免算法。它提出的思想是：把操作系统看做是银行家，操作系统管理的资源相当于银行家管理的资金，
      进程向操作系统请求分配资源相当于用户向银行家贷款。操作系统按照银行家制定的规则为进程分配资源，当进程首次申请资源时，
      要测试该进程对资源的最大需求量，如果系统现存的资源可以满足它的最大需求量则按当前的申请量分配资源，否则就推迟分配。
      当进程在执行中继续申请资源时，先测试该进程已占用的资源数与本次申请的资源数之和是否超过了该进程对资源的最大需求量。
      若超过则拒绝分配资源，若没有超过则再测试系统现存的资源能否满足该进程尚需的最大资源量，若能满足则按当前的申请量分配资源，
      否则也要推迟分配。
       
  * __死锁解除__：
   *  __资源剥夺法__：挂起某些死锁进程并剥夺他的资源给其他等待资源的进程
   *  __撤销进程法__：强制撤销部分或者全部死锁进程，并剥夺这些进程资源
   *  __进程回退法__：让多个进程回退到足以回避死锁的地步，进程回退时资源释放占有的资源


   > 
    死锁的四个必要条件：    
    互斥：一个资源每次只能被一个集成访问    
    请求与保持：一个进程因请求资源而阻塞时，对已经获取的资源保持不放    
    不剥夺：进程已经获得的资源，在没有使用完成之前，不能强制剥夺    
    循环：若干进程之间形成头尾相接的循环等待资源    

