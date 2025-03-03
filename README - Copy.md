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
```
