# spring+quartz实现定时任务

1. 导入jar包

   ```xml
   <dependency>
     <groupId>org.quartz-scheduler</groupId>
     <artifactId>quartz</artifactId>
     <version>2.2.3</version>
   </dependency>
   ```

2. 实现需要执行的任务

   ```java
   public class CronJob {
   	
   	public void execute(){
   		System.out.println("I am CronJob");
   	}
   }
   ```


3. spring-quartz.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
   	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.1.xsd">

   	<!-- 加载job bean -->
   	<bean id="myJob" class="com.inspur.SSM.quartz.CronJob" />

   	<!-- 将job bean 封装成method -->
   	<bean id="myMethod"
   		class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
   		<property name="targetObject">
   			<ref bean="myJob" />
   		</property>
   		<property name="targetMethod">
   			<value>execute</value>
   		</property>
   	</bean>

   	<!-- 触发器 -->
   	<bean id="triggerBean"
   		class="org.springframework.scheduling.quartz.CronTriggerFactoryBean">
   		<property name="jobDetail" ref="myMethod"></property>
   		<property name="cronExpression" value="* * * * * ?"></property><!-- 秒 分 小时 日 
   			月 周 年 -->
   	</bean>

   	<!-- 调度工厂 -->
   	<bean id="schedulerBean"
   		class="org.springframework.scheduling.quartz.SchedulerFactoryBean"
   		lazy-init="false">
   		<property name="triggers">
   			<list>
   				<ref bean="triggerBean" />
   			</list>
   		</property>
   	</bean>

   </beans>
   ```