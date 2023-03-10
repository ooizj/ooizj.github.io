### spring线程池配置

```java

@Bean
public Executor taskExecutor() {
    ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
    executor.setCorePoolSize(200); // 核心线程大小
    executor.setMaxPoolSize(300); // 最大线程大小
    executor.setQueueCapacity(700); // 任务队列大小，如果不为当前任务占用满corePoolSize，再有任务过来则会放到队列中而不会增加线程，当队列填埋才会增加线程
    executor.setAllowCoreThreadTimeOut(true); // 是否允许核心线程超时，如果为true，则核心线程闲置后等待设置的"keepAliveSeconds"秒后销毁
    executor.setRejectedExecutionHandler(new ThreadPoolExecutor.AbortPolicy()); // 超限策略
    executor.setThreadNamePrefix("task-");
    executor.initialize();
    return executor;
}
```
