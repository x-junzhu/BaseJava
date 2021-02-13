# JUC基础

> 锁是什么

锁是同步监视器, 即当前类的Class对象

> 公平锁与非公平锁

公平锁: 先来后到 缺点: 如果有两个线程执行时间分别是3h 3s那3s必须等3h执行完

非公平锁: 可以插队

> synchronized和Lock的区别
1. synchronized是java内置的关键字;Lock是一个类
2. synchronized无法判断锁的状态;Lock可以判断是否获取到了锁
3. synchronized会自动释放锁;Lock必须手动释放锁
4. synchronized 线程1(获得锁 阻塞)  线程2(等待 一直等);Lock不一定会一直等待下去(tryLock()尝试获取锁)
5. synchronized是可重入锁,不可中断,非公平的;Lock是可重入锁,可判断锁,非公平(可以自由设置是否公平)
6. synchronized适合锁少量代码块;Lock适合锁大量的代码同步
