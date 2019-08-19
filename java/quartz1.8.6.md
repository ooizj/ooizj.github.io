# quartz1.8.6

## quartz1.8.6的简单使用

### 添加maven依赖

```markup
<dependency>
	    <groupId>junit</groupId>
	    <artifactId>junit</artifactId>
	    <version>4.12</version>
	    <scope>test</scope>
	</dependency>
	
	<dependency>
	    <groupId>org.slf4j</groupId>
	    <artifactId>slf4j-simple</artifactId>
	    <version>1.7.26</version>
	</dependency>
	
	<dependency>
	    <groupId>org.quartz-scheduler</groupId>
	    <artifactId>quartz</artifactId>
	    <version>1.8.6</version>
	</dependency>
```

### 新增quartz.properties到classpath

```yaml
org.quartz.scheduler.instanceName = MySchedulerTest
org.quartz.scheduler.instanceId = 1
org.quartz.scheduler.rmi.export = false
org.quartz.scheduler.rmi.proxy = false

org.quartz.threadPool.class = org.quartz.simpl.SimpleThreadPool
org.quartz.threadPool.threadCount = 20

org.quartz.jobStore.class = org.quartz.simpl.RAMJobStore
```

### 测试一下

```java
private Scheduler scheduler ; 
	
	@Before
	public void init() throws SchedulerException{
		scheduler = StdSchedulerFactory.getDefaultScheduler() ; 
		scheduler.start();
	}
	
	@After
	public void destroy() throws SchedulerException{
		scheduler.shutdown();
	}
	
	@Test
	public void hello() throws SchedulerException{
		System.out.println("MetaData:\n"+scheduler.getMetaData());
	}
```

### 指定时间运行

```java
	public static class TestJob implements Job {

		@Override
		public void execute(JobExecutionContext context) throws JobExecutionException {
			System.out.println("test data:\n"+context.getJobDetail().getJobDataMap().get("testKey"));
		}
		
	}
	
	@Test
	public void testJob() throws SchedulerException{
		// Define job instance
		JobDetail job = new JobDetail("job1", "group1", TestJob.class);
		
		//put test data
		job.getJobDataMap().put("testKey", "哈哈哈") ; 

		// Define a Trigger that will fire "now"
		Trigger trigger = new SimpleTrigger("trigger1", "group1", new Date());

		// Schedule the job with the trigger
		scheduler.scheduleJob(job, trigger);
	}
	
	//指定时间运行（如果scheduler.scheduleJob的时候，指定的时间小于当前时间会立刻执行）
	@Test
	public void testJob1() throws SchedulerException{
		// Define job instance
		JobDetail job = new JobDetail("job1", "group1", TestJob.class);
		
		//put test data
		job.getJobDataMap().put("testKey", "哈哈哈") ; 

		// Define a Trigger that will fire "now"
		Trigger trigger = null;
		try {
			trigger = new SimpleTrigger("trigger1", "group1", 
					//如果时间小于当前时间会立刻执行
					new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").parse("2018-08-01 15:10:50"));
		} catch (ParseException e) {
			LOG.error(e.getMessage(), e);
		}
		
		System.out.println(scheduler.getJobDetail("job1", "group1")); // output: null

		// Schedule the job with the trigger
		scheduler.scheduleJob(job, trigger);
		
		System.out.println(scheduler.getJobDetail("job1", "group1")); // output: JobDetail 'group1.job1' ...
		
		//Keep main thread alive 
		try {
			Thread.sleep(1000*10);
		} catch (InterruptedException e) {
		}
		
		System.out.println(scheduler.getJobDetail("job1", "group1")); // output: null 
	}
```

### 指定时间周期执行

```java
//测试CronTrigger
	@Test
	public void testCronTrigger() throws SchedulerException{
		// Define job instance
		JobDetail job = new JobDetail("job1", "group1", TestJob.class);
		
		//put test data
		job.getJobDataMap().put("testKey", "哈哈哈") ; 

		// Define a CronTrigger
		Trigger trigger = null;
		try {
			trigger = new CronTrigger("trigger1", "group1", "0/3 * * * * ?");
		} catch (ParseException e) {
			LOG.error(e.getMessage(), e);
		} 
		
		// Schedule the job with the trigger
		scheduler.scheduleJob(job, trigger);
		
		//Keep main thread alive 10s
		try {
			Thread.sleep(1000*10);
		} catch (InterruptedException e) {
		}
	}
```

### 测试暂停&恢复

```java
public static class TestJob2 implements Job {

		@Override
		public void execute(JobExecutionContext context) throws JobExecutionException {
//			System.out.println("before-------------------");
//			try { Thread.sleep(1000*3); } catch (InterruptedException e) { }
//			System.out.println("after-------------------");
			System.out.println("execute");
		}
		
	}
	
	//测试暂停&恢复
	@Test
	public void testPauseAndResume() throws SchedulerException{
		// Define job instance
		JobDetail job = new JobDetail("job1", "group1", TestJob2.class);
		
		//put test data
		job.getJobDataMap().put("testKey", "哈哈哈") ; 

		// Define a CronTrigger
		Trigger trigger = null;
		try {
			//每1秒运行一次
			trigger = new CronTrigger("trigger1", "group1", "*/1 * * * * ?");
		} catch (ParseException e) {
			LOG.error(e.getMessage(), e);
		} 
		
		// Schedule the job with the trigger
		scheduler.scheduleJob(job, trigger);
		
		//先运行3秒钟
		try { Thread.sleep(1000*3); } catch (InterruptedException e) { }
		
		//暂停
		if( LOG.isInfoEnabled() ){
			LOG.info("暂停");
		}
		scheduler.pauseJob("job1", "group1");
		
		//6秒后恢复运行
		try { Thread.sleep(1000*6); } catch (InterruptedException e) { }
		if( LOG.isInfoEnabled() ){
			LOG.info("恢复运行");
		}
		scheduler.resumeJob("job1", "group1");
		
		//再运行一会
		try { Thread.sleep(1000*5); } catch (InterruptedException e) { }
	}
```

### 重新配置触发时机

```java
//修改Trigger
	@Test
	public void reconfigTrigger() throws SchedulerException{
		JobDetail job = new JobDetail("job1", "group1", TestJob2.class);
		job.getJobDataMap().put("testKey", "哈哈哈") ; 
		
		// Define a CronTrigger
		Trigger trigger = null;
		try {
			//每1秒运行一次
			trigger = new CronTrigger("trigger1", "group1", "*/1 * * * * ?");
		} catch (ParseException e) {
			LOG.error(e.getMessage(), e);
		} 
		
		// Schedule the job with the trigger
		scheduler.scheduleJob(job, trigger);
		
		//先运行5秒钟
		try { Thread.sleep(1000*5); } catch (InterruptedException e) { }
		
		//修改Trigger
		if( LOG.isInfoEnabled() ){
			LOG.info("修改Trigger");
		}
		Trigger newTrigger = null;
		try {
			newTrigger = new CronTrigger("newTrigger", "group1", "*/3 * * * * ?");
		} catch (ParseException e) {
			LOG.error(e.getMessage(), e);
		} 
		//must set job's name and group
		newTrigger.setJobName("job1");
		newTrigger.setJobGroup("group1");
		scheduler.rescheduleJob("trigger1", "group1", newTrigger) ; 
		
		//再运行一会
		try { Thread.sleep(1000*15); } catch (InterruptedException e) { }
	}
```

### 按照频率执行

```java
//DateIntervalTrigger，按照某个频率去触发定时器
	@Test
	public void testDateIntervalTrigger() throws SchedulerException{
		JobDetail job = new JobDetail("job1", "group1", TestJob2.class);
		job.getJobDataMap().put("testKey", "哈哈哈") ; 
		
		// Define a CronTrigger
		Trigger trigger = null;
		//每1秒运行一次
		trigger = new DateIntervalTrigger("trigger", "group1", IntervalUnit.SECOND, 1) ; 
		
		// Schedule the job with the trigger
		scheduler.scheduleJob(job, trigger);
		
		//先运行6秒钟
		try { Thread.sleep(1000*6); } catch (InterruptedException e) { }
	}
```

