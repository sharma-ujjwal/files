SRVE0315E: An exception occurred: java.lang.Throwable: javax.servlet.ServletException: Service Tracker has not been initialized
at com.ibm.ws.webcontainer.webapp.WebApp.handleRequest(WebApp.java:5154)
[INFO]  at [internal classes]
[INFO] Caused by: javax.servlet.ServletException: Service Tracker has not been initialized
[INFO]  at javax.faces.webapp.FacesServlet.service(FacesServlet.java:220)
[INFO]  at [internal classes]
[INFO]  at com.assurant.inc.sox.ar.servlets.RedirectServlet.doPost(RedirectServlet.java:73)
[INFO]  at com.assurant.inc.sox.ar.servlets.BaseServlet.doGet(BaseServlet.java:17)
[INFO]  at javax.servlet.http.HttpServlet.service(HttpServlet.java:686)
[INFO]  ... 1 more
[INFO] Caused by: javax.faces.FacesException: Service Tracker has not been initialized
[INFO]  at org.richfaces.application.ServiceTracker.getServicesFactory(ServiceTracker.java:103)
[INFO]  at org.richfaces.application.ServiceTracker.getService(ServiceTracker.java:84)
[INFO]  at org.richfaces.application.ServiceTracker$1.invoke(ServiceTracker.java:149)
[INFO]  at jdk.proxy11/jdk.proxy11.$Proxy34.createResource(Unknown Source)
[INFO]  at org.richfaces.resource.ResourceFactoryImpl.resolveMappedResource(ResourceFactoryImpl.java:393)
[INFO]  at org.richfaces.resource.ResourceFactoryImpl.createMappedResource(ResourceFactoryImpl.java:360)
[INFO]  at org.richfaces.resource.ResourceFactoryImpl.createResource(ResourceFactoryImpl.java:343)
[INFO]  at org.richfaces.resource.ResourceHandlerImpl.createResource(ResourceHandlerImpl.java:266)
[INFO]  at org.richfaces.resource.ResourceHandlerImpl.createResource(ResourceHandlerImpl.java:280)
[INFO]  at org.apache.myfaces.shared.renderkit.html.util.ResourceUtils.renderMyfacesJSInlineIfNecessary(ResourceUtils.java:244)
[INFO]  ... 5 more
