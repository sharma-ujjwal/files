```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<properties>
		<jdkTargetVersion>17</jdkTargetVersion>
		<log4j.version>1.4.14</log4j.version>
		<spring.version>5.3.31</spring.version>
		<javax.mail.version>2.0.1</javax.mail.version>
		<richfaces.version>4.5.17.Final</richfaces.version>
		<myfaces.version>2.2.15</myfaces.version>
		<commons-lang.version>3.14.0</commons-lang.version>
		<jersey.version>1.19.4</jersey.version>
		<aspectj.version>1.9.7</aspectj.version>
		<slf4j.version>2.0.7</slf4j.version>
		<asm.version>9.5</asm.version>
		<dozer.version>5.5.1</dozer.version>
		<javax.el.version>6.0.1</javax.el.version>
		<javax.servlet.jsp.api.version>2.3.3</javax.servlet.jsp.api.version>
		<facelets.version>1.1.14</facelets.version>
		<commons-digester.version>2.1</commons-digester.version>
		<junit.version>4.13.2</junit.version>
		<ojdbc.version>21.9.0.0</ojdbc.version>
		<glassfish-el.version>5.0.0</glassfish-el.version>
		<commons-codec.version>1.15</commons-codec.version>
		<ehcache.version>2.10.9.2</ehcache.version>
		<javax.servlet.api.version>4.0.1</javax.servlet.api.version>
	</properties>
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.assurant.inc.sox.ar</groupId>
	<artifactId>newsoxautoreviews</artifactId>
	<packaging>war</packaging>
	<name>Sox AutoReviews War</name>
	<version>4.0-SNAPSHOT</version>
	<description />
	<build>
		<finalName>newsoxautoreviews</finalName>
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
			<version>3.6-SNAPSHOT</version>
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
			<exclusions>
				<exclusion>
					<groupId>org.apache.myfaces.core</groupId>
					<artifactId>myfaces-push</artifactId>
				</exclusion>
			</exclusions>
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
			<groupId>jakarta.persistence</groupId>
			<artifactId>jakarta.persistence-api</artifactId>
			<version>3.2.0</version>
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
			<artifactId>javax.servlet.jsp-api</artifactId>
			<version>${javax.servlet.jsp.api.version}</version>
			<scope>provided</scope>
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
</project>


<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

   <properties>
      <jdkTargetVersion>17</jdkTargetVersion>
      <maven.test.skip>true</maven.test.skip>
      <spring.version>5.3.31</spring.version>
      <persistenceApi>2.2</persistenceApi>
      <commonsLang>2.6</commonsLang>
      <junitJupiterVersion>5.10.0</junitJupiterVersion>
      <jacksonCoreAsl>1.9.13</jacksonCoreAsl>
      <commonsDbcp>1.4</commonsDbcp>
      <dom4j>1.6.1</dom4j>
      <servletApi>2.5</servletApi>
      <aspectjweaver>1.5.3</aspectjweaver>
      <ehCache>2.10.9.2</ehCache>
      <backportUtilConcurrent>3.1</backportUtilConcurrent>
      <logbackVersion>1.4.14</logbackVersion>
      <slf4jVersion>2.0.7</slf4jVersion>
      <mavenPmdPlugin>3.21.0</mavenPmdPlugin>
      <mavenJxrPlugin>3.3.1</mavenJxrPlugin>
      <mavenSurefireReportPlugin>3.2.5</mavenSurefireReportPlugin>
      <coberturaMavenPlugin>2.7</coberturaMavenPlugin>
   </properties>
   <modelVersion>4.0.0</modelVersion>

   <groupId>com.assurant.inc.sox</groupId>
   <artifactId>SoxDataAccess</artifactId>
   <version>3.6-SNAPSHOT</version>
   <packaging>jar</packaging>

   <name>SoxDataAccess</name>
   <description>The data access code for the sox application.</description>
   <build>
      <plugins>
         <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-jxr-plugin</artifactId>
            <version>${mavenJxrPlugin}</version>
         </plugin>
         <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-pmd-plugin</artifactId>
            <version>${mavenPmdPlugin}</version>
            <configuration>
               <targetJdk>${jdkTargetVersion}</targetJdk>
            </configuration>
         </plugin>
         <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-resources-plugin</artifactId>
            <version>3.2.0</version>
            <configuration>
               <encoding>UTF-8</encoding>
            </configuration>
         </plugin>
         <plugin>
            <artifactId>maven-clean-plugin</artifactId>
            <version>2.5</version>
            <executions>
               <execution>
                  <id>clean</id>
                  <phase>process-resources</phase>
                  <goals>
                     <goal>clean</goal>
                  </goals>
               </execution>
            </executions>
         </plugin>
         <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-source-plugin</artifactId>
            <version>3.2.1</version>
            <executions>
               <execution>
                  <id>attach-sources</id>
                  <phase>verify</phase>
                  <goals>
                     <goal>jar</goal>
                  </goals>
               </execution>
            </executions>
         </plugin>
         <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>3.5.1</version>
            <configuration>
               <testFailureIgnore>true</testFailureIgnore>
               <skipTests>false</skipTests>
            </configuration>
         </plugin>
         <plugin>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
               <source>${jdkTargetVersion}</source>
               <target>${jdkTargetVersion}</target>
            </configuration>
         </plugin>

      </plugins>
   </build>

   <dependencies>
      <dependency>
         <groupId>javax.annotation</groupId>
         <artifactId>javax.annotation-api</artifactId>
         <version>1.3.2</version>
      </dependency>
      <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-orm</artifactId>
         <version>${spring.version}</version>
      </dependency>
      <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-core</artifactId>
         <version>${spring.version}</version>
         <exclusions>
            <exclusion>
               <artifactId>org.springframework</artifactId>
               <groupId>spring-asm</groupId>
            </exclusion>
         </exclusions>
      </dependency>
      <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-context</artifactId>
         <version>${spring.version}</version>
         <exclusions>
            <exclusion>
               <groupId>org.springframework</groupId>
               <artifactId>spring-asm</artifactId>
            </exclusion>
         </exclusions>
      </dependency>
      <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-beans</artifactId>
         <version>${spring.version}</version>
      </dependency>
      <dependency>
         <groupId>commons-lang</groupId>
         <artifactId>commons-lang</artifactId>
         <version>${commonsLang}</version>
         <scope>compile</scope>
      </dependency>
      <dependency>
         <groupId>ch.qos.logback</groupId>
         <artifactId>logback-core</artifactId>
         <version>${logbackVersion}</version>
      </dependency>
      <dependency>
         <groupId>org.slf4j</groupId>
         <artifactId>slf4j-api</artifactId>
         <version>${slf4jVersion}</version>
      </dependency>
      <dependency>
         <groupId>org.junit.jupiter</groupId>
         <artifactId>junit-jupiter-engine</artifactId>
         <version>${junitJupiterVersion}</version>
         <scope>test</scope>
      </dependency>
      <dependency>
         <groupId>org.junit.jupiter</groupId>
         <artifactId>junit-jupiter-api</artifactId>
         <version>${junitJupiterVersion}</version>
         <scope>test</scope>
      </dependency>
      <dependency>
         <groupId>org.junit.vintage</groupId>
         <artifactId>junit-vintage-engine</artifactId>
         <version>${junitJupiterVersion}</version>
      </dependency>
      <dependency>
         <groupId>javax.persistence</groupId>
         <artifactId>javax.persistence-api</artifactId>
         <version>${persistenceApi}</version>
      </dependency>
      <dependency>
         <groupId>org.codehaus.jackson</groupId>
         <artifactId>jackson-core-asl</artifactId>
         <version>${jacksonCoreAsl}</version>
      </dependency>
      <dependency>
         <groupId>org.codehaus.jackson</groupId>
         <artifactId>jackson-mapper-asl</artifactId>
         <version>${jacksonCoreAsl}</version>
      </dependency>
      <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-test</artifactId>
         <version>${spring.version}</version>
         <scope>test</scope>
      </dependency>
      <dependency>
         <groupId>commons-dbcp</groupId>
         <artifactId>commons-dbcp</artifactId>
         <version>${commonsDbcp}</version>
         <scope>runtime</scope>
      </dependency>
      <dependency>
         <groupId>dom4j</groupId>
         <artifactId>dom4j</artifactId>
         <version>${dom4j}</version>
         <scope>runtime</scope>
      </dependency>
      <dependency>
         <groupId>org.hibernate</groupId>
         <artifactId>hibernate-core</artifactId>
         <version>5.6.15.Final</version>
         <scope>compile</scope>
         <exclusions>
            <exclusion>
               <groupId>net.sf.ehcache</groupId>
               <artifactId>ehcache</artifactId>
            </exclusion>
            <exclusion>
               <groupId>commons-logging</groupId>
               <artifactId>commons-logging</artifactId>
            </exclusion>
            <exclusion>
               <groupId>asm</groupId>
               <artifactId>asm-attrs</artifactId>
            </exclusion>
            <exclusion>
               <groupId>asm</groupId>
               <artifactId>asm</artifactId>
            </exclusion>
         </exclusions>
      </dependency>
      <dependency>
         <groupId>com.oracle.database.jdbc</groupId>
         <artifactId>ojdbc8</artifactId>
         <version>21.3.0.0</version>
      </dependency>
      <dependency>
         <groupId>org.hibernate.common</groupId>
         <artifactId>hibernate-commons-annotations</artifactId>
         <version>5.1.2.Final</version>
         <scope>compile</scope>
      </dependency>
      <dependency>
         <groupId>aspectj</groupId>
         <artifactId>aspectjweaver</artifactId>
         <version>${aspectjweaver}</version>
         <scope>compile</scope>
      </dependency>
      <dependency>
         <groupId>net.sf.ehcache</groupId>
         <artifactId>ehcache</artifactId>
         <version>${ehCache}</version>
         <scope>test</scope>
         <exclusions>
            <exclusion>
               <groupId>commons-logging</groupId>
               <artifactId>commons-logging</artifactId>
            </exclusion>
            <exclusion>
               <groupId>commons-collections</groupId>
               <artifactId>commons-collections</artifactId>
            </exclusion>
         </exclusions>
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
            <artifactId>maven-surefire-report-plugin</artifactId>
            <version>${mavenSurefireReportPlugin}</version>
         </plugin>
         <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>cobertura-maven-plugin</artifactId>
            <version>${coberturaMavenPlugin}</version>
         </plugin>
      </plugins>
   </reporting>
</project>

