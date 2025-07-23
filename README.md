SRVE0279E: Error occured while processing global listeners for the application Engage: java.lang.NoClassDefFoundError: javax/servlet/ServletRequestListener
[INFO]  at java.base/java.lang.ClassLoader.defineClass1(Native Method)
[INFO]  at java.base/java.lang.ClassLoader.defineClass(ClassLoader.java:1012)
[INFO]  at com.ibm.ws.classloading.internal.AppClassLoader.definePackageAndClass(AppClassLoader.java:443)
[INFO]  at [internal classes]

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="6.0" xmlns="https://jakarta.ee/xml/ns/jakartaee"
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee
		 https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd">

<context-param>
		<param-name>jakarta.faces.CONFIG_FILES</param-name>
		<param-value>/WEB-INF/faces-config.xml</param-value>
	</context-param>
	<context-param>
		<param-name>jakarta.faces.DEFAULT_SUFFIX</param-name>
		<param-value>.xhtml</param-value>
	</context-param>
	<context-param>
		<param-name>facelets.DEVELOPMENT</param-name>
		<param-value>true</param-value>
	</context-param>
	<context-param>
		<param-name>jakarta.faces.STATE_SAVING_METHOD</param-name>
		<param-value>client</param-value>
	</context-param>
	<filter-mapping>
		<filter-name>IE8CompatablityFixServlet</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	<servlet>
		<servlet-name>Faces Servlet</servlet-name>
		<servlet-class>jakarta.faces.webapp.FacesServlet</servlet-class>
		<load-on-startup>0</load-on-startup>
	</servlet>
	<servlet>
		<display-name>Redirect</display-name>
		<servlet-name>Redirect</servlet-name>
		<servlet-class>main.java.com.example.RedirectServlet</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>Faces Servlet</servlet-name>
		<url-pattern>*.faces</url-pattern>
	</servlet-mapping>
	<servlet-mapping>
		<servlet-name>Redirect</servlet-name>
		<url-pattern>/redirect.html</url-pattern>
	</servlet-mapping>
	<session-config>
		<session-timeout>60</session-timeout>
	</session-config>
	<welcome-file-list>
		<welcome-file>redirect.html</welcome-file>
	</welcome-file-list>
	<login-config>
		<auth-method>BASIC</auth-method>
	</login-config>
</web-app>
