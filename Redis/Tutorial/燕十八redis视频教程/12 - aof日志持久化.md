# aof日志持久化


## Aof 的配置

```
appendonly no # 是否打开 aof日志功能

appendfsync always   # 每1个命令,都立即同步到aof. 安全,速度慢
appendfsync everysec # 折衷方案,每秒写1次
appendfsync no      # 写入工作交给操作系统,由操作系统判断缓冲区大小,统一写入到aof. 同步频率低,速度快,


no-appendfsync-on-rewrite  yes: # 正在导出rdb快照的过程中,要不要停止同步aof
auto-aof-rewrite-percentage 100 #aof文件大小比起上次重写时的大小,增长率100%时,重写
auto-aof-rewrite-min-size 64mb #aof文件,至少超过64M时,重写
``` 


注: 在dump rdb过程中,aof如果停止同步,会不会丢失?  
答: 不会,所有的操作缓存在内存的队列里, dump完成后,统一操作.

注: aof重写是指什么?  
答: aof重写是指把内存中的数据,逆化成命令,写入到.aof日志里.  
以解决 aof日志过大的问题.

问: 如果rdb文件,和aof文件都存在,优先用谁来恢复数据?  
答: aof

问: 2种是否可以同时用?  
答: 可以,而且推荐这么做

问: 恢复时rdb和aof哪个恢复的快  
答: rdb快,因为其是数据的内存映射,直接载入到内存,而aof是命令,需要逐条执行