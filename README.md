```
Exception thrown by application class 'javax.faces.webapp.FacesServlet.service:236'
javax.servlet.ServletException: Could not Resolve Variable [Overflow]: rejectedUserSummaryBean
at javax.faces.webapp.FacesServlet.service(FacesServlet.java:236)
at [internal classes]
at com.assurant.inc.sox.ar.Filters.IE8CompatablityFixServlet.doFilter(IE8CompatablityFixServlet.java:27)
at com.ibm.ws.webcontainer.filter.FilterInstanceWrapper.doFilter(FilterInstanceWrapper.java:203)
at [internal classes]
Caused by: javax.el.ELException: Could not Resolve Variable [Overflow]: rejectedUserSummaryBean
at org.apache.myfaces.view.facelets.el.VariableMapperWrapper.resolveVariable(VariableMapperWrapper.java:96)
... 1 more
Caused by: java.lang.StackOverflowError:
at java.base/java.lang.Exception.(Exception.java:85)
at java.base/java.lang.RuntimeException.(RuntimeException.java:81)
at javax.el.ELException.(ELException.java:66)
... 1 more
