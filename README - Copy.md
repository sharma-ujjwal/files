```
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:jee="http://www.springframework.org/schema/jee"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:util="http://www.springframework.org/schema/util"
	xsi:schemaLocation="
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-2.5.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-2.5.xsd
		http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-2.5.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-2.5.xsd
		http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-2.5.xsd">
	<context:annotation-config/>
	<context:component-scan base-package="com.assurant.inc.sox"/>
	<aop:aspectj-autoproxy/>
	<tx:annotation-driven/>
	<context:mbean-export/>

	<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
		<property name="driverClassName">
			<value>oracle.jdbc.driver.OracleDriver</value>
		</property>
		<property name="url">
			<!--Dev-->
			<value>jdbc:oracle:thin:@l3d1ora02:1551:fd30</value>
			<!--Stage-->
			<!--<value>jdbc:oracle:thin:@l3d1ora02:1551:fm30</value>-->
		</property>
		<property name="username">
			<value>WEBSPHR_SOX</value>
		</property>
		<property name="password">
			<value>AEWBSPHR</value>
		</property>
	</bean>

	<bean class="org.springframework.orm.hibernate5.LocalSessionFactoryBean"
		id="sessionFactory" p:dataSource-ref="dataSource"
		p:annotatedPackages="com.assurant.inc.sox.domain" p:annotatedClasses-ref="hibernateClasses">
		<property name="hibernateProperties">
			<props>
				<prop key="hibernate.dialect">org.hibernate.dialect.Oracle10gDialect</prop>
				<prop key="hibernate.show_sql">false</prop>
				<prop key="hibernate.format_sql">false</prop>
				<prop key="hibernate.use_sql_comments">false</prop>
			</props>
		</property>
	</bean>

	<bean id="transactionManager"
		class="org.springframework.orm.hibernate5.HibernateTransactionManager">
		<property name="sessionFactory" ref="sessionFactory" />
	</bean>

	<util:list id="hibernateClasses">
		<value type="java.lang.Class">com.assurant.inc.sox.domain.security.SecurityFunction</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.security.SecurityFunctionAccess</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.security.SecurityRole</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.luad.PrivilegeComment</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.luad.User</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.luad.UserAccess</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.Application</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.ApplicationSystem</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.Department</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.Division</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.Environment</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.FilterCriteria</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.FilterType</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.Review</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.Reviewer</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.ReviewBundle</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.ReviewOwner</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.ReviewQueue</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.ReviewerReassignment</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.ReviewUser</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.ReviewUserAccess</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.SoxConcern</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.UserStatus</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.UserType</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.Supervisor</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.ReviewApplication</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.ReviewWorkOrder</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.ReviewUserAccessWO</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.ReviewApplicationWO</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.ReviewUserWO</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.Code</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.Conflict</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.ConflictType</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.ProcessLock</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.ar.FunctionDuty</value>
	</util:list>

	<bean id="dataSourceSavvion" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
		<property name="driverClassName">
			<value>oracle.jdbc.driver.OracleDriver</value>
		</property>
		<property name="url">
			<!-- Dev -->
			<value>jdbc:oracle:thin:@l3d1ora02:1581:fd3n</value>
		</property>
		<property name="username">
			<value>EBMS</value>
		</property>
		<property name="password">
			<value>savv10n</value>
		</property>
	</bean>

	<util:list id="hibernateClassesSavvion">
		<value type="java.lang.Class">com.assurant.inc.sox.domain.savvion.SavvionVerifyAccess</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.savvion.SavvionScheduleReviews</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.savvion.SavvionRejectedUser</value>
		<value type="java.lang.Class">com.assurant.inc.sox.domain.savvion.SavvionRejectedAccesses</value>
	</util:list>

	<!-- <bean class="org.springframework.orm.hibernate5.annotation.AnnotationSessionFactoryBean"
		id="sessionFactorySavvion" p:dataSource-ref="dataSourceSavvion"
		p:annotatedClasses-ref="hibernateClassesSavvion">
		<property name="hibernateProperties">
			<props>
				<prop key="hibernate.dialect">org.hibernate.dialect.Oracle10gDialect</prop>
				<prop key="hibernate.show_sql">true</prop>
			</props>
		</property>
	</bean> -->

	<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
		<constructor-arg ref="dataSource" />
	</bean>
</beans>



<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="SOX_DATA_ACCESS_LOG" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${com.ibm.ws.logging.log.directory}/SoxDataAccess.log</file>
    <rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
      <FileNamePattern>${com.ibm.ws.logging.log.directory}/SoxDataAccess.%d{yyyy-MM-dd}.%i.log</FileNamePattern>
      <FileNamePattern>/temp/logs/SoxDataAccess.log.%i</FileNamePattern>
      <MinIndex>1</MinIndex>
      <MaxIndex>5</MaxIndex>
    </rollingPolicy>
    <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
      <MaxFileSize>10000KB</MaxFileSize>
    </triggeringPolicy>
    <encoder>
      <Pattern>%date %level [%thread] [%file:%line] %msg%n</Pattern>
    </encoder>
  </appender>
  <!-- AEB Logger -->
  <logger name="com.assurant.inc" additivity="false">
    <level value="debug"/>
    <appender-ref ref="SOX_AUTO_REVIEW_LOG"/>
  </logger>
  <!-- Hibernate Logger -->
  <logger name="org.hibernate.SQL" level="OFF" additivity="false">
    <appender-ref ref="SOX_AUTO_REVIEW_LOG"/>
  </logger>
  <logger name="org.hibernate" additivity="false">
    <level value="WARN"/>
    <appender-ref ref="SOX_AUTO_REVIEW_LOG"/>
  </logger>
  <!-- Apache Logger -->
  <logger name="org.apache" additivity="false">
    <level value="error"/>
    <appender-ref ref="SOX_AUTO_REVIEW_LOG"/>
  </logger>
  <!-- Root Logger -->
  <root level="debug">
    <appender-ref ref="SOX_DATA_ACCESS_LOG"/>
  </root>
  <root level="debug">
    <appender-ref ref="SOX_DATA_ACCESS_LOG"/>
    <appender-ref ref="STDOUT"/>
  </root>
</configuration>
