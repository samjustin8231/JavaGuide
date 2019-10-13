# SpringBoot使用线程池

```
@Configuration
public class TreadPoolConfig {


    /**
     * 消费队列线程
     * @return
     */
    @Bean(value = "consumerQueueThreadPool")
    public ExecutorService buildConsumerQueueThreadPool(){
        ThreadFactory namedThreadFactory = new ThreadFactoryBuilder()
                .setNameFormat("consumer-queue-thread-%d").build();

        ExecutorService pool = new ThreadPoolExecutor(5, 5, 0L, TimeUnit.MILLISECONDS,
                new ArrayBlockingQueue<Runnable>(5),namedThreadFactory,new ThreadPoolExecutor.AbortPolicy());

        return pool ;
    }
}
```

使用时

```
    @Resource(name = "consumerQueueThreadPool")
    private ExecutorService consumerQueueThreadPool;

    @Override
    public void execute() {
        //消费队列
        for (int i = 0; i < 5; i++) {
            consumerQueueThreadPool.execute(new ConsumerQueueThread());
        }
    }
```