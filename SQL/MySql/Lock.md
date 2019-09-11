# Tips of MySql Lock
MySql 有以下几种锁：
1. Shared Lock 分享锁 ( S )
2. Exclusive Locks 独一锁/排他锁 ( X )
3. Table Intention Shared Locks 表级分享意向锁 ( IS )
4. Table Intention Exclusive Locks 表级独一/排他意向锁 ( IX )

其余锁
1. Record Lock 记录锁，只锁单行（如select...where xxx=xxx for update/lock in share mode, insert）
2. Gap Lock 间隔锁，锁住一定范围（如update, delete, select...where xxx>xxx）
3. Next Key Lock 记录锁和间隔锁的结合

## Shared Lock 分享锁 S
- 故名思意，申请锁后仍然同意其他多个事件(Session)申请该**行**的分享锁。
- Shared Lock 分享锁会block阻止其他事件对该行/表的Exclusive Lock 独一锁的申请。

## Exclusive Locks 独一锁/排他锁 X
- 对于单个行只能由一个事件申请一个Exclusive Lock。
- Exclusive Lock会block阻止其他事件对该行的Exclusive Lock和Shared Lock的申请。

## Table Intention Locks 表意向锁 IS & IX & II
- 其中包含Table Intention Shared Locks ( IS )，Table Intention Exclusive Locks ( IX )，Insert Intention Lock ( II )。
- 在执行Select...Lock in share mode和申请其他分享锁时，会先和Table表申请Table Intention Shared Locks。
- 在执行Select...for update, insert, update, delete时会先和Table表申请Table Intention Exclusive Locks。
- IX和IS均允许被多个事件获得，但在申请实际锁（如S，X）时则可能发生相互block（如多个事件申请单个行的X），此时先到先得。
- IX和IS唯一发生阻塞是在一个事件获得了Table Lock时（Alter Table, Drop Table, Lock Tables），其余正要申请IX和IS，后者被阻塞。

- II较为特殊，一般IX和IS只有在table被lock时导致阻塞才能在 `select * from information_schema.INNODB_LOCKS;` 中看到锁。但在往已有锁的行中使用insert时会见到II，即II更多为行级锁，IX和IS更多为表级锁。



## Insert, Update, Delete, For Update/In Shared Mode
- Insert: 使用此语句后会先申请Share的Record锁（II）（此时若有X，则阻塞），随后再申请（X）。若此时由多个事件同时获得II时
- Update：会直接获得X
- delete：会直接获得X
- For Update：会直接获得X

## 对于有range的锁-Gap
比如事件Session#1使用` select * from testinsert where id>4 for update`时，select明确了一个查找范围同时用for update给这范围上锁，对应的锁为Gap+Record；

此时，若另一个事件Session#2使用`insert into testinsert value(7);`。在查询`select * from information_schema.INNODB_LOCKS`时我们可发现Session#2的`lock_mode`为Gap和X，`lock_type`为Record。

由此可知，insert语句在对应的行id=7上没有遇到X锁，所以II（S）锁获得，但在此时申请X锁时被Gap锁阻塞。即Record锁可被Record和Gap锁阻塞。

## DeadLock
此处指出一个比较特殊的死锁情况：

Session#1和Session#2分别对一张空表`select`不同索引的行且`for update`。或者对非空表但`select`不存在且索引值不同的行且`for update`。如：Session#1: `select * from table where id=10 for update` Session#2: `select * from table where id=20 for update`


此时我们在两个Session中分别使用`insert into`至分别拥有锁的对应行中。如：Session#1:`insert into table value(10)`，执行此语句后Session#1 blocked。随后Session#2执行`insert into table value(20)`，系统提示deadlock，Session#2的insert操作取消，此时Session#1的操作不受阻塞，操作成功。

两个Session在`select`时将会获得对应索引行的X Lock，这两个X Lock看似为两个不同的锁，实际上为先相同的锁。

Session#1操作`insert`时blocked因为检测到对应行有锁（此锁来自Session#2），而Session#2 dead lock因为其需要Session#1的锁。

概括：双方拥有锁且锁相同，即使在select中体现为两种锁（因为id不同，但此时两个id均不存在）。所以在insert时仍然需要对方的锁。