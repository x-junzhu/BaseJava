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

> Callable
1. 可以有返回值
2. 可以抛出异常
3. 方法不同 run()/call()
4. 多个线程执行会有缓存使得返回结果只有一个, 同时如果方法执行时间长可能会出现阻塞.

> 常用辅助类
1. CountDownLatch
```
public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(6);


        for (int i = 1; i <= 6 ; i++) {
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "离开教室");
                countDownLatch.countDown();//-1操作
            }, String.valueOf(i)).start();
        }

        // 等待计数器归零, 然后再向下执行
        countDownLatch.await();

        System.out.println("关门");
    }
```
2. CyclicBarrier
```
public static void main(String[] args) {

        // 召唤神龙的线程
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7, () -> {
            System.out.println("七颗龙珠集齐, 召唤神龙");
        });
        // 集齐七颗龙珠召唤神龙
        for (int i = 1; i <= 7 ; i++) {
            final int temp = i;
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "收集到第" + temp + "颗龙珠");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }, String.valueOf(temp)).start();
        }
    }
```
3. Semaphore
```
// 模拟停车位(3个), 应用场景: 限流
    public static void main(String[] args) {
        // 3个线程等同于3个停车位
        Semaphore semaphore = new Semaphore(3);

        for (int i = 1; i <= 6 ; i++) {
            final int temp = i;
            new Thread(() -> {
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + "获取到第" + temp + "停车位");
                    // 模拟停车时间
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName() + "离开到第" + temp + "停车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();
                }
            }, "宝马X" + temp).start();

        }
    }
```
> ReadWriteLock
```
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCacheLock myCache = new MyCacheLock();
        // 写入
        for (int i = 1; i <= 5; i++){
            final int temp = i;
            new Thread(() -> {
             myCache.put("第" + temp + "变量", "" + temp);
            }, String.valueOf(i)).start();
        }
        // 读取
        for (int i = 1; i <= 5; i++){
            final int temp = i;
            new Thread(() -> {
                myCache.get("第" + temp + "变量");
            }, String.valueOf(i)).start();
        }
    }
}
class MyCacheLock{
    private volatile Map<String, Object> map = new HashMap<>();
    // 读写锁: 更加细粒度的控制
    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();
    // 存 -> 写(只希望写入的时候只有一个线程在操作)
    public void put(String key, Object value){
        // 加锁
        readWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "写入" + key);
            map.put(key, value);
            System.out.println(Thread.currentThread().getName() + "写入完成");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.writeLock().unlock();
        }
    }
    // 取 -> 读(所有线程都可以读取)
    public void get(String key){
        readWriteLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "读取" + key);
            Object res = map.get(key);
            System.out.println(Thread.currentThread().getName() + "读取完成, value=" + res);
        } catch (Exception e){
            e.printStackTrace();
        } finally {
            readWriteLock.readLock().unlock();
        }
    }
}
```
1. 独占锁(写锁):一次只能被一个线程占有
2. 共享锁(读锁):多个线程可以同时占有

读-读: 可以共存

读-写: 不可共存

写-写: 不可共存

> BlockingQueue(四组API)

|方式|抛出异常|不会抛出异常,有返回值|阻塞等待|超时等待|
|:----:|:----:|:----:|:----:|:----:|
|添加|add()|offer()|put()|offer()|
|移除|remove()|poll()|take()|poll()|
|查看队首元素|element()|peek()|-|-|
1. 抛出异常
```
    /**
     * 抛出异常
     */
    public static void throwException(){
        // FIFO: 需要初始化队列参数
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);

        System.out.println(blockingQueue.add("a"));
        System.out.println(blockingQueue.add("b"));
        System.out.println(blockingQueue.add("c"));

        // 查看对首元素
        System.out.println(blockingQueue.element());

        // 当队列满了抛出异常: java.lang.IllegalStateException: Queue full
//        System.out.println(blockingQueue.add("d"));

        // 弹出队列元素
        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());

        // 队列为空后再取元素: java.util.NoSuchElementException
        System.out.println(blockingQueue.remove());

    }
```
2. 不会抛出异常,有返回值
```
/**
     * 不抛出异常,但带有返回值
     */
    public static void notThrowException(){
        // FIFO: 需要初始化队列参数
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);

        System.out.println(blockingQueue.offer("a"));
        System.out.println(blockingQueue.offer("b"));
        System.out.println(blockingQueue.offer("c"));

        // 查看对首元素
        System.out.println(blockingQueue.peek());

        // 当队列满了不抛出异常, 但会返回false
        System.out.println(blockingQueue.offer("d"));

        // 弹出队列元素
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());

        // 队列为空后再取元素: 返回null
        System.out.println(blockingQueue.poll());
    }
```
3. 阻塞等待(一直阻塞)
```
/**
     * 阻塞等待(一直等待)
     */
    public static void waiting() throws InterruptedException {
        // FIFO: 需要初始化队列参数
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);

        blockingQueue.put("a");
        blockingQueue.put("b");
        blockingQueue.put("c");

        // 当出现阻塞时会一直等待
//        blockingQueue.put("d");
        System.out.println(blockingQueue.take());
        System.out.println(blockingQueue.take());
        System.out.println(blockingQueue.take());

        // 队列为空后再取元素: 一直等待, 直到有元素
        System.out.println(blockingQueue.take());
    }
```
4. 超时等待
```
    /**
     * 超时等待
     */
    public static void waitingCanStop() throws InterruptedException {
        // FIFO: 需要初始化队列参数
        ArrayBlockingQueue blockingQueue = new ArrayBlockingQueue<>(3);

        System.out.println(blockingQueue.offer("a", 3, TimeUnit.SECONDS));
        System.out.println(blockingQueue.offer("b", 3, TimeUnit.SECONDS));
        System.out.println(blockingQueue.offer("c", 3, TimeUnit.SECONDS));

        // 当队列满了之后, 会超时在规定时间内插入, 若无法插入则返回false
//        System.out.println(blockingQueue.offer("c", 3, TimeUnit.SECONDS));

        // 从队列中取出值
        System.out.println(blockingQueue.poll(2, TimeUnit.SECONDS));
        System.out.println(blockingQueue.poll(2, TimeUnit.SECONDS));
        System.out.println(blockingQueue.poll(2, TimeUnit.SECONDS));

        // 当队列为空后, 尝试在规定时间内从队列中取出值, 若无法取出则返回null
        System.out.println(blockingQueue.poll(2, TimeUnit.SECONDS));
    }
```
> SynchronousQueue 同步队列

SynchronousQueue和其他的BlockingQueue不一样, 它不存储元素, 当put()一个元素后, 必须等待take()出来后, 否则不能put()进去值