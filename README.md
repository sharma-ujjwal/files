```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
	  xmlns:h="http://xmlns.jcp.org/jsf/html"
	  xmlns:p="http://primefaces.org/ui">
<h:head>
	<title>Engage Local Login Page</title>
</h:head>
<h:body>
	<h:form id="loginForm">
		<p:outputLabel value="Hello From Primefaces 15 Spring 6.2.8 #{loginActionListener.doLoginAction}"/>
		<h:outputText id="test" value="#{loginActionListener.doLoginAction}"/>
		<p:commandButton id="login" value="#{loginActionListener.doLoginAction}" />
	</h:form>
</h:body>
</html>

web.xml
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
		<param-name>primefaces.UPLOADER_CLEANER_DISABLED</param-name>
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
		<servlet-class>com.example.servlets.RedirectServlet</servlet-class>
	</servlet>
	<servlet-mapping>
		<servlet-name>Faces Servlet</servlet-name>
		<url-pattern>*.xhtml</url-pattern>
	</servlet-mapping>
	<servlet-mapping>
		<servlet-name>Redirect</servlet-name>
		<url-pattern>/redirect.html</url-pattern>
	</servlet-mapping>
	<session-config>
		<session-timeout>60</session-timeout>
	</session-config>
	<welcome-file-list>
		<welcome-file>login.xhtml</welcome-file>
	</welcome-file-list>
	<login-config>
		<auth-method>BASIC</auth-method>
	</login-config>
</web-app>

pom.xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
<modelVersion>4.0.0</modelVersion>

<!-- Versions -->
<properties>
    <jdkTargetVersion>17</jdkTargetVersion>
    <commonLogging>1.3.5</commonLogging>
    <slf4j>2.0.17</slf4j>
    <logback>1.5.18</logback>
    <primefaces>15.0.5</primefaces>
    <jakartaEl>6.0.1</jakartaEl>
    <searchbusinessservice>3.0</searchbusinessservice>
    <memberbusinessservice>2.0</memberbusinessservice>
    <policybusinessservice>2.0</policybusinessservice>
    <spring>6.2.8</spring>
    <jakartaServlet>5.0.0</jakartaServlet>
</properties>

<!-- The Basics -->
<groupId>sample_engage</groupId>
<artifactId>sample_engage</artifactId>
<version>3.0-SNAPSHOT</version>
<packaging>war</packaging>
<name>sample_engage</name>
<description>The war file containing the interface components for the Engage User Interface</description>
<dependencies>
    <dependency>
        <groupId>commons-beanutils</groupId>
        <artifactId>commons-beanutils</artifactId>
        <version>1.11.0</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>${slf4j}</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>log4j-over-slf4j</artifactId>
        <version>${slf4j}</version>
    </dependency>
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-core</artifactId>
        <version>${logback}</version>
    </dependency>
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>${logback}</version>
    </dependency>
    <dependency>
        <groupId>org.apache.myfaces.core</groupId>
        <artifactId>myfaces-impl</artifactId>
        <version>4.0.1</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>jakarta.faces</groupId>
        <artifactId>jakarta.faces-api</artifactId>
        <version>4.1.2</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.primefaces</groupId>
        <artifactId>primefaces</artifactId>
        <version>${primefaces}</version>
        <classifier>jakarta</classifier>
        <exclusions>
            <exclusion>
                <groupId>org.apache.myfaces.core</groupId>
                <artifactId>myfaces-impl</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.apache.myfaces.core</groupId>
                <artifactId>myfaces-core</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>jakarta.el</groupId>
        <artifactId>jakarta.el-api</artifactId>
        <version>6.0.1</version>
    </dependency>
    <dependency>
        <groupId>jakarta.servlet</groupId>
        <artifactId>jakarta.servlet-api</artifactId>
        <version>${jakartaServlet}</version>
    </dependency>

    <!-- CDI required APIs -->
    <dependency>
        <groupId>jakarta.inject</groupId>
        <artifactId>jakarta.inject-api</artifactId>
        <version>2.0.1</version>
    </dependency>
    <dependency>
        <groupId>jakarta.enterprise</groupId>
        <artifactId>jakarta.enterprise.cdi-api</artifactId>
        <version>4.1.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-lang3</artifactId>
        <version>3.18.0</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.thoughtworks.xstream</groupId>
        <artifactId>xstream</artifactId>
        <version>1.4.21</version>
    </dependency>
</dependencies>

<!-- Build Settings -->
<build>
    <finalName>Engage</finalName>
    <sourceDirectory>src/main</sourceDirectory>
    <testSourceDirectory>test</testSourceDirectory>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
        </resource>
        <resource>
            <directory>${basedir}/WebRoot/WEB-INF</directory>
            <includes>
                <include>web.xml</include>
            </includes>
            <filtering>true</filtering>
            <targetPath>${project.build.directory}</targetPath>
        </resource>
    </resources>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.14.0</version>
            <configuration>
                <source>${jdkTargetVersion}</source>
                <target>${jdkTargetVersion}</target>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.4.0</version>
            <configuration>
                <webResources>
                    <resource>
                        <directory>WebRoot</directory>
                    </resource>
                </webResources>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-source-plugin</artifactId>
            <version>3.3.1</version>
            <configuration>
                <attach>true</attach>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-clean-plugin</artifactId>
            <version>3.5.0</version>
            <configuration>
                <filesets>
                    <fileset>
                        <directory>WebRoot/WEB-INF/lib</directory>
                        <includes>
                            <include>*.jar</include>
                        </includes>
                    </fileset>
                    <fileset>
                        <directory>WebRoot/WEB-INF/classes</directory>
                        <includes>
                            <include>*.*</include>
                        </includes>
                    </fileset>
                </filesets>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-dependency-plugin</artifactId>
            <version>3.6.1</version>
            <configuration>
                <overWriteSnapshots>true</overWriteSnapshots>
                <outputDirectory>WebRoot/WEB-INF/lib</outputDirectory>
                <excludeScope>provided</excludeScope>
            </configuration>
        </plugin>
    </plugins>
</build>
<reporting>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-javadoc-plugin</artifactId>
            <version>3.10.0</version>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-pmd-plugin</artifactId>
            <version>3.21.0</version>
            <configuration>
                <targetJdk>${jdkTargetVersion}</targetJdk>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-jxr-plugin</artifactId>
            <version>3.3.1</version>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-report-plugin</artifactId>
            <version>3.5.3</version>
        </plugin>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>findbugs-maven-plugin</artifactId>
            <version>3.0.5</version>
            <configuration>
                <effort>Max</effort>
                <threshold>High</threshold>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>cobertura-maven-plugin</artifactId>
            <version>2.7</version>
        </plugin>
        <plugin>
            <groupId>com.assurant.inc.maven.plugins</groupId>
            <artifactId>annotationReporter</artifactId>
            <version>1.0</version>
            <reportSets>
                <reportSet>
                    <reports>
                        <report>report-annotations-html</report>
                    </reports>
                </reportSet>
            </reportSets>
        </plugin>
    </plugins>
</reporting>

<!-- More Project Information -->

<url>http://lkcmwiki1/srm/Engage</url>
<inceptionYear>2009</inceptionYear>
<organization>
    <name>AEB-SRM Java Development Team</name>
    <url>http://lkcmwiki1/srm/MyStartingPage</url>
</organization>
<developers>
    <developer>
        <id>mw20689</id>
        <name>Mike Webster</name>
        <email>mike.webster@assurant.com</email>
        <roles>
            <role>Developer</role>
        </roles>
        <organization>Assurant Employee Benefits</organization>
        <organizationUrl>www.assurantemployeebenefits.com</organizationUrl>
    </developer>
    <developer>
        <id>sz18995</id>
        <name>Shraddha Zope</name>
        <email>shraddha.zope@assurant.com</email>
        <roles>
            <role>Developer</role>
        </roles>
        <organization>Assurant Employee Benefits</organization>
        <organizationUrl>www.assurantemployeebenefits.com</organizationUrl>
    </developer>
</developers>

<!-- Environment Settings -->
<issueManagement>
    <system>QCExplorer</system>
    <url>http://165.245.175.93:8080/qcbin</url>
</issueManagement>
<ciManagement>
    <system>hudson</system>
    <url>http://mci0lsubd001:8180/hudson/</url>
    <notifiers>
        <notifier>
            <type>mail</type>
            <configuration>
                <recipients>mike.webster@assurant.com</recipients>
            </configuration>
            <sendOnError>true</sendOnError>
            <sendOnFailure>true</sendOnFailure>
            <sendOnSuccess>true</sendOnSuccess>
            <sendOnWarning>true</sendOnWarning>
        </notifier>
    </notifiers>
</ciManagement>
<scm>
    <connection>scm:svn:http://mci0lsubd001/svn/Engage/trunk/EngageWar</connection>
    <developerConnection>scm:svn:http://mci0lsubd001/svn/Engage/trunk/EngageWar</developerConnection>
    <url>http://mci0lsubd001/svn/Engage/trunk/EngageWar</url>
</scm>
<distributionManagement>
    <repository>
        <id>assurantDevRepository</id>
        <name>Assurant Internal Repository</name>
        <url>http://mci0lsubd001:8080/artifactory/libs-releases</url>
    </repository>
    <snapshotRepository>
        <id>assurantDevRepositorySnapshots</id>
        <name>Assurant Internal Repository</name>
        <url>http://mci0lsubd001:8080/artifactory/libs-snapshots</url>
    </snapshotRepository>
    <site>
        <id>website</id>
        <url>scp://MCI0LSUBD001/repository/MavenSites/EnterpriseBusinessServices/SearchBusinessService</url>
    </site>
</distributionManagement>
</project>
package java.com.example;

import jakarta.enterprise.context.SessionScoped;
import jakarta.inject.Named;

import java.io.Serializable;

// This is a placeholder for the LoginActionListener class.
// It currently does not contain any methods or properties.
@Named(value = "loginActionListener")
@SessionScoped
public class LoginActionListener implements Serializable {

    public String doLoginAction() {
        return "Login action performed";
    }
}

server.xml
<?xml version="1.0" encoding="UTF-8"?>
<server description="engageapps">
	<featureManager>
		<feature>jakartaee-10.0</feature>
		<feature>cdi-4.0</feature>
	</featureManager>

	<!-- Automatically expand WAR files and EAR files-->
	<applicationManager autoExpand="true"/>
	<applicationMonitor updateTrigger="mbean"/>

	<!-- Include application configuration files and standard server.xml-->
<!--	<include location="appConfXML/soxautoreviews_mail.xml"/>-->
<!--	<include location="appConfXML/soxautoreviews_jndi.xml"/>-->
<!--	<include location="appConfXML/soxautoreviews_jdbc.xml"/>-->
<!--	<include location="appConfXML/soxautoreviews_jms.xml"/>-->
</server>

<img width="450" height="667" alt="image" src="https://github.com/user-attachments/assets/12c5fc88-b440-4131-b754-6b2314a9769d" />
