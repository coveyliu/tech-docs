# spring 执行任务



`Runnable` 任务的执行,有两种方式, 对应两个接口 `TaskExecutor` , `TaskScheduler`

分别对应的是:

- 执行任务
- 定时执行任务



## TaskExecutor



可以看到,`TaskExecutor` 跟 `Executor` 没什么区别

```java
@FunctionalInterface
public interface TaskExecutor extends Executor {

	@Override
	void execute(Runnable task);

}

// Executor 接口
public interface Executor {

    void execute(Runnable command);
}
```



spring 提供的内部实现:



![image-20220223102454891](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/image-20220223102454891.png)



其中 `ThreadPoolTaskExecutor` 最常用, `TheadPoolTaskExecutor` 内部使用的线程池,是 `JDK` 提供的 `ThreadPoolExecutor` , 以 bean 属性方式配置的参数,最终都是 `ThreaPoolExecutor` 的那几个核心参数



看下,spring 的 `ThreadPoolTaskExecutor` 是怎么写的?

```java
public class ThreadPoolTaskExecutor extends ExecutorConfigurationSupport
		implements AsyncListenableTaskExecutor, SchedulingTaskExecutor {

	private final Object poolSizeMonitor = new Object();

	private int corePoolSize = 1;

	private int maxPoolSize = Integer.MAX_VALUE;

	private int keepAliveSeconds = 60;

	private int queueCapacity = Integer.MAX_VALUE;

	private boolean allowCoreThreadTimeOut = false;

	@Nullable
	private TaskDecorator taskDecorator;

	@Nullable
	private ThreadPoolExecutor threadPoolExecutor;

	// Runnable decorator to user-level FutureTask, if different
	private final Map<Runnable, Object> decoratedTaskMap =
			new ConcurrentReferenceHashMap<>(16, ConcurrentReferenceHashMap.ReferenceType.WEAK);


	/**
	 * Set the ThreadPoolExecutor's core pool size.
	 * Default is 1.
	 * <p><b>This setting can be modified at runtime, for example through JMX.</b>
	 */
	public void setCorePoolSize(int corePoolSize) {
		synchronized (this.poolSizeMonitor) {
			this.corePoolSize = corePoolSize;
			if (this.threadPoolExecutor != null) {
				this.threadPoolExecutor.setCorePoolSize(corePoolSize);
			}
		}
	}


	public void setMaxPoolSize(int maxPoolSize) {
		synchronized (this.poolSizeMonitor) {
			this.maxPoolSize = maxPoolSize;
			if (this.threadPoolExecutor != null) {
				this.threadPoolExecutor.setMaximumPoolSize(maxPoolSize);
			}
		}
	}

	/**
	 * Set the ThreadPoolExecutor's keep-alive seconds.
	 * Default is 60.
	 * <p><b>This setting can be modified at runtime, for example through JMX.</b>
	 */
	public void setKeepAliveSeconds(int keepAliveSeconds) {
		synchronized (this.poolSizeMonitor) {
			this.keepAliveSeconds = keepAliveSeconds;
			if (this.threadPoolExecutor != null) {
				this.threadPoolExecutor.setKeepAliveTime(keepAliveSeconds, TimeUnit.SECONDS);
			}
		}
	}
    
    public ThreadPoolExecutor getThreadPoolExecutor() throws IllegalStateException {
		Assert.state(this.threadPoolExecutor != null, "ThreadPoolTaskExecutor not initialized");
		return this.threadPoolExecutor;
	}
    
    
    // 执行 Runnable task 方法
    @Override
	public void execute(Runnable task) {
		Executor executor = getThreadPoolExecutor();
		try {
			executor.execute(task);
		}
		catch (RejectedExecutionException ex) {
			throw new TaskRejectedException("Executor [" + executor + "] did not accept task: " + task, ex);
		}
	}
```



可以看到:

1. `ThreadPoolTaskExecutor` 内部维护了一个 `ThreadPoolExecutor`
2. `corePoolSize`, `maxPoolSize`, `keepAliveSeconds` 等参数都是为 `ThreadPoolExecutor` 设置的
3. 执行 `Runable` task 时,都是利用 `ThreadPoolExecutor` 执行的







![](https://typora-covey.oss-cn-shanghai.aliyuncs.com/img/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20220302173635.jpg)