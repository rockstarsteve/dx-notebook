# spring-scheduling 动态定时任务



## 依赖

```
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-log4j2</artifactId>
        <optional>true</optional>
    </dependency>

    <!-- spring boot 2.3版本后，如果需要使用校验，需手动导入validation包-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>

    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

## 开启定时任务

@EnableScheduling



## 配置定时任务的配置文件

- 在resources下创建task-config.ini。内容如下：

```
printTime.cron=0/10 * * * * ?
```

- 使用定时任务（使用的1方式）

  1：读取ini文件中的方式

    @PropertySource("classpath:/task-config.ini")

  2：直接在application.yml中配置也是一样的

```
  @Data
  @Slf4j
  @Component
  @PropertySource("classpath:/task-config.ini")
  public class ScheduleTask implements SchedulingConfigurer {
   
      @Value("${printTime.cron}")
      private String cron;
   
      @Override
      public void configureTasks(ScheduledTaskRegistrar taskRegistrar) {
          // 动态使用cron表达式设置循环间隔
          taskRegistrar.addTriggerTask(new Runnable() {
              @Override
              public void run() {
                  log.info("Current time： {}", LocalDateTime.now());
              }
          }, new Trigger() {
              @Override
              public Date nextExecutionTime(TriggerContext triggerContext) {
                  // 使用CronTrigger触发器，可动态修改cron表达式来操作循环规则
                  CronTrigger cronTrigger = new CronTrigger(cron);
                  Date nextExecutionTime = cronTrigger.nextExecutionTime(triggerContext);
                  return nextExecutionTime;
              }
          });
      }
  }
```

  



- 用接口动态修改

```
@Autowrite
private ScheduleTask scheduleTask;

@GetMapping("/updateCron")
public String updateCron(String cron) {
    log.info("new cron :{}", cron);
    scheduleTask.setCron(cron);
    return "ok";
}
```

  























