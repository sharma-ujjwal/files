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

<?xml version="1.0" encoding="UTF-8"?>
<web-app version="3.1"
		 xmlns="http://xmlns.jcp.org/xml/ns/javaee"
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd">

<display-name>SoxAutoReviews</display-name>
	<context-param>
		<param-name>org.richfaces.skin</param-name>
		<param-value>blueSky</param-value>
	</context-param>
	<context-param>
		<param-name>org.richfaces.resourceMapping.enabled</param-name>
		<param-value>true</param-value>
	</context-param>
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
<!--		<filter-class>org.richfaces.webapp.Filter</filter-class>-->
<!--	</filter>-->

	<filter-mapping>
		<filter-name>IE8CompatablityFixServlet</filter-name>
		<url-pattern>*.xhtml</url-pattern>
	</filter-mapping>
<!--	<filter-mapping>-->
<!--		<filter-name>richfaces</filter-name>-->
<!--		<servlet-name>Faces Servlet</servlet-name>-->
<!--		<dispatcher>REQUEST</dispatcher>-->
<!--		<dispatcher>FORWARD</dispatcher>-->
<!--		<dispatcher>INCLUDE</dispatcher>-->
<!--	</filter-mapping>-->
	<listener>
		<listener-class>org.springframework.web.context.request.RequestContextListener</listener-class>
	</listener>
	<listener>
		<listener-class>org.apache.myfaces.webapp.StartupServletContextListener
		</listener-class>
	</listener>
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
	<servlet>
		<servlet-name>RichFaces Resource Servlet</servlet-name>
		<servlet-class>org.richfaces.webapp.ResourceServlet</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>RichFaces Resource Servlet</servlet-name>
		<url-pattern>/org.richfaces.resources/*</url-pattern>
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


<faces-config
		xmlns="http://xmlns.jcp.org/xml/ns/javaee"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-facesconfig_2_3.xsd"
		version="2.3">
	<!--
		Navigations
	-->
	<navigation-rule>
		<from-view-id>/xhtml/*</from-view-id>
		<navigation-case>
			<from-outcome>taskList</from-outcome>
			<to-view-id>/xhtml/tasklist/tasklistPage.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>createBundle</from-outcome>
			<to-view-id>/xhtml/review/createBundle.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>createReview</from-outcome>
			<to-view-id>/xhtml/review/createReviewPage.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>dashboard</from-outcome>
			<to-view-id>/xhtml/dashboard/reviewManagementDashboard.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>adminConsole</from-outcome>
			<to-view-id>/xhtml/admin/departmentSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToApplicationTable</from-outcome>
			<to-view-id>/xhtml/admin/applicationSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToConflictsTable</from-outcome>
			<to-view-id>/xhtml/admin/conflictSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToConflictTypesTable</from-outcome>
			<to-view-id>/xhtml/admin/conflictTypeSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToDivisionsTable</from-outcome>
			<to-view-id>/xhtml/admin/divisionSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToDepartmentsTable</from-outcome>
			<to-view-id>/xhtml/admin/departmentSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToEnvironmentsTable</from-outcome>
			<to-view-id>/xhtml/admin/environmentSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToFunctionDutyTable</from-outcome>
			<to-view-id>/xhtml/admin/functionDutySummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToReviewOwnerTable</from-outcome>
			<to-view-id>/xhtml/admin/reviewOwnerSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToSodOwnerTable</from-outcome>
			<to-view-id>/xhtml/admin/sodOwnerSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToDataOwnerTable</from-outcome>
			<to-view-id>/xhtml/admin/dataOwnerSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToSoxConcernTable</from-outcome>
			<to-view-id>/xhtml/admin/soxConcernSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToUserStatusTable</from-outcome>
			<to-view-id>/xhtml/admin/userStatusSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToUserTypeTable</from-outcome>
			<to-view-id>/xhtml/admin/userTypeSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToExceptionReporting</from-outcome>
			<to-view-id>/xhtml/admin/exceptionSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToUserTable</from-outcome>
			<to-view-id>/xhtml/admin/userSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToPrivilegeComments</from-outcome>
			<to-view-id>/xhtml/admin/privCommentSummary.xhtml</to-view-id>
		</navigation-case>
		<!-- made this change
		<navigation-case>
			<from-outcome>updateFieldPrivComment</from-outcome>
			<to-view-id>/xhtml/admin/updateFieldPrivComment.xhtml</to-view-id>
		</navigation-case>-->
		<navigation-case>
			<from-outcome>rejectedUserSummary</from-outcome>
			<to-view-id>/xhtml/admin/rejectedUserSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToUserTable</from-outcome>
			<to-view-id>/xhtml/admin/userSummary.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>switchToRejectedUsers</from-outcome>
			<to-view-id>/xhtml/admin/rejectedUserSummary.xhtml</to-view-id>
		</navigation-case>
	</navigation-rule>
	<navigation-rule>
		<from-view-id>/xhtml/tasklist/tasklistPage.xhtml</from-view-id>
		<navigation-case>
			<from-outcome>employeeList</from-outcome>
			<to-view-id>/xhtml/reviewerReport/employeeList.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>reviewDetails</from-outcome>
			<to-view-id>/xhtml/reviewDetails/reviewDetailsPage.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>bundlePreview</from-outcome>
			<to-view-id>/xhtml/review/reviewBundleSummaryPage.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>actionRequired</from-outcome>
			<to-view-id>/xhtml/actionRequired/actionRequired.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>rejectedUser</from-outcome>
			<to-view-id>/xhtml/actionRequired/rejectUserPage.xhtml</to-view-id>
		</navigation-case>
	</navigation-rule>
	<navigation-rule>
		<from-view-id>/xhtml/reviewDetails/reviewDetailsPage.xhtml</from-view-id>
		<navigation-case>
			<from-outcome>employeeList</from-outcome>
			<to-view-id>/xhtml/reviewerReport/employeeList.xhtml</to-view-id>
		</navigation-case>
	</navigation-rule>
	<navigation-rule>
		<from-view-id>/xhtml/reviewerReport/employeeList.xhtml</from-view-id>
		<navigation-case>
			<from-outcome>accessList</from-outcome>
			<to-view-id>/xhtml/reviewerReport/accessList.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>reviewDetails</from-outcome>
			<to-view-id>/xhtml/reviewDetails/reviewDetailsPage.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>printEmployeeList</from-outcome>
			<to-view-id>/xhtml/reviewerReport/printEmployeeList.xhtml</to-view-id>
		</navigation-case>
	</navigation-rule>
	<navigation-rule>
		<from-view-id>/xhtml/reviewerReport/accessList.xhtml</from-view-id>
		<navigation-case>
			<from-outcome>employeeList</from-outcome>
			<to-view-id>/xhtml/reviewerReport/employeeList.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>accessList</from-outcome>
			<to-view-id>/xhtml/reviewerReport/accessList.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>reviewUserDashboard</from-outcome>
			<to-view-id>/xhtml/dashboard/reviewUserDashboard.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>printAccessList</from-outcome>
			<to-view-id>/xhtml/reviewerReport/printAccessList.xhtml</to-view-id>
		</navigation-case>
	</navigation-rule>
	<navigation-rule>
		<from-view-id>/xhtml/dashboard/reviewManagementDashboard.xhtml</from-view-id>
		<navigation-case>
			<from-outcome>selectReview</from-outcome>
			<to-view-id>/xhtml/dashboard/reviewDashboard.xhtml</to-view-id>
		</navigation-case>
	</navigation-rule>
	<navigation-rule>
		<from-view-id>/xhtml/dashboard/reviewDashboard.xhtml</from-view-id>
		<navigation-case>
			<from-outcome>selectReviewer</from-outcome>
			<to-view-id>/xhtml/dashboard/reviewUserDashboard.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>reviewMgmtDashboard</from-outcome>
			<to-view-id>/xhtml/dashboard/reviewManagementDashboard.xhtml</to-view-id>
		</navigation-case>
	</navigation-rule>
	<navigation-rule>
		<from-view-id>/xhtml/dashboard/reviewUserDashboard.xhtml</from-view-id>
		<navigation-case>
			<from-outcome>accessList</from-outcome>
			<to-view-id>/xhtml/reviewerReport/accessList.xhtml</to-view-id>
		</navigation-case>
		<navigation-case>
			<from-outcome>reviewDashboard</from-outcome>
			<to-view-id>/xhtml/dashboard/reviewDashboard.xhtml</to-view-id>
		</navigation-case>
	</navigation-rule>
	<navigation-rule>
		<from-view-id>/xhtml/reviewerReport/rejectReviewUsersModalPanelComponent.xhtml</from-view-id>
		<navigation-case>
			<from-outcome>reportSummary</from-outcome>
			<to-view-id>/xhtml/reviewerReport/employeeList.xhtml</to-view-id>
		</navigation-case>
	</navigation-rule>
	<lifecycle>
		<phase-listener>com.assurant.inc.sox.ar.utils.LoggedInCheck</phase-listener>
	</lifecycle>
	<application>
		<el-resolver>org.springframework.web.jsf.el.SpringBeanFacesELResolver</el-resolver>
	</application>
</faces-config>

