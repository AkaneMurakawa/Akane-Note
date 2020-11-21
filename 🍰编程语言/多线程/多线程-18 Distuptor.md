# 多线程 - Distuptor



## Disruptor介绍

Disruptor：分裂、瓦解的意思

Disruptor是英国外汇交易公司LMAX开发的一个**高性能队列**，号称"速度最快MQ"。

主页：http://lmax-exchange.github.io/disruptor/

源码：https://github.com/LMAX-Exchange/disruptor

GettingStarted: https://github.com/LMAX-Exchange/disruptor/wiki/Getting-Started

api: http://lmax-exchange.github.io/disruptor/docs/index.html

maven: https://mvnrepository.com/artifact/com.lmax/disruptor



### Disruptor特点

- Disruptor是数组实现的，使用环形Buffer，直接覆盖（不用清除）旧的数据，降低GC频率
- 性能极高，无锁CAS，**单机**支持高并发，单线程能支撑每秒600万订单
- 系统内部的内存队列

- 实现了基于事件的生产者消费者模式（观察者模式）


https://tech.meituan.com/2016/11/18/disruptor.html



为什么Disruptor是数组实现的，而不是链表？

因为数组的访问更快。数组长度`2^n`，通过位运算，加快定位的速度。下标采取递增的形式。不用担心index溢出的问题。



### RingBuffer

环形队列

RingBuffer的序号，指向下一个可用的元素

采用数组实现，没有首尾指针

对比ConcurrentLinkedQueue，用数组实现的速度更快

> 假如长度为8，当添加到第12个元素的时候在哪个序号上呢？用12%8(取模)决定
>
> 当Buffer被填满的时候到底是覆盖还是等待，由Producer决定
>
> 长度设为2的n次幂，利于二进制计算，例如：12%8 = 12 & (8 - 1)  pos = num & (size -1)



### Disruptor重要的三个概念

1. 定义Event：队列中需要处理的元素

2. 定义Event工厂，用于填充队列

   > 这里牵扯到效率问题：disruptor初始化的时候，会调用Event工厂，对ringBuffer进行内存的提前分配
   >
   > GC产频率会降低

3. 定义EventHandler（消费者）：处理容器中的元素



### Event发布示例

```java
long sequence = ringBuffer.next();  // Grab the next sequence
try {
    LongEvent event = ringBuffer.get(sequence); // Get the entry in the Disruptor
    // for the sequence
    event.set(8888L);  // Fill with data
} finally {
    ringBuffer.publish(sequence);
}
```



### EventTranslator发布事件示例

```java
//===============================================================
        EventTranslator<LongEvent> translator1 = new EventTranslator<LongEvent>() {
            @Override
            public void translateTo(LongEvent event, long sequence) {
                event.set(8888L);
            }
        };

        ringBuffer.publishEvent(translator1);

        //===============================================================
        EventTranslatorOneArg<LongEvent, Long> translator2 = new EventTranslatorOneArg<LongEvent, Long>() {
            @Override
            public void translateTo(LongEvent event, long sequence, Long l) {
                event.set(l);
            }
        };

        ringBuffer.publishEvent(translator2, 7777L);

        //===============================================================
        EventTranslatorTwoArg<LongEvent, Long, Long> translator3 = new EventTranslatorTwoArg<LongEvent, Long, Long>() {
            @Override
            public void translateTo(LongEvent event, long sequence, Long l1, Long l2) {
                event.set(l1 + l2);
            }
        };

        ringBuffer.publishEvent(translator3, 10000L, 10000L);

        //===============================================================
        EventTranslatorThreeArg<LongEvent, Long, Long, Long> translator4 = new EventTranslatorThreeArg<LongEvent, Long, Long, Long>() {
            @Override
            public void translateTo(LongEvent event, long sequence, Long l1, Long l2, Long l3) {
                event.set(l1 + l2 + l3);
            }
        };

        ringBuffer.publishEvent(translator4, 10000L, 10000L, 1000L);

        //===============================================================
        EventTranslatorVararg<LongEvent> translator5 = new EventTranslatorVararg<LongEvent>() {

            @Override
            public void translateTo(LongEvent event, long sequence, Object... objects) {
                long result = 0;
                for(Object o : objects) {
                    long l = (Long)o;
                    result += l;
                }
                event.set(result);
            }
        };

        ringBuffer.publishEvent(translator5, 10000L, 10000L, 10000L, 10000L);
```



### 使用Lamda表达式

```java
package com.mashibing.disruptor;

import com.lmax.disruptor.RingBuffer;
import com.lmax.disruptor.dsl.Disruptor;
import com.lmax.disruptor.util.DaemonThreadFactory;

public class Main03
{
    public static void main(String[] args) throws Exception
    {
        // Specify the size of the ring buffer, must be power of 2.
        int bufferSize = 1024;

        // Construct the Disruptor
        Disruptor<LongEvent> disruptor = new Disruptor<>(LongEvent::new, bufferSize, DaemonThreadFactory.INSTANCE);

        // Connect the handler
        disruptor.handleEventsWith((event, sequence, endOfBatch) -> System.out.println("Event: " + event));

        // Start the Disruptor, starts all threads running
        disruptor.start();

        // Get the ring buffer from the Disruptor to be used for publishing.
        RingBuffer<LongEvent> ringBuffer = disruptor.getRingBuffer();


        ringBuffer.publishEvent((event, sequence) -> event.set(10000L));

        System.in.read();
    }
}
```



### ProducerType生产者线程模式

ProducerType有两种模式 Producer.MULTI和Producer.SINGLE

- 默认是MULTI，表示在多线程模式下产生sequence


- 如果确认是单线程生产者，那么可以指定SINGLE，效率会提升


如果是多个生产者（多线程），但模式指定为SINGLE，会出什么问题呢？答：会造成数据错乱。



### 等待策略

1. 【常用】BlockingWaitStrategy：通过线程阻塞的方式，等待生产者唤醒，被唤醒后，再循环检查依赖的sequence是否已经消费。

2. BusySpinWaitStrategy：线程一直自旋等待，可能比较耗cpu

3. LiteBlockingWaitStrategy：线程阻塞等待生产者唤醒，与BlockingWaitStrategy相比，区别在signalNeeded.getAndSet,如果两个线程同时访问一个访问waitfor,一个访问signalAll时，可以减少lock加锁次数.

4. LiteTimeoutBlockingWaitStrategy：与LiteBlockingWaitStrategy相比，设置了阻塞时间，超过时间后抛异常。

5. PhasedBackoffWaitStrategy：根据时间参数和传入的等待策略来决定使用哪种等待策略

6. TimeoutBlockingWaitStrategy：相对于BlockingWaitStrategy来说，设置了等待时间，超过后抛异常

7. 【常用】YieldingWaitStrategy：尝试100次，然后Thread.yield()让出cpu

8. 【常用】SleepingWaitStrategy : sleep



### 消费者异常处理

默认：disruptor.setDefaultExceptionHandler()

覆盖：disruptor.handleExceptionFor().with()



## Log4j2

在log4j2提供了异步处理，同时使用了Disruptor提高了效率。

示例配置：

```xml
        <AsyncLogger name="com.example.log" level="${logger.level}" additivity="false">
            <appender-ref ref="FILE.DEBUG"/>
            <appender-ref ref="FILE.INFO"/>
            <appender-ref ref="FILE.WARN"/>
            <appender-ref ref="FILE.ERROR"/>
        </AsyncLogger>
```

AsyncLogger中便封装了Disruptor 

```java
public class AsyncLogger extends Logger implements EventTranslatorVararg<RingBufferLogEvent> {
    private final AsyncLoggerDisruptor loggerDisruptor;
    
// ----------------------------------------------------------------------------------------------------------------------
class AsyncLoggerDisruptor extends AbstractLifeCycle {
        
      public synchronized void start() {
        if (this.disruptor != null) {
            LOGGER.trace("[{}] AsyncLoggerDisruptor not starting new disruptor for this context, using existing object.", this.contextName);
        } else {
            LOGGER.trace("[{}] AsyncLoggerDisruptor creating new disruptor for this context.", this.contextName);
            this.ringBufferSize = DisruptorUtil.calculateRingBufferSize("AsyncLogger.RingBufferSize");
            WaitStrategy waitStrategy = DisruptorUtil.createWaitStrategy("AsyncLogger.WaitStrategy");
            ThreadFactory threadFactory = new Log4jThreadFactory("AsyncLogger[" + this.contextName + "]", true, 5) {
                public Thread newThread(Runnable r) {
                    Thread result = super.newThread(r);
                    AsyncLoggerDisruptor.this.backgroundThreadId = result.getId();
                    return result;
                }
            };
            this.asyncQueueFullPolicy = AsyncQueueFullPolicyFactory.create();
            // 熟悉的Disruptor使用方式
            this.disruptor = new Disruptor(RingBufferLogEvent.FACTORY, this.ringBufferSize, threadFactory, ProducerType.MULTI, waitStrategy);
            ExceptionHandler<RingBufferLogEvent> errorHandler = DisruptorUtil.getAsyncLoggerExceptionHandler();
            this.disruptor.setDefaultExceptionHandler(errorHandler);
            RingBufferLogEventHandler[] handlers = new RingBufferLogEventHandler[]{new RingBufferLogEventHandler()};
            this.disruptor.handleEventsWith(handlers);
            LOGGER.debug("[{}] Starting AsyncLogger disruptor for this context with ringbufferSize={}, waitStrategy={}, exceptionHandler={}...", this.contextName, this.disruptor.getRingBuffer().getBufferSize(), waitStrategy.getClass().getSimpleName(), errorHandler);
            this.disruptor.start();
            LOGGER.trace("[{}] AsyncLoggers use a {} translator", this.contextName, this.useThreadLocalTranslator ? "threadlocal" : "vararg");
            super.start();
        }
    }

```