```
org.apache.myfaces.spi.InjectionProviderException: java.lang.RuntimeException: com.ibm.wsspi.injectionengine.InjectionException: SRVE8061E: Unable to invoke method --> [public void com.assurant.inc.sox.ar.utils.LoggedInCheck.beforePhase(javax.faces.event.PhaseEvent)] on class --> [class com.assurant.inc.sox.ar.utils.LoggedInCheck].
	at com.ibm.ws.jsf.cdi.WASCDIAnnotationInjectionProvider.inject(WASCDIAnnotationInjectionProvider.java:75)
	at com.ibm.ws.jsf.spi.impl.WASCDIAnnotationDelegateInjectionProvider.inject(WASCDIAnnotationDelegateInjectionProvider.java:52)
	at org.apache.myfaces.config.FacesConfigurator.configureLifecycle(FacesConfigurator.java:1387)
	at org.apache.myfaces.config.FacesConfigurator.configure(FacesConfigurator.java:571)
	at org.apache.myfaces.webapp.AbstractFacesInitializer.buildConfiguration(AbstractFacesInitializer.java:456)
	at org.apache.myfaces.webapp.Jsp21FacesInitializer.initContainerIntegration(Jsp21FacesInitializer.java:70)
	at org.apache.myfaces.webapp.AbstractFacesInitializer.initFaces(AbstractFacesInitializer.java:190)
	at org.apache.myfaces.webapp.StartupServletContextListener.contextInitialized(StartupServletContextListener.java:103)
	at com.ibm.ws.webcontainer.webapp.WebApp.notifyServletContextCreated(WebApp.java:2464)
	at com.ibm.ws.webcontainer31.osgi.webapp.WebApp31.notifyServletContextCreated(WebApp31.java:512)
	at com.ibm.ws.webcontainer.webapp.WebApp.initialize(WebApp.java:1062)
	at com.ibm.ws.webcontainer.webapp.WebApp.initialize(WebApp.java:6722)
	at com.ibm.ws.webcontainer.osgi.DynamicVirtualHost.startWebApp(DynamicVirtualHost.java:484)
	at com.ibm.ws.webcontainer.osgi.DynamicVirtualHost.startWebApplication(DynamicVirtualHost.java:479)
	at com.ibm.ws.webcontainer.osgi.WebContainer.startWebApplication(WebContainer.java:1208)
	at com.ibm.ws.webcontainer.osgi.WebContainer.access$100(WebContainer.java:113)
	at com.ibm.ws.webcontainer.osgi.WebContainer$3.run(WebContainer.java:996)
	at com.ibm.ws.threading.internal.ExecutorServiceImpl$RunnableWrapper.run(ExecutorServiceImpl.java:298)
	at java.base/java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:539)
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1136)
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:635)
	at java.base/java.lang.Thread.run(Thread.java:833)
Caused by: java.lang.RuntimeException: com.ibm.wsspi.injectionengine.InjectionException: SRVE8061E: Unable to invoke method --> [public void com.assurant.inc.sox.ar.utils.LoggedInCheck.beforePhase(javax.faces.event.PhaseEvent)] on class --> [class com.assurant.inc.sox.ar.utils.LoggedInCheck].
	at com.ibm.wsspi.webcontainer.annotation.AnnotationHelper.inject(AnnotationHelper.java:93)
	at com.ibm.wsspi.webcontainer.annotation.AnnotationHelper.inject(AnnotationHelper.java:110)
	at com.ibm.ws.jsf.cdi.WASCDIAnnotationInjectionProvider.inject(WASCDIAnnotationInjectionProvider.java:72)
	... 22 more
Caused by: com.ibm.wsspi.injectionengine.InjectionException: SRVE8061E: Unable to invoke method --> [public void com.assurant.inc.sox.ar.utils.LoggedInCheck.beforePhase(javax.faces.event.PhaseEvent)] on class --> [class com.assurant.inc.sox.ar.utils.LoggedInCheck].
	at com.ibm.ws.webcontainer.webapp.WebApp.validateAndRun(WebApp.java:934)
	at com.ibm.ws.webcontainer.webapp.WebApp.invokeAnnotTypeOnObjectAndHierarchy(WebApp.java:879)
	at com.ibm.ws.webcontainer.osgi.webapp.WebApp.injectAndPostConstruct(WebApp.java:1361)
	at com.ibm.wsspi.webcontainer.annotation.AnnotationHelper.inject(AnnotationHelper.java:52)
	... 24 more



 An error occured while initializing MyFaces: class org.apache.myfaces.webapp.ManagedBeanDestroyerListener cannot be cast to class org.apache.myfaces.webapp.ManagedBeanDestroyerListener (org.apache.myfaces.webapp.ManagedBeanDestroyerListener is in unnamed module of loader com.ibm.ws.classloading.internal.AppClassLoader @73ca6c13; org.apache.myfaces.webapp.ManagedBeanDestroyerListener is in unnamed module of loader org.eclipse.osgi.internal.loader.EquinoxClassLoader @3403938f)
java.lang.ClassCastException: class org.apache.myfaces.webapp.ManagedBeanDestroyerListener cannot be cast to class org.apache.myfaces.webapp.ManagedBeanDestroyerListener (org.apache.myfaces.webapp.ManagedBeanDestroyerListener is in unnamed module of loader com.ibm.ws.classloading.internal.AppClassLoader @73ca6c13; org.apache.myfaces.webapp.ManagedBeanDestroyerListener is in unnamed module of loader org.eclipse.osgi.internal.loader.EquinoxClassLoader @3403938f)
	at org.apache.myfaces.config.FacesConfigurator.configureManagedBeanDestroyer(FacesConfigurator.java:1489)
	at org.apache.myfaces.config.FacesConfigurator.configure(FacesConfigurator.java:573)
	at org.apache.myfaces.webapp.AbstractFacesInitializer.buildConfiguration(AbstractFacesInitializer.java:456)
	at org.apache.myfaces.webapp.Jsp21FacesInitializer.initContainerIntegration(Jsp21FacesInitializer.java:70)
	at org.apache.myfaces.webapp.AbstractFacesInitializer.initFaces(AbstractFacesInitializer.java:190)
	at org.apache.myfaces.webapp.StartupServletContextListener.contextInitialized(StartupServletContextListener.java:103)
	at com.ibm.ws.webcontainer.webapp.WebApp.notifyServletContextCreated(WebApp.java:2464)
	at com.ibm.ws.webcontainer31.osgi.webapp.WebApp31.notifyServletContextCreated(WebApp31.java:512)
	at com.ibm.ws.webcontainer.webapp.WebApp.initialize(WebApp.java:1062)
	at com.ibm.ws.webcontainer.webapp.WebApp.initialize(WebApp.java:6722)
	at com.ibm.ws.webcontainer.osgi.DynamicVirtualHost.startWebApp(DynamicVirtualHost.java:484)
	at com.ibm.ws.webcontainer.osgi.DynamicVirtualHost.startWebApplication(DynamicVirtualHost.java:479)
	at com.ibm.ws.webcontainer.osgi.WebContainer.startWebApplication(WebContainer.java:1208)
	at com.ibm.ws.webcontainer.osgi.WebContainer.access$100(WebContainer.java:113)
	at com.ibm.ws.webcontainer.osgi.WebContainer$3.run(WebContainer.java:996)
	at com.ibm.ws.threading.internal.ExecutorServiceImpl$RunnableWrapper.run(ExecutorServiceImpl.java:298)
	at java.base/java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:539)
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1136)
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:635)
	at java.base/java.lang.Thread.run(Thread.java:833)

[3/3/25, 19:29:42:001 IST] 0000002b com.ibm.ws.webcontainer.webapp                               E SRVE0276E: Error while initializing Servlet [SpringContextServlet]: javax.servlet.UnavailableException: SRVE0201E: Servlet [org.springframework.web.context.ContextCleanupListener]: not a servlet class
	at com.ibm.ws.webcontainer.servlet.ServletWrapper$1.run(ServletWrapper.java:1564)
	at java.base/java.security.AccessController.doPrivileged(AccessController.java:569)
	at com.ibm.ws.webcontainer.servlet.ServletWrapper.loadServlet(ServletWrapper.java:1519)
	at com.ibm.ws.webcontainer.servlet.ServletWrapper.loadOnStartupCheck(ServletWrapper.java:1397)
	at com.ibm.ws.webcontainer.webapp.WebApp.doLoadOnStartupActions(WebApp.java:1228)
	at com.ibm.ws.webcontainer.webapp.WebApp.commonInitializationFinally(WebApp.java:1196)
	at com.ibm.ws.webcontainer.webapp.WebApp.initialize(WebApp.java:1094)
	at com.ibm.ws.webcontainer.webapp.WebApp.initialize(WebApp.java:6722)
	at com.ibm.ws.webcontainer.osgi.DynamicVirtualHost.startWebApp(DynamicVirtualHost.java:484)
	at com.ibm.ws.webcontainer.osgi.DynamicVirtualHost.startWebApplication(DynamicVirtualHost.java:479)
	at com.ibm.ws.webcontainer.osgi.WebContainer.startWebApplication(WebContainer.java:1208)
	at com.ibm.ws.webcontainer.osgi.WebContainer.access$100(WebContainer.java:113)
	at com.ibm.ws.webcontainer.osgi.WebContainer$3.run(WebContainer.java:996)
	at com.ibm.ws.threading.internal.ExecutorServiceImpl$RunnableWrapper.run(ExecutorServiceImpl.java:298)
	at java.base/java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:539)
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264)
	at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1136)
	at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:635)
	at java.base/java.lang.Thread.run(Thread.java:833)

CWWKZ0002E: An exception occurred while starting the application soxautoreviews. The exception message was: com.ibm.ws.container.service.metadata.MetaDataException: SRVE0303E: Servlet name not found adding servlet mapping; servlet name=SpringContextServlet; URL pattern=/SpringContextServlet; module=soxautoreviews; application=soxautoreviews



web.xml

<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
		 					http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
		 version="3.1">
	<display-name>SoxAutoReviews</display-name>
	<context-param>
		<param-name>javax.faces.DEFAULT_SUFFIX</param-name>
		<param-value>.xhtml</param-value>
	</context-param>
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/springDataConfig.xml</param-value>
	</context-param>
	<context-param>
		<param-name>facelets.DEVELOPMENT</param-name>
		<param-value>true</param-value>
	</context-param>
	 
	<filter>
		<filter-name>IE8CompatablityFixServlet</filter-name>
		<filter-class>
			com.assurant.inc.sox.ar.Filters.IE8CompatablityFixServlet</filter-class>
	</filter>
<!--	<filter>-->
<!--		<display-name>RichFaces Filter</display-name>-->
<!--		<filter-name>richfaces</filter-name>-->
<!--		<filter-class>org.ajax4jsf.Filter</filter-class>-->
<!--	</filter>-->

	<filter-mapping>
		<filter-name>IE8CompatablityFixServlet</filter-name>
		<url-pattern>*.xhtml</url-pattern>
	</filter-mapping>
	<filter-mapping>
		<filter-name>richfaces</filter-name>
		<servlet-name>Faces Servlet</servlet-name>
		<dispatcher>REQUEST</dispatcher>
		<dispatcher>FORWARD</dispatcher>
		<dispatcher>INCLUDE</dispatcher>
	</filter-mapping>

<!--	<listener>-->
<!--		<listener-class>com.sun.faces.config.ConfigureListener</listener-class>-->
<!--	</listener>-->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener
		</listener-class>
	</listener>
	<resource-ref>
		<res-ref-name>jdbc/SoxDataSource</res-ref-name>
		<res-type>javax.sql.DataSource</res-type>
		<res-auth>Container</res-auth>
		<res-sharing-scope>Shareable</res-sharing-scope>
	</resource-ref>
	<servlet>
		<servlet-name>Faces Servlet</servlet-name>
		<servlet-class>javax.faces.webapp.FacesServlet</servlet-class>
		<load-on-startup>2</load-on-startup>
	</servlet>
	<servlet>
		<display-name>SpringContextServlet</display-name>
		<servlet-name>SpringContextServlet</servlet-name>
		<servlet-class>org.springframework.web.context.ContextCleanupListener</servlet-class>
		<load-on-startup>1</load-on-startup>
	</servlet>
	<servlet>
		<display-name>Redirect</display-name>
		<servlet-name>Redirect</servlet-name>
		<servlet-class>com.assurant.inc.sox.ar.servlets.RedirectServlet</servlet-class>

	</servlet>
	<servlet-mapping>
		<servlet-name>Redirect</servlet-name>
		<url-pattern>/redirect.html</url-pattern>
	</servlet-mapping>
	<!--<servlet>
		<display-name>printEmployeeListServlet</display-name>
		<servlet-name>printEmployeeListServlet</servlet-name>
		<servlet-class>
			com.assurant.inc.sox.ar.servlets.printEmployeeListServlet</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>printEmployeeListServlet</servlet-name>
		<url-pattern>/printServlet</url-pattern>
	</servlet-mapping>-->
	<servlet-mapping>
		<servlet-name>Faces Servlet</servlet-name>
		<url-pattern>*.jsf</url-pattern>
	</servlet-mapping>
	<servlet-mapping>
		<servlet-name>Faces Servlet</servlet-name>
		<url-pattern>*.faces</url-pattern>
	</servlet-mapping>
	<servlet-mapping>
		<servlet-name>Faces Servlet</servlet-name>
		<url-pattern>*.xhtml</url-pattern>
	</servlet-mapping>
	<servlet-mapping>
		<servlet-name>SpringContextServlet</servlet-name>
		<url-pattern>/SpringContextServlet</url-pattern>
	</servlet-mapping>
	<servlet-mapping>
		<servlet-name>SpringContextServlet</servlet-name>
		<url-pattern>/ContextLoaderServlet</url-pattern>
	</servlet-mapping>
	<!-- <context-param>
		<param-name>org.richfaces.SKIN</param-name>
		<param-value>plain</param-value>
	</context-param>
	<context-param>
        <param-name>org.richfaces.LoadStyleStrategy</param-name>
        <param-value>ALL</param-value>
     </context-param>
     <context-param>
         <param-name>org.richfaces.LoadScriptStrategy</param-name>
         <param-value>ALL</param-value>
     </context-param> -->
	<welcome-file-list>
		<welcome-file>redirect.html</welcome-file>
	</welcome-file-list>
	<login-config>
		<auth-method>BASIC</auth-method>
	</login-config>
</web-app>


[INFO] --- maven-dependency-plugin:2.8:tree (default-cli) @ soxautoreviews ---
[INFO] com.assurant.inc.sox.ar:soxautoreviews:war:1.0.3
[INFO] +- com.assurant.inc.sox:SoxDataAccess:jar:1.4.4.4:compile
[INFO] |  +- org.springframework:spring-orm:jar:5.3.39:compile
[INFO] |  |  +- org.springframework:spring-jdbc:jar:5.3.39:compile
[INFO] |  |  \- org.springframework:spring-tx:jar:5.3.39:compile
[INFO] |  +- org.springframework:spring-beans:jar:5.3.39:compile
[INFO] |  +- javax.annotation:javax.annotation-api:jar:1.3.2:compile
[INFO] |  +- javax.persistence:javax.persistence-api:jar:2.2:compile
[INFO] |  +- ch.qos.logback:logback-classic:jar:1.3.15:compile
[INFO] |  +- com.fasterxml.jackson.core:jackson-databind:jar:2.15.4:compile
[INFO] |  |  \- com.fasterxml.jackson.core:jackson-annotations:jar:2.15.4:compile
[INFO] |  +- com.fasterxml.jackson.core:jackson-core:jar:2.18.2:compile
[INFO] |  +- commons-dbcp:commons-dbcp:jar:1.2.2:runtime
[INFO] |  |  \- commons-pool:commons-pool:jar:1.3:runtime
[INFO] |  +- org.dom4j:dom4j:jar:2.1.4:compile
[INFO] |  +- org.hibernate:hibernate-core:jar:5.6.15.Final:compile
[INFO] |  |  +- org.jboss.logging:jboss-logging:jar:3.4.3.Final:compile
[INFO] |  |  +- net.bytebuddy:byte-buddy:jar:1.12.18:compile
[INFO] |  |  +- antlr:antlr:jar:2.7.7:compile
[INFO] |  |  +- org.jboss.spec.javax.transaction:jboss-transaction-api_1.2_spec:jar:1.1.1.Final:compile
[INFO] |  |  +- org.jboss:jandex:jar:2.4.2.Final:compile
[INFO] |  |  +- com.fasterxml:classmate:jar:1.5.1:compile
[INFO] |  |  +- javax.activation:javax.activation-api:jar:1.2.0:compile
[INFO] |  |  +- javax.xml.bind:jaxb-api:jar:2.3.1:compile
[INFO] |  |  \- org.glassfish.jaxb:jaxb-runtime:jar:2.3.1:compile
[INFO] |  |     +- org.glassfish.jaxb:txw2:jar:2.3.1:compile
[INFO] |  |     +- com.sun.istack:istack-commons-runtime:jar:3.0.7:compile
[INFO] |  |     +- org.jvnet.staxex:stax-ex:jar:1.8:compile
[INFO] |  |     \- com.sun.xml.fastinfoset:FastInfoset:jar:1.2.15:compile
[INFO] |  +- com.oracle.database.jdbc:ojdbc10:jar:19.25.0.0:compile
[INFO] |  \- org.hibernate.common:hibernate-commons-annotations:jar:5.1.2.Final:compile
[INFO] +- org.richfaces:richfaces:jar:4.5.17.Final:compile
[INFO] |  +- org.richfaces:richfaces-core:jar:4.5.17.Final:compile
[INFO] |  |  +- net.sourceforge.cssparser:cssparser:jar:0.9.18:compile
[INFO] |  |  |  \- org.w3c.css:sac:jar:1.3:compile
[INFO] |  |  \- com.google.guava:guava:jar:19.0:compile
[INFO] |  \- org.richfaces:richfaces-a4j:jar:4.5.17.Final:compile
[INFO] +- org.apache.myfaces.core:myfaces-api:jar:2.3.11:compile
[INFO] +- org.apache.myfaces.core:myfaces-impl:jar:2.3.11:provided
[INFO] |  \- commons-beanutils:commons-beanutils:jar:1.9.4:compile
[INFO] |     \- commons-collections:commons-collections:jar:3.2.2:compile
[INFO] +- org.apache.commons:commons-lang3:jar:3.14.0:compile
[INFO] +- org.springframework:spring-test:jar:5.3.21:test
[INFO] +- jakarta.mail:jakarta.mail-api:jar:2.1.3:compile
[INFO] |  \- jakarta.activation:jakarta.activation-api:jar:2.1.3:compile
[INFO] +- com.sun.jersey:jersey-json:jar:1.19.4:compile
[INFO] |  +- org.codehaus.jettison:jettison:jar:1.1:compile
[INFO] |  \- com.sun.jersey:jersey-core:jar:1.19.4:compile
[INFO] |     \- javax.ws.rs:jsr311-api:jar:1.1.1:compile
[INFO] +- org.springframework:spring-context:jar:5.3.21:compile
[INFO] |  \- org.springframework:spring-expression:jar:5.3.21:compile
[INFO] +- org.springframework:spring-core:jar:5.3.21:compile
[INFO] |  \- org.springframework:spring-jcl:jar:5.3.21:compile
[INFO] +- org.springframework:spring-web:jar:5.3.21:compile
[INFO] +- org.springframework:spring-webmvc:jar:5.3.21:compile
[INFO] +- org.springframework:spring-aop:jar:5.3.21:compile
[INFO] +- org.aspectj:aspectjweaver:jar:1.9.7:compile
[INFO] +- org.ow2.asm:asm:jar:9.5:compile
[INFO] +- ch.qos.logback:logback-core:jar:1.3.15:compile
[INFO] +- org.slf4j:slf4j-api:jar:2.0.7:compile
[INFO] +- net.sf.dozer:dozer:jar:5.5.1:compile
[INFO] |  \- org.slf4j:jcl-over-slf4j:jar:1.7.5:compile
[INFO] +- jakarta.el:jakarta.el-api:jar:6.0.1:compile
[INFO] +- javax.servlet:javax.servlet-api:jar:4.0.1:provided
[INFO] +- javax.servlet.jsp:jsp-api:jar:2.2:compile
[INFO] +- com.sun.facelets:jsf-facelets:jar:1.1.14:compile
[INFO] +- com.sun.faces:jsf-api:jar:2.2.20:compile
[INFO] +- javax.faces:jsf-impl:jar:1.2-20:provided
[INFO] +- commons-digester:commons-digester:jar:2.1:compile
[INFO] |  \- commons-logging:commons-logging:jar:1.1.1:compile
[INFO] +- junit:junit:jar:4.13.2:test
[INFO] |  \- org.hamcrest:hamcrest-core:jar:1.3:test
[INFO] +- com.oracle.database.jdbc:ojdbc11:jar:21.9.0.0:test
[INFO] +- org.glassfish.expressly:expressly:jar:5.0.0:compile
[INFO] +- commons-codec:commons-codec:jar:1.15:compile
[INFO] \- net.sf.ehcache:ehcache:jar:2.10.9.2:compile
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 3.082 s
[INFO] Finished at: 2025-03-04T19:33:42+05:30
[INFO] Final Memory: 14M/60M


[INFO] com.assurant.inc.sox.ar:soxautoreviews:war:1.0.3
[INFO] +- com.assurant.inc.sox:SoxDataAccess:jar:1.4.4.4:compile
[INFO] |  +- org.springframework:spring-orm:jar:5.3.39:compile
[INFO] |  |  +- org.springframework:spring-jdbc:jar:5.3.39:compile
[INFO] |  |  \- org.springframework:spring-tx:jar:5.3.39:compile
[INFO] |  +- org.springframework:spring-beans:jar:5.3.39:compile
[INFO] |  +- javax.annotation:javax.annotation-api:jar:1.3.2:compile
[INFO] |  +- javax.persistence:javax.persistence-api:jar:2.2:compile
[INFO] |  +- ch.qos.logback:logback-classic:jar:1.3.15:compile
[INFO] |  +- com.fasterxml.jackson.core:jackson-databind:jar:2.18.2:compile
[INFO] |  |  \- com.fasterxml.jackson.core:jackson-annotations:jar:2.18.2:compile
[INFO] |  +- com.fasterxml.jackson.core:jackson-core:jar:2.18.2:compile
[INFO] |  +- commons-dbcp:commons-dbcp:jar:1.2.2:runtime
[INFO] |  |  \- commons-pool:commons-pool:jar:1.3:runtime
[INFO] |  +- org.dom4j:dom4j:jar:2.1.4:compile
[INFO] |  +- org.hibernate:hibernate-core:jar:5.6.15.Final:compile
[INFO] |  |  +- org.jboss.logging:jboss-logging:jar:3.4.3.Final:compile
[INFO] |  |  +- net.bytebuddy:byte-buddy:jar:1.12.18:compile
[INFO] |  |  +- antlr:antlr:jar:2.7.7:compile
[INFO] |  |  +- org.jboss.spec.javax.transaction:jboss-transaction-api_1.2_spec:jar:1.1.1.Final:compile
[INFO] |  |  +- org.jboss:jandex:jar:2.4.2.Final:compile
[INFO] |  |  +- com.fasterxml:classmate:jar:1.5.1:compile
[INFO] |  |  +- javax.activation:javax.activation-api:jar:1.2.0:compile
[INFO] |  |  +- javax.xml.bind:jaxb-api:jar:2.3.1:compile
[INFO] |  |  \- org.glassfish.jaxb:jaxb-runtime:jar:2.3.1:compile
[INFO] |  |     +- org.glassfish.jaxb:txw2:jar:2.3.1:compile
[INFO] |  |     +- com.sun.istack:istack-commons-runtime:jar:3.0.7:compile
[INFO] |  |     +- org.jvnet.staxex:stax-ex:jar:1.8:compile
[INFO] |  |     \- com.sun.xml.fastinfoset:FastInfoset:jar:1.2.15:compile
[INFO] |  \- org.hibernate.common:hibernate-commons-annotations:jar:5.1.2.Final:compile
[INFO] +- org.richfaces:richfaces:jar:4.5.17.Final:compile
[INFO] |  +- org.richfaces:richfaces-core:jar:4.5.17.Final:compile
[INFO] |  |  +- net.sourceforge.cssparser:cssparser:jar:0.9.18:compile
[INFO] |  |  |  \- org.w3c.css:sac:jar:1.3:compile
[INFO] |  |  \- com.google.guava:guava:jar:19.0:compile
[INFO] |  \- org.richfaces:richfaces-a4j:jar:4.5.17.Final:compile
[INFO] +- org.apache.myfaces.core:myfaces-api:jar:2.3.11:compile
[INFO] +- org.apache.myfaces.core:myfaces-impl:jar:2.3.11:provided
[INFO] |  \- commons-beanutils:commons-beanutils:jar:1.9.4:compile
[INFO] |     \- commons-collections:commons-collections:jar:3.2.2:compile
[INFO] +- org.apache.commons:commons-lang3:jar:3.14.0:compile
[INFO] +- org.springframework:spring-test:jar:5.3.39:test
[INFO] +- jakarta.mail:jakarta.mail-api:jar:2.1.3:compile
[INFO] |  \- jakarta.activation:jakarta.activation-api:jar:2.1.3:compile
[INFO] +- com.sun.jersey:jersey-json:jar:1.19.4:compile
[INFO] |  +- org.codehaus.jettison:jettison:jar:1.1:compile
[INFO] |  \- com.sun.jersey:jersey-core:jar:1.19.4:compile
[INFO] |     \- javax.ws.rs:jsr311-api:jar:1.1.1:compile
[INFO] +- org.springframework:spring-context:jar:5.3.39:compile
[INFO] |  \- org.springframework:spring-expression:jar:5.3.39:compile
[INFO] +- org.springframework:spring-core:jar:5.3.39:compile
[INFO] |  \- org.springframework:spring-jcl:jar:5.3.39:compile
[INFO] +- org.springframework:spring-web:jar:5.3.39:compile
[INFO] +- org.springframework:spring-webmvc:jar:5.3.39:compile
[INFO] +- org.springframework:spring-aop:jar:5.3.39:compile
[INFO] +- org.aspectj:aspectjweaver:jar:1.9.7:compile
[INFO] +- org.ow2.asm:asm:jar:9.5:compile
[INFO] +- ch.qos.logback:logback-core:jar:1.3.15:compile
[INFO] +- org.slf4j:slf4j-api:jar:2.0.7:compile
[INFO] +- net.sf.dozer:dozer:jar:5.5.1:compile
[INFO] |  \- org.slf4j:jcl-over-slf4j:jar:1.7.5:compile
[INFO] +- jakarta.el:jakarta.el-api:jar:6.0.1:compile
[INFO] +- javax.servlet:javax.servlet-api:jar:4.0.1:provided
[INFO] +- javax.servlet.jsp:jsp-api:jar:2.2:compile
[INFO] +- com.sun.facelets:jsf-facelets:jar:1.1.14:compile
[INFO] +- com.sun.faces:jsf-api:jar:2.2.20:compile
[INFO] +- javax.faces:jsf-impl:jar:1.2-20:provided
[INFO] +- commons-digester:commons-digester:jar:2.1:compile
[INFO] |  \- commons-logging:commons-logging:jar:1.1.1:compile
[INFO] +- junit:junit:jar:4.13.2:test
[INFO] |  \- org.hamcrest:hamcrest-core:jar:1.3:test
[INFO] +- com.oracle.database.jdbc:ojdbc11:jar:21.9.0.0:test
[INFO] +- org.glassfish.expressly:expressly:jar:5.0.0:compile
[INFO] +- commons-codec:commons-codec:jar:1.15:compile
[INFO] \- net.sf.ehcache:ehcache:jar:2.10.9.2:compile

[INFO] [ERROR   ] CWWKZ0002E: An exception occurred while starting the application soxautoreviews. The exception message was: com.ibm.ws.container.service.metadata.MetaDataException: SRVE0303E: Servlet name not found adding servlet mapping; servlet name=SpringContextServlet; URL pattern=/SpringContextServlet; module=soxautoreviews; application=soxautoreviews    

Caused by: javax.servlet.ServletException: Cannot invoke "javax.faces.render.RenderKit.getResponseStateManager()" because "renderKit" is null
[INFO]  at javax.faces.webapp.FacesServlet.service(FacesServlet.java:236)
[INFO]  at [internal classes]
[INFO]  at com.assurant.inc.sox.ar.servlets.RedirectServlet.doPost(RedirectServlet.java:73)
[INFO]  at com.assurant.inc.sox.ar.servlets.BaseServlet.doGet(BaseServlet.java:17)
[INFO]  at javax.servlet.http.HttpServlet.service(HttpServlet.java:686)
[INFO]  ... 1 more
[INFO] Caused by: java.lang.NullPointerException: Cannot invoke "javax.faces.render.RenderKit.getResponseStateManager()" because "renderKit" is null
[INFO]  at org.apache.myfaces.context.servlet.FacesContextImpl.isPostback(FacesContextImpl.java:415)
[INFO]  ... 5 more


```
