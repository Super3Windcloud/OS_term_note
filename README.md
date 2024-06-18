# OS_term_note
SIT 操作系统期末复习笔记




#  单一连续分配 整个内存空间位一个程序服务 简单资源利用率低

1. 固定分区 :  *内存被分为多个固定大小分区  ,每个 分区可以装入一个程序 但是可能产生内部碎片* 
2. 动态分区: __内存根据程序实际运行大小分配 ,减少内部碎片 ,但是可能产生外部碎片 __ 
   - 动态可重定位分区, 使用内存压缩技术 将分散的空间内存合并  , 	减少外部碎片 

跳转到 [section](#section) 



#   存储的离散分配

| 分段                                                         | 分页                                                         | 段页式                                                 |
| :----------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------ |
| 内存按逻辑单元划分为不同大小的段，每段独立管理，便于共享和保护。 | 将内存和程序划分为固定大小的页和页框，使用页表管理，不易产生外部碎片，但可能有内部碎片 | 结合分页和分段的优点，内存先按段划分，每段再分页管理。 |

## 续存相关概念 {#IO}

| 动机                                                         | 场合                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 提供比实际物理内存更大的地址空间，使程序可以运行在较小的物理内存上，同时提高内存利用率和系统稳定性。 | 当程序需要的内存超过物理内存容量时，或者需要多个程序同时运行时。 |

### 虚存内存分配策略

> 请求分页: 按需将页面加载进内存, 只有当页面被访问时才从磁盘调入，减少内存占用。
>
> 请求分段,: 按需将页面装入内存，类似于请求分页，但管理单位是段而非页

__内部碎片 VS 外部碎片 __

``` markdown
- 内部碎片:由于分配的内存大于实际需求的内存, 造成未使用完全的内存空间 
>>> 通常出现在固定分区和分页
```

---

``` markdown
- 外部碎片: 由于频繁的内存分配和释放, 导致大量小的非连续空间内存块出现, 通常在动态内存分配
```

###  IO的设备编址方式  

==端口映射IO==   :将I/O设备的寄存器映射到主存地址空间的一部分，通过访问特定的内存地址来进行I/O操作。

> 优点 : **使用普通的内存访问指令无需 专用IO**
>
> 缺点 : *可能会占用部分内存空间*

:sparkler: 端口映射IO :使用专门的I/O指令（如IN、OUT）来访问I/O设备，通过端口地址进行操作。

 ``` markdown
- 优点 : 不占用内存空间
 - 缺点 :需要额外的IO指令集
 ```

### 字符设备与块设备区别

- :sparkle: 字符设备

```markdown
- 特点 :以字符为单位进行数据传输  ,不支持随机访问 
- 举例：键盘、鼠标、串口设备。
+ 操作方式：读写操作是连续的，数据以流的形式处理。
```

:sparkles:   **快设备**

```markdown
- 特点 : 固定大小数据块进行数据传输	 , 支持随机访问
* 硬盘 SSD 光盘 
+  可以对任意块进行读写，支持缓冲和缓存。
```

(:sparkles: )  **设备管理层次**

```markdown
1. 设备驱动程序 : 与具体硬件交互, 负责设备初始化, 控制和数据传输
2. 设备独立软件 : 提供统一接口, 屏蔽设备具体实现细节, 使用设备通用功能, 如缓冲管理, 设备调度
3. IO系统管理程序 :处理用户请求, 提供设备分配和释放, 错误处理等
4. 用户IO: 为应用程序提供IO操作的API 
```

:coffee:   :stars: :carousel_horse:  **设备管理中为何要引入缓冲？**

``` markdown
- 提高效率 : 缓冲区可减少CPU与I/O设备之间的等待时间，提高数据传输效率
- 数据流控制: 可以平滑数据流, 防止IO设备之间速度不匹配导致数据丢失和系统阻塞
- 数据整合 :将小数据转化成大块数据传输, 提高传输效率
```

:rainbow::anchor: :lollipop: **Spooling的基本原理**

- **Spooling（Simultaneous Peripheral Operation On-line）**：一种将输入输出设备操作与计算机程序执行分离的方法，通过使用临时存储（如磁盘）来缓冲输入输出数据

  > ```markdown
  > - 应用场景 : 将IO操作提交到缓冲区, IO设备可以与CPU独立工作, 提高运行效率, 批处理作业
  > ```
  >
  > 

:dart: :gem: :surfing_man: :golf: **中断与DMA的基本工作原理，中断源举例**

- 中断 

  > ```markdown
  > - 基本原理 :当IO设备需要CPU时  通过中断信号通知CPU, CPU暂停工作到中断服务器程序处理请求, 处理完毕返回原来的工作
  > - 外部中断: 键盘输入 鼠标点击
  > - 内部中断 : 除零错误, 非法指令
  > - 软件中断:系统调用 
  > ```

- DMA 

  > ```markdown
  > * 基本原理 :通过DMA直接在内存和IO设备之间传输数据 无需CPU的干预
  > *工作过程: CPU设置DMA 控制器之后 DMA控制器直接传输数据, 完成后发送中断通知CPU
  > ```
  >
  > 

### 常见外存分配算法 :soccer: :gun: :hocho: :dvd: :santa:

- 顺序分配 : *顺序分配磁盘空间 ,需要连续空闲块*
-  **链式分配** : 使用链表数据结构管理文件的磁盘块，每个块包含指向下一个块的指针。
- **索引分配**  : 为每个文件分配一个索引块 , 该索引块 存储文件的所有磁盘块地址
- **多级索引分配** :  类似索引分配，但索引块本身也可以分为多级，以处理大文件。

### 文件逻辑结构与物理结构 :ghost: :jack_o_lantern: :christmas_tree: :blossom:  :shell:

> ```markdown
> - 逻辑结构 : 文件数据项之间的逻辑关系 , 包括顺序结构, 随机结构 和索引结构
> - 物理结构: 磁盘上的组织形式  , 包括连续结构与链接结构 索引结构
> ```
>
> 

### 文件保护的常见数据结构及其比较   :dragon_face: :panda_face: :snake:  :snowflake: :snowman:

> - 访问控制列表 : 为每一个文件维护一个访问控制链表, 列出所有访问该文件的用户和用户组, 并指定权限
> - 访问矩阵: 以二维矩阵的形式表示系统的所有主体 与对象的访问权限关系  , 如用户与文件之间的关系
> - 能力 : 每个对象存储一个能力, 是该对象的访问权限描述, 只有拥有能力的主体才能够访问文件

### 文件存储空间管理 :collision:  :cupid: 	:sparkling_heart: :rage:  :sunglasses:

```markdown
- 空闲表 :用于记录所有空闲块的列表 , 每个条目item表示空闲块的位置和大小
- 空闲链表: 将空闲块链接起来形成链表, 每个空闲块 包含下一个空闲块的地址
- 位示图（Bit Map）：使用位示图来管理磁盘空间，位示图中的每一位表示对应磁盘块的状态（空闲或占用）。
```

## 常见的文件系统 

> -  fat文件系统 :一种简单的文件系统，主要用于较小的存储设备，如闪存驱动器和老式硬盘。
> - **NTFS ** : ：由微软开发的先进文件系统，支持大容量硬盘、大文件和高级安全特性。
> - **ext** :Linux操作系统中常见的文件系统，提供高性能、可靠性和数据保护特性。
> - **APFS（Apple File System）**：苹果公司最新的文件系统，优化了文件系统的性能、可靠性和安全性，支持SSD等新技术。

## 磁盘扫描算法

- **FIFO** 

  ```markdown
  工作原理 : 按照请求顺序进行磁盘扫描, 先进入请求队列的请求先处理
  - 优点 :实现简单, 适用于轻度负载系统
  - 缺点: 可能会导致较长的平均寻道时间，不考虑磁头移动的最优化，容易产生“电梯效应”。
  ```

- **SSTF**

  > - 工作原理  :- 每次选择离当前磁头位置最近的请求进行处理, 最小化寻道时  
  > - 优点: 有效减少寻道时间, 提高寻道效率 
  > - 缺点: 可能导致一些请求长时间等待, 产生饥饿效应  

- **SCAN**

  > ````markdown
  > - 工作原理 : 磁头沿着一个方向移动,处理请求,直到该方向最后一个请求,然后磁头反向移动, 继续处理反方向请求
  > - 优点: 能够避免长时间请求等待, 但不保证平均寻道时间最小
  > - 缺点: 电梯效应, 较远距离请求可能长时间等待 
  > ````

- **CSCAN**

  > **工作原理**：类似于SCAN，但是磁头到达磁盘末端后立即返回到磁盘起始位置，形成循环。
  >
  > **优点**：减少某些请求的等待时间，适合某些特定的应用场景，减少了“电梯效应”的影响。

## 分页计算 

给定地址结构，计算可分配的页数、页大小、转换地址

> - 页大小:	指定系统中每一页的大小，通常为2的幂次方，如4KB、8KB等。
> - 页表:   用于将逻辑地址转化为物理地址的数据结构
> - **转换地址（Translation Address）**：逻辑地址经页表转换后得到的物理地址

**二级页表的作用及引入二级页表后，引入虚存与不引入虚存的区别**

> **二级页表 ** :在大内存系统中,为了减小单页表的大小和提高页表查找效率, 将单页表划分成二级页表
>
> **作用** : 降低页表存储空间和访问时间, 使操作系统能管理更大的内存	
>
> **引入续存的区别** : 
>
> > - **引入虚存**：允许程序将未使用的部分数据暂时存储在磁盘上，从而释放物理内存给其他程序使用。通过页表和页表项的转换，实现逻辑地址到物理地址的映射。
> > - **不引入虚存**：程序的地址空间直接映射到物理内存，无需页表和页表项的转换，但系统的可用内存受到物理内存大小的限制，不具备内存空间的动态调整和管理能力。



### 顺序文件与索引顺序文件的工作原理 

- 顺序文件

  > 工作原理 : 文件中的记录按照某种顺序（如关键字升序）顺序存储，记录之间可能有固定长度的间隔。顺序文件适合顺序访问，通过顺序查找来获取数据。

- 索引顺序文件 

  ```markdown
  文件中的记录按顺序存储，同时维护一个索引表，索引表中的每个条目包含关键字和对应记录的指针或地址。索引表支持快速的索引查找和顺序查找。
  ```

## 页面置换算法

1. **OPT**(Optimal)"

   > ```markdown
   > - 工作原理: 选择在未来最长时间内不会访问的页面进行置换, 理论保证最低的缺页率 
   > - 优点: ：理论上最优的置换算法，可以作为其他算法性能的参照标
   > ```

2. LRU  (least recently used ) 

   > ```markdown 
   > - 原理: 选择最近最久没有使用的页面进行置换
   > ```
   >
   > ``` markdown
   > - 优点: 相对于OPT算法，实现较为简单，且在实际应用中通常能够较好地模拟出最优置换的效果 
   > ```

3. **CLOCK**

   > ```markdown
   >  - 原理: 基于近似LRU算法，通过使用一个“时钟”指针循环扫描页面，替换第一个遇到的未被使用的页面
   >  - 优点：实现相对简单，性能接近LRU，但减少了对时间戳的管理和维护。
   > ```

##  文件保护相关数据的解读

> ```markdown
> 访问控制列表（ACL）：为每个文件或资源列出所有可以访问它的用户或用户组，以及他们的权限。
> 访问矩阵（Access Matrix）：以二维矩阵形式表示系统中所有主体（如用户）与对象（如文件）之间的访问权限关系。
> 能力（Capability）：在每个对象中存储一个能力，能力是对该对象的访问权限描述，只有拥有这个能力的主体才能访问该对象。
> ```
>
>  

#### 同步与互斥

同步与互斥是并发编程中的两个重要概念。同步确保多个线程按照特定的顺序执行，互斥确保在某一时刻只有一个线程可以访问共享资源。

####  前驱图的编程实现 ,前驱图是用于表示任务依赖关系的有向图，其中每个节点表示一个任务，每条有向边表示一个任务对另一个任务的依赖关系。

## 使用信号量解决经典同步互斥问题 

> - **生产者消费者问题** 
>
>   > 生产者-消费者问题是一个经典的同步问题。生产者生成产品放入缓冲区，消费者从缓冲区取产品。使用信号量来确保同步和互斥。 
>   >
>   > ``` Python 
>   > import  threading 
>   > import time 
>   > import random 
>   > buffer = []  #缓冲区定义 
>   > buffer_size =10 
>   > empty 	=threading.Semaphore(buffer_size) #空槽信号量 
>   > full =threading.Semephore(0) #满槽信号量
>   > mutex =threading.Semephore(1) #互斥信号量
>   > def producer () : 
>   >     while True :
>   >         number= random.randint( 1,100) 
>   >         emply.acquire () ; 
>   >         mutex.acquire() ; 
>   >         buffer.append(number) 
>   >         print (f' already product {number}')
>   >         mutex.release () 
>   >         full.release () 
>   >         time.sleep (random.random())
>   >         
>   > def consumer () :
>   >     while True :
>   >         full.acquire() 
>   >         mutex.acquire() 
>   >         item =buffer.pop(0) 
>   >         print (f'already consume {item}')
>   >         mutex.release() 
>   >         emply .acquire() 
>   >         time.sleep(random.random()) 
>   >         
>   > product_thread = threading.Thread(target =producer ) 
>   > consume_thread =threading .Thread(target=consumer) 
>   > product_thread.start() 
>   > consume_thread.start() 
>   > product_thread.join() #继续执行后续线程代码
>   > consume_thread.join() 
>   > ```
>   >
>   > 
>
> - **读者写者问题 **  
>
>   > ```Python 
>   > import threading
>   > import  random 
>   > import time 
>   > current_reader_num =0 
>   > mutex =threading.Semaphore(1) 
>   > write_signal = threading.Semaphore (1) 
>   > def reader () : 
>   >     global current_reader_num 
>   >     while True : 
>   >         mutex.acquire() 
>   >         current_reader_num +=1 ; 
>   >         if (current_reader_num==1): 
>   >             write_signal.acquire() 
>   > 		mutex.release () 
>   >         print ("IS  Reading ")
>   >         time.sleep (random.random()) 
>   >         mutex.acquire() 
>   >         current_reader_num -=1 #python 没有递增和递减
>   >         if(current_reader_num==0) : 
>   >             write_signal.release() 
>   >          mutex.release () 
>   >         time.sleep(random.random()) 
>   > def  writer (): 
>   >     while True  : 
>   >         write_signal .acquire() 
>   >         print ("is writing ") 
>   >         time.sleep(random.random()) 
>   >         write_signal .release() 
>   >         time.sleep(random.random()) 
>   > reader_thread = [threading.Thread(target= reader) for _ in range(0,5) ]
>   > writer_threads = [threading.Thread(target=writer) for _ in range(2)]
>   > 
>   > # 启动所有读者线程和写者线程
>   > for t in reader_threads:
>   >     t.start()
>   > for t in writer_threads:
>   >     t.start()
>   > 
>   > # 等待所有读者线程和写者线程结束
>   > for t in reader_threads:
>   >     t.join()
>   > for t in writer_threads:
>   >     t.join		
>   > ```
>   >
>   > 

- **哲学家就餐问题** 

  > ``` python
  > import threading
  > import random
  > import time
  > number_people =5 
  > forks = [threading.Semaphore(1)  for _ in range(0,number_people)] #创建叉子资源
  > def philosopher(i) : 
  >     while True : 
  >         print (f"philosopher {i} is thinking" ) 
  >         time.sleep (random.random() ) 
  >         forks[i].acquire() 
  >         forks[(i+1)%number_people].auquire() 
  >         print (f"philosophere {i} is eating ") 
  >         time.sleep(random.random()) 
  >         forks[i].release() 
  >         forks[(i+1)%number_people].release() 
  > philosopher_thread =[threading.Thread(targer=philosopher, args=(i,))  for  i  in range (0, number_people) ] 
  > #两者IIFE写法
  > [t.start()  for  t in philosopher_thread] 
  > list(map(lambda t: t.start()  , philosopher_thread)) 
  > list(map (lambda t: t.join() , philosopher_thread)) 
  > ```
  >
  > 

###  交换相关语句是否会引发死锁？

在哲学家就餐问题中，如果交换拿叉子的顺序（如第一个哲学家先拿右叉子，再拿左叉子），可能会引发死锁。为了避免死锁，可以使用以下策略：

1. **资源分级策略**：将资源编号，按照编号顺序获取资源。
2. **服务员策略**：一个服务员限制最多同时允许N-1个哲学家拿叉子。

``` Python 
import threading
import time
import random

N = 5
forks = [threading.Semaphore(1) for _ in range(N)]
room = threading.Semaphore(N - 1)

def philosopher(i):
    while True:
        print(f'Philosopher {i} is thinking.')
        time.sleep(random.random())
        
        room.acquire()
        forks[i].acquire()
        forks[(i+1)%N].acquire()
        
        print(f'Philosopher {i} is eating.')
        time.sleep(random.random())
        
        forks[i].release()
        forks[(i+1)%N].release()
        room.release()

philosopher_threads = [threading.Thread(target=philosopher, args=(i,)) for i in range(N)]
for t in philosopher_threads:
    t.start()
for t in philosopher_threads:
    t.join()

```

