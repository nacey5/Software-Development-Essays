
故障级别&#x20;

&#x9;1.逻辑错误

&#x9;		这部分需要开发者去处理，处理可能出现的错误

&#x9;2.系统错误

&#x9;		硬件故障，这部分跟dbms无关的，但是在设计db的时候也是需要考虑的

&#x9;3.存储媒介故障

&#x9;		这部分故障设计db的时候不考虑

矛盾点：一个是高速写入的时候，又得落入非易失性的介质

undolog：撤销日志

redolog：重做日志

缓存池策略

![image.png](https://note.youdao.com/yws/res/9541/WEBRESOURCE21387408878eec09b5379da2bd262a6b)

这里设涉及到的一个事就是如果这个pool中存在多个事务的数据，但是其中一个数据是失败的，要回滚，那么如果在之前就把数据写盘，那我是不是还得拿出来重写改掉呢？&#x20;

force和not force策略

steal和not steal

👇👇👇👇👇👇👇👇👇👇👇👇

not steal+force 会新开一个单独的页，为什么不能单独改一个数据呢？因为db管理内存的最小单位就是页

缺点：缓存池中的页会太多并且刷盘太频繁

![image.png](https://note.youdao.com/yws/res/9576/WEBRESOURCE09dbdfd7c9bc061b349b5f7ae81e8b6d)

影子页

1.建备份

2.在备份上改

3.指针重新指向

![image.png](https://note.youdao.com/yws/res/9557/WEBRESOURCE887f491670d7ae5458c37c97dd17d35a)

不需要redo

undo的时候只需要把本地修改的页去掉就行了

缺点：刷盘更频繁

&#x9;		commit瞬间要干的东西太多了

&#x9;		也需要把一整个页放进缓存，开销太大了

&#x9;		垃圾回收之后数据更加碎片化

SQLLIte 就用了优化的shawn page的改进版--2010年之后就放弃了这种做法

wal--主角！！！

![image.png](https://note.youdao.com/yws/res/9567/WEBRESOURCE5b1df067c5df98f2fb3666cd3f63c5e6)

在大部分db中，undolog 和redolog 可能做成同一个，但是在mysql中却是做成了两个 。

优化手段：日志组提交

WAL因为是顺序追加的，所以性能很ok

![image.png](https://note.youdao.com/yws/res/9574/WEBRESOURCEcf9c8919e704d308569139430f596a74)

日志库

![image.png](https://note.youdao.com/yws/res/9579/WEBRESOURCE33da6468e7014f508eb4658f0a23709f)

&#x9;物理日志和逻辑日志

&#x9;物理日志：记载了某个页的具体更改和位置

&#x9;逻辑日志：某个操作的指令

&#x9;都有各个自己的缺点，一个是占用内存太大，并且db中空间重整的时候也可能会出现我问题，另一个可能恢复不准确，比如now（）函数具有时效性，恢复不行，还有很多问题

所以现在很多都是用physiological logging，使用槽，也就是混合的，mysql中的物理日志也是这个混合的

也有混合性，mixed类型

![image.png](https://note.youdao.com/yws/res/9592/WEBRESOURCEb6da381a884d06582629a78ae77f9aaf)

undolog 就算before

redolog就算after

checkpoints检查点

一个刷盘标记，什么样的数据需要做redolog，什么时候需要做undolog

问题：全阻塞，性能，频率等等

![image.png](https://note.youdao.com/yws/res/9599/WEBRESOURCE0158d74da7664ad34c2ae6091dcef454)
