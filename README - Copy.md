```
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
	<render-kit>
		<render-kit-id>HTML_BASIC</render-kit-id>
		<render-kit-class>org.apache.myfaces.renderkit.html.HtmlRenderKitImpl</render-kit-class>
	</render-kit>
	<application>
		<variable-resolver>org.springframework.web.jsf.DelegatingVariableResolver</variable-resolver>
	</application>
	<lifecycle>
		<phase-listener>com.assurant.inc.sox.ar.utils.LoggedInCheck</phase-listener>
	</lifecycle>
</faces-config>


<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.assurant.inc.sox.ar</groupId>
	<artifactId>soxautoreviews</artifactId>
	<packaging>war</packaging>
	<name>Sox AutoReviews War</name>
	<version>1.0.3</version>
	<description />
	<build>
		<finalName>soxautoreviews</finalName>
		<resources>
			<resource>
				<directory>${basedir}/src/main/resources</directory>
				<targetPath>${basedir}/WebContent/WEB-INF/classes</targetPath>
				<includes>
					<include>log4j.xml</include>
				</includes>
				<excludes>
					<exclude>**/*.java</exclude>
					<exclude>${basedir}/src/test</exclude>
				</excludes>
			</resource>
		</resources>
		<plugins>
			<plugin>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<source>${jdkTargetVersion}</source>
					<target>${jdkTargetVersion}</target>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
					<artifactId>maven-war-plugin</artifactId>
				<version>3.3.1</version>

				<configuration>
					<webResources>
						<resource>
							<directory>WebContent</directory>
						</resource>
					</webResources>
				</configuration>
			</plugin>
			<plugin>
				<artifactId>maven-dependency-plugin</artifactId>
				<executions>
					<execution>
						<id>copy-dependencies</id>
						<phase>validate</phase>
						<goals>
							<goal>copy-dependencies</goal>
						</goals>
						<configuration>
							<overWriteSnapshots>true</overWriteSnapshots>
							<outputDirectory>WebContent/WEB-INF/lib</outputDirectory>
							<excludeScope>provided</excludeScope>

						</configuration>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<artifactId>maven-source-plugin</artifactId>
				<configuration>
					<attach>true</attach>
				</configuration>
			</plugin>
			<plugin>
				<artifactId>maven-clean-plugin</artifactId>
				<executions>
					<execution>
						<id>clean</id>
						<phase>process-resources</phase>
						<goals>
							<goal>clean</goal>
						</goals>
					</execution>
				</executions>
				<configuration>
					<filesets>
						<fileset>
							<directory>WebContent/WEB-INF/lib</directory>
							<includes>
								<include>**/*.jar</include>
							</includes>
						</fileset>
						<fileset>
							<directory>src/main/webapp/WEB-INF/classes</directory>
							<includes>
								<include>*.*</include>
							</includes>
						</fileset>
					</filesets>
				</configuration>
			</plugin>
			<plugin>
				<artifactId>maven-surefire-plugin</artifactId>
				<configuration>
					<testFailureIgnore>true</testFailureIgnore>
					<skipTests>true</skipTests>
					<excludes>
						<exclude>**/Test*.java</exclude>
						<exclude>**/*Test.java</exclude>
						<exclude>**/*Mock.java</exclude>
					</excludes>
				</configuration>
			</plugin>
		</plugins>
	</build>
	<dependencies>
		<dependency>
			<groupId>com.assurant.inc.sox</groupId>
			<artifactId>SoxDataAccess</artifactId>
			<version>1.4.4.4</version>
		</dependency>
		<dependency>
			<groupId>org.richfaces</groupId>
			<artifactId>richfaces</artifactId>
			<version>${richfaces.version}</version>
		</dependency>
		<dependency>
			<groupId>org.apache.myfaces.core</groupId>
			<artifactId>myfaces-api</artifactId>
			<version>${myfaces.version}</version>
		</dependency>
		<dependency>
			<groupId>org.apache.myfaces.core</groupId>
			<artifactId>myfaces-impl</artifactId>
			<version>${myfaces.version}</version>
		</dependency>
		<dependency>
			<groupId>org.apache.commons</groupId>
			<artifactId>commons-lang3</artifactId>
			<version>${commons-lang.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<version>${spring.version}</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>jakarta.mail</groupId>
			<artifactId>jakarta.mail-api</artifactId>
			<version>2.1.3</version>
		</dependency>
		<dependency>
			<groupId>com.sun.jersey</groupId>
			<artifactId>jersey-json</artifactId>
			<version>${jersey.version}</version>
			<exclusions>
				<exclusion>
					<artifactId>jaxb-impl</artifactId>
					<groupId>com.sun.xml.bind</groupId>
				</exclusion>
				<exclusion>
					<artifactId>jackson-core-asl</artifactId>
					<groupId>org.codehaus.jackson</groupId>
				</exclusion>
				<exclusion>
					<artifactId>jackson-mapper-asl</artifactId>
					<groupId>org.codehaus.jackson</groupId>
				</exclusion>
				<exclusion>
					<artifactId>stax-api</artifactId>
					<groupId>stax</groupId>
				</exclusion>
				<exclusion>
					<artifactId>jackson-xc</artifactId>
					<groupId>org.codehaus.jackson</groupId>
				</exclusion>
				<exclusion>
					<artifactId>jackson-jaxrs</artifactId>
					<groupId>org.codehaus.jackson</groupId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>${spring.version}</version>
			<exclusions>
				<exclusion>
					<groupId>commons-logging</groupId>
					<artifactId>commons-logging</artifactId>
				</exclusion>
				<exclusion>
					<groupId>org.springframework</groupId>
					<artifactId>spring-asm</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aop</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjweaver</artifactId>
			<version>${aspectj.version}</version>
		</dependency>
		<dependency>
			<groupId>org.ow2.asm</groupId>
			<artifactId>asm</artifactId>
			<version>${asm.version}</version>
		</dependency>
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-core</artifactId>
			<version>${log4j.version}</version>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>${slf4j.version}</version>
		</dependency>
		<dependency>
			<groupId>net.sf.dozer</groupId>
			<artifactId>dozer</artifactId>
			<version>${dozer.version}</version>
		</dependency>
		<dependency>
			<groupId>jakarta.el</groupId>
			<artifactId>jakarta.el-api</artifactId>
			<version>${javax.el.version}</version>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>${javax.servlet.api.version}</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet.jsp</groupId>
			<artifactId>jsp-api</artifactId>
			<version>${jsp-api.version}</version>
		</dependency>
		<dependency>
			<groupId>commons-digester</groupId>
			<artifactId>commons-digester</artifactId>
			<version>${commons-digester.version}</version>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>${junit.version}</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>com.oracle.database.jdbc</groupId>
			<artifactId>ojdbc11</artifactId>
			<version>${ojdbc.version}</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.glassfish.expressly</groupId>
			<artifactId>expressly</artifactId>
			<version>${glassfish-el.version}</version>
		</dependency>
		<dependency>
			<groupId>commons-codec</groupId>
			<artifactId>commons-codec</artifactId>
			<version>${commons-codec.version}</version>
		</dependency>
		<dependency>
			<groupId>net.sf.ehcache</groupId>
			<artifactId>ehcache</artifactId>
			<version>${ehcache.version}</version>
		</dependency>
	</dependencies>
	<reporting>
		<plugins>
			<plugin>
				<artifactId>maven-javadoc-plugin</artifactId>
				<reportSets>
					<reportSet>
						<reports>
							<report>javadoc</report>
						</reports>
					</reportSet>
				</reportSets>
			</plugin>
			<plugin>
				<artifactId>maven-pmd-plugin</artifactId>
				<configuration>
					<targetJdk>${jdkTargetVersion}</targetJdk>
				</configuration>
			</plugin>
			<plugin>
				<artifactId>maven-jxr-plugin</artifactId>
				<reportSets>
					<reportSet>
						<reports>
							<report>jxr</report>
						</reports>
					</reportSet>
				</reportSets>
			</plugin>
			<plugin>
				<artifactId>maven-surefire-report-plugin</artifactId>
			</plugin>
			<plugin>
				<groupId>org.codehaus.mojo</groupId>
				<artifactId>cobertura-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</reporting>
	<properties>
		<log4j.version>1.3.15</log4j.version>
		<jdkTargetVersion>17</jdkTargetVersion>
		<spring.version>5.3.39</spring.version>
		<javax.mail.version>2.0.1</javax.mail.version>
		<richfaces.version>4.5.17.Final</richfaces.version>
		<myfaces.version>2.3.11</myfaces.version>
		<commons-lang.version>3.14.0</commons-lang.version>
		<jersey.version>1.19.4</jersey.version>
		<aspectj.version>1.9.7</aspectj.version>
		<slf4j.version>2.0.7</slf4j.version>
		<asm.version>9.5</asm.version>
		<dozer.version>5.5.1</dozer.version>
		<javax.el.version>6.0.1</javax.el.version>
		<jsp-api.version>2.2</jsp-api.version>
		<facelets.version>1.1.14</facelets.version>
		<jsf-api.version>2.2.20</jsf-api.version>
		<jsf-impl.version>1.2-20</jsf-impl.version>
		<commons-digester.version>2.1</commons-digester.version>
		<junit.version>4.13.2</junit.version>
		<ojdbc.version>21.9.0.0</ojdbc.version>
		<glassfish-el.version>5.0.0</glassfish-el.version>
		<commons-codec.version>1.15</commons-codec.version>
		<ehcache.version>2.10.9.2</ehcache.version>
		<javax.servlet.api.version>4.0.1</javax.servlet.api.version>
	</properties>
</project>

<?xml version="1.0" encoding="UTF-8"?>
<web-app version="3.1" xmlns="http://xmlns.jcp.org/xml/ns/javaee"
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd">

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
	<filter>
		<display-name>RichFaces Filter</display-name>
		<filter-name>richfaces</filter-name>
		<filter-class>org.richfaces.webapp.PushFilter</filter-class>
	</filter>

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
		<load-on-startup>2</load-on-startup>
	</servlet>
	<servlet>
		<display-name>SpringContextServlet</display-name>
		<servlet-name>SpringContextServlet</servlet-name>
		<servlet-class>org.springframework.web.context.ContextLoader</servlet-class>
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

[INFO] [ERROR   ] CWWKZ0002E: An exception occurred while starting the application soxautoreviews. The exception message was: com.ibm.ws.container.service.metadata.MetaDataException: SRVE0303E: Servlet name not found adding servlet mapping; servlet name=SpringContextServlet; URL pattern=/SpringContextServlet; module=soxautoreviews; application=soxautoreviews    


[3/7/25, 16:21:19:288 IST] 0000002d org.apache.myfaces.shared.util.ClassUtils                    E Class org.springframework.web.jsf.DelegatingVariableResolver not found
java.lang.ClassNotFoundException: org.springframework.web.jsf.DelegatingVariableResolver cannot be found by com.ibm.ws.org.apache.myfaces.2.3_1.0.98.cl250220250209-1902
	at org.eclipse.osgi.internal.loader.BundleLoader.generateException(BundleLoader.java:562)
	at org.eclipse.osgi.internal.loader.BundleLoader.findClass0(BundleLoader.java:557)
	at org.eclipse.osgi.internal.loader.BundleLoader.findClass(BundleLoader.java:434)
	at org.eclipse.osgi.internal.loader.ModuleClassLoader.loadClass(ModuleClassLoader.java:193)
	at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:520)
	at java.base/java.lang.Class.forName0(Native Method)
	at java.base/java.lang.Class.forName(Class.java:467)
	at org.apache.myfaces.shared.util.ClassUtils.classForName(ClassUtils.java:191)
	at org.apache.myfaces.shared.util.ClassUtils.simpleClassForName(ClassUtils.java:224)
	at org.apache.myfaces.shared.util.ClassUtils.simpleClassForName(ClassUtils.java:207)
	at org.apache.myfaces.shared.util.ClassUtils.buildApplicationObject(ClassUtils.java:562)
	at org.apache.myfaces.shared.util.ClassUtils.buildApplicationObject(ClassUtils.java:534)
	at org.apache.myfaces.config.FacesConfigurator.configureApplication(FacesConfigurator.java:871)
	at org.apache.myfaces.config.FacesConfigurator.configure(FacesConfigurator.java:561)
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

[3/7/25, 16:21:19:290 IST] 0000002d org.apache.myfaces.webapp.AbstractFacesInitializer           E An error occured while initializing MyFaces: java.lang.ClassNotFoundException: org.springframework.web.jsf.DelegatingVariableResolver cannot be found by com.ibm.ws.org.apache.myfaces.2.3_1.0.98.cl250220250209-1902
javax.faces.FacesException: java.lang.ClassNotFoundException: org.springframework.web.jsf.DelegatingVariableResolver cannot be found by com.ibm.ws.org.apache.myfaces.2.3_1.0.98.cl250220250209-1902
	at org.apache.myfaces.shared.util.ClassUtils.simpleClassForName(ClassUtils.java:231)
	at org.apache.myfaces.shared.util.ClassUtils.simpleClassForName(ClassUtils.java:207)
	at org.apache.myfaces.shared.util.ClassUtils.buildApplicationObject(ClassUtils.java:562)
	at org.apache.myfaces.shared.util.ClassUtils.buildApplicationObject(ClassUtils.java:534)
	at org.apache.myfaces.config.FacesConfigurator.configureApplication(FacesConfigurator.java:871)
	at org.apache.myfaces.config.FacesConfigurator.configure(FacesConfigurator.java:561)
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
Caused by: java.lang.ClassNotFoundException: org.springframework.web.jsf.DelegatingVariableResolver cannot be found by com.ibm.ws.org.apache.myfaces.2.3_1.0.98.cl250220250209-1902
	at org.eclipse.osgi.internal.loader.BundleLoader.generateException(BundleLoader.java:562)
	at org.eclipse.osgi.internal.loader.BundleLoader.findClass0(BundleLoader.java:557)
	at org.eclipse.osgi.internal.loader.BundleLoader.findClass(BundleLoader.java:434)
	at org.eclipse.osgi.internal.loader.ModuleClassLoader.loadClass(ModuleClassLoader.java:193)
	at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:520)
	at java.base/java.lang.Class.forName0(Native Method)
	at java.base/java.lang.Class.forName(Class.java:467)
	at org.apache.myfaces.shared.util.ClassUtils.classForName(ClassUtils.java:191)
	at org.apache.myfaces.shared.util.ClassUtils.simpleClassForName(ClassUtils.java:224)
	... 24 more


```
