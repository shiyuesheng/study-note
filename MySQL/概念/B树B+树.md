因为B+树的原理是 叶子节点存储数据，非叶子节点存储索引，B+树的每个节点可以存储多个关键字，它将节点大小设置为磁盘页的大小，充分利用了磁盘预读的功能。每次读取磁盘页时就会读取一整个节点,每个叶子节点还有指向前后节点的指针，为的是最大限度的降低磁盘的IO;因为数据在内存中读取耗费的时间是从磁盘的IO读取的百万分之一







"OK，就是在I/O操作都时候，例如磁盘I/O，网络I/O等！为什么一般是在I/O操作都时候，要用多线程呢(面试高频题，必背)？因为I/O操作一般可以分为两个阶段:即等待I/O准备就绪和真正操作I/O资源！"

"以磁盘操作为例，磁盘的结构如下"

"在磁盘上数据是分磁道、分簇存储的，而数据往往并不是连续排列在同一磁道上，所以磁头在读取数据时往往需要在磁道之间反复移动，因此这里就有一个寻道耗时！另外，盘面旋转将请求数据所在扇区移至读写头下方也是需要时间，这里还存在一个旋转耗时！"

"那么，在这一时间段（即"I/O等待"）内，线程是在“阻塞”着等待磁盘，此时操作系统可以将那个空闲的CPU核心用于服务其他线程。因此在I/O操作的情况下，使用多线程，效率会更高！"

"OK,现在回到我们的问题！Redis读写数据有涉及到I/O操作么？"

"所以啊，Redis不涉及I/O操作，因此设计为单线程是效率最高的！那么，既然你知道既然Redis的性能和CPU无关，那你知道Redis的性能瓶颈在哪么？"

小刘无奈的摇了摇头！

一般在两个地方

其一是机器内存大小，内存大小关系到Redis存储的数据量其二是网络带宽，这点我仔细说一下Redis客户端执行一条命令分为四个过程：发送命令、命令排队、命令执行、返回结果

而其中发送命令+返回结果这一过程被称为Round Trip Time（RTT，往返时间）



