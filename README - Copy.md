import java.math.BigDecimal;

import javax.faces.application.FacesMessage;
import javax.faces.component.UIComponent;
import javax.faces.component.UIInput;
import javax.faces.context.FacesContext;
import javax.faces.event.AjaxBehaviorEvent;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class PaymentValidatorTest {

    private PaymentValidator paymentValidator;
    private FacesContext facesContext;
    private UIInput payAmountInput;
    private UIComponent component;
    private AjaxBehaviorEvent event;

    @BeforeEach
    void setUp() {
        paymentValidator = new PaymentValidator();

        // Initialize FacesContext (Requires JSF Test Framework for full support)
        facesContext = FacesContext.getCurrentInstance();

        // Initialize a real UIInput instance
        payAmountInput = new UIInput();
        
        // Create a simple UIComponent instance
        component = new UIComponent() {}; // Anonymous subclass since UIComponent is abstract

        // Manually create an AjaxBehaviorEvent with the component
        event = new AjaxBehaviorEvent(component) {};
    }

    @Test
    void testValidate_NullValue_ShouldShowErrorMessage() {
        // Simulate a null value
        payAmountInput.getAttributes().put("value", null);

        // Invoke the method
        paymentValidator.validate(event);

        // Verify that an error message was added
        assertFalse(facesContext.getMessageList().isEmpty());

        // Check the specific error message (Modify based on actual implementation)
        FacesMessage message = facesContext.getMessageList().get(0);
        assertEquals("Error: Payment amount is required.", message.getDetail());
    }

    @Test
    void testValidate_NegativeValue_ShouldShowErrorMessage() {
        // Simulate a negative value
        payAmountInput.getAttributes().put("value", new BigDecimal("-1"));

        // Invoke the method
        paymentValidator.validate(event);

        // Verify that an error message was added
        assertFalse(facesContext.getMessageList().isEmpty());

        // Check the error message for negative values
        FacesMessage message = facesContext.getMessageList().get(0);
        assertEquals("Error: Negative payment amount is not allowed.", message.getDetail());
    }

    @Test
    void testValidate_ValidValue_ShouldNotShowErrorMessage() {
        // Simulate a valid payment amount
        payAmountInput.getAttributes().put("value", new BigDecimal("100"));

        // Invoke the method
        paymentValidator.validate(event);

        // Ensure no error messages are added
        assertTrue(facesContext.getMessageList().isEmpty());
    }
}

Public Sub BatchStepOpenForm(ByVal Textbox As Textbox, ByVal DataMode As AcFormOpenDataMode, Optional ByVal BatchID As Variant = Null)

```
Imports System.Runtime.InteropServices
Imports Microsoft.Office.Interop.Access

<ComVisible(True)>
<Guid("0ABBE17C-CD4E-4B6C-B5B7-A9E70E6845E1")>
<ClassInterface(ClassInterfaceType.None)>
Public Class FlexGridWrapper
    Implements IFlexGridWrapper

    ' Double-click event handler
    Private Sub flexGrid_DoubleClick(sender As Object, e As EventArgs) Handles flexGrid.DoubleClick
        Dim selectedRow As Integer = flexGrid.Row
        Dim batchID As String = flexGrid.get_TextMatrix(selectedRow, 0) ' Assuming BatchID is in the first column

        ' Call the VBA method
        CallVBAFunction("BatchStepOpenForm", batchID)
    End Sub

    ' Method to call a VBA function in MS Access
    Public Sub CallVBAFunction(ByVal functionName As String, ByVal batchID As String)
        Try
            ' Get the running Access instance
            Dim accessApp As Object = Marshal.GetActiveObject("Access.Application")

            ' Open a hidden Access form that contains the target textbox (Replace 'frmHiddenForm' with the actual form name)
            accessApp.DoCmd.OpenForm "frmHiddenForm", AcFormView.acDesign

            ' Get the textbox control reference
            Dim txtBox As Object = accessApp.Forms("frmHiddenForm").Controls("txtYourTextbox")

            ' Call the VBA method with parameters
            accessApp.Run(functionName, txtBox, AcFormOpenDataMode.acFormEdit, batchID)

        Catch ex As Exception
            Throw New Exception("Error calling VBA function: " & ex.Message)
        End Try
    End Sub
End Class


' Double-click event handler
    Private Sub flexGrid_DoubleClick(sender As Object, e As EventArgs) Handles flexGrid.DoubleClick
        ' Call the VBA method from OpenFormRoutines module
        CallVBAFunction("OpenFormRoutines.BatchStepOpenForm")
    End Sub

    ' Method to call a VBA function in MS Access
    Public Sub CallVBAFunction(ByVal functionName As String)
        Try
            ' Get the running MS Access instance
            Dim accessApp As Object = Marshal.GetActiveObject("Access.Application")

            ' Open the hidden form containing the required TextBox
            Dim formName As String = "frmHiddenForm" ' Replace with an actual form that contains the textbox
            accessApp.DoCmd.OpenForm(formName, 0) ' 0 = acNormal view

            ' Get a reference to the TextBox control
            Dim txtBox As Object = accessApp.Forms(formName).Controls("txtYourTextbox") ' Replace with actual textbox name

            ' Set the desired DataMode (acFormAdd, acFormEdit, etc.)
            Dim dataMode As Integer = 2 ' Example: acFormEdit (1=acFormAdd, 2=acFormEdit, 3=acFormReadOnly)

            ' Call the VBA function with the parameters
            accessApp.Run(functionName, txtBox, dataMode, Null) ' Pass Null for BatchID if not needed

        Catch ex As Exception
            Throw New Exception("Error calling VBA function: " & ex.Message)
        End Try
    End Sub
```

```

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
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
				<artifactId>maven-war-plugin</artifactId>
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
			<version>1.4.4</version>
			<!--<exclusions>
				<exclusion>
					<groupId>javax.servlet</groupId>
					<artifactId>servlet-api</artifactId>
				</exclusion>
			</exclusions>-->
		</dependency>
		<dependency>
			<groupId>javax.mail</groupId>
			<artifactId>mail</artifactId>
			<version>1.4</version>
		</dependency>
		<!--<dependency>
			<groupId>org.glassfish</groupId>
			<artifactId></artifactId>
		</dependency>-->
		<dependency>
			<groupId>org.richfaces</groupId>
			<artifactId>richfaces</artifactId>
			<version>4.5.17.Final</version>
		</dependency>
		<dependency>
			<groupId>org.apache.myfaces.core</groupId>
			<artifactId>myfaces-api</artifactId>
			<version>2.2.12</version>
		</dependency>
		<dependency>
			<groupId>org.apache.myfaces.core</groupId>
			<artifactId>myfaces-impl</artifactId>
			<version>2.2.12</version>
		</dependency>
		<dependency>
			<groupId>commons-lang</groupId>
			<artifactId>commons-lang</artifactId>
			<version>2.3</version>
			<scope>compile</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<version>${spring.version}</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>com.sun.jersey</groupId>
			<artifactId>jersey-json</artifactId>
			<version>1.12</version>
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
				<!-- Exclude Commons Logging in favor of SLF4j -->
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
			<version>1.6.1</version>
		</dependency>

		<dependency>
			<groupId>asm</groupId>
			<artifactId>asm</artifactId>
			<version>1.5.3</version>
		</dependency>
		<dependency>
			<groupId>asm</groupId>
			<artifactId>asm-attrs</artifactId>
			<version>1.5.3</version>
		</dependency>


		<!--<dependency> <groupId>aspectj</groupId> <artifactId>aspectjrt</artifactId> 
			<version>1.6.1</version> <scope>compile</scope> </dependency> -->
		<!--<dependency> <groupId>aspectj</groupId> <artifactId>aspectjweaver</artifactId> 
			<version>1.5.3</version> <scope>compile</scope> </dependency> -->
		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>1.2.17</version>
			<scope>compile</scope>
		</dependency>
		<dependency>
			<groupId>net.sf.dozer</groupId>
			<artifactId>dozer</artifactId>
			<version>4.2</version>
			<scope>compile</scope>
		</dependency>
		<dependency>
			<groupId>javax.el</groupId>
			<artifactId>el-api</artifactId>
			<version>2.2</version>
			<!--<scope>provided</scope>-->
		</dependency>
		<dependency>
			<groupId>javax.servlet.jsp</groupId>
			<artifactId>jsp-api</artifactId>
			<version>2.2</version>
		</dependency>
		<!--<dependency>
			<groupId>javax</groupId>
			<artifactId>javaee-api</artifactId>
			<version>7.0</version>
			<scope>provided</scope>
		</dependency>-->
		<!--<dependency> <groupId>javax.el</groupId> <artifactId>el-ri</artifactId> 
			<version>1.2</version> <scope>compile</scope> </dependency> -->
		<dependency>
			<groupId>com.sun.facelets</groupId>
			<artifactId>jsf-facelets</artifactId>
			<version>1.1.14</version>
			<scope>compile</scope>
			<exclusions>
				<exclusion>
					<groupId>com.sun.faces</groupId>
					<artifactId>jsf-api</artifactId>
				</exclusion>
				<exclusion>
					<groupId>com.sun.faces</groupId>
					<artifactId>jsf-impl</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>javax.faces</groupId>
			<artifactId>jsf-api</artifactId>
			<version>1.2_15</version>
			
		</dependency>
		<dependency>
			<groupId>javax.faces</groupId>
			<artifactId>jsf-impl</artifactId>
			<version>1.2_15</version>
			
		</dependency>

	
		<dependency>
			<groupId>commons-digester</groupId>
			<artifactId>commons-digester</artifactId>
			<version>1.8</version>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.4</version>
			<scope>provided</scope>
		</dependency>
		<!-- <dependency> <groupId>axis</groupId> <artifactId>axis</artifactId> 
			<version>1.3</version> </dependency> -->
		<dependency>
			<groupId>com.oracle</groupId>
			<artifactId>ojdbc14</artifactId>
			<version>10.1.0.4.0</version>
			<!--<artifactId>ojdbc6</artifactId> <version>11.2.0.4</version> -->
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.glassfish.web</groupId>
			<artifactId>el-impl</artifactId>
			<version>2.2</version>
		</dependency>
		<!-- <dependency> <groupId>com.assurant.inc.common</groupId> <artifactId>Obfuscator</artifactId> 
			<version>1.0</version> <scope>compile</scope> </dependency> -->
		<dependency>
			<groupId>commons-codec</groupId>
			<artifactId>commons-codec</artifactId>
			<version>1.10</version>
			<scope>compile</scope>
		</dependency>
		<dependency>
			<groupId>net.sf.ehcache</groupId>
			<artifactId>ehcache</artifactId>
			<version>1.5.0</version>
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
		<jdkTargetVersion>1.8</jdkTargetVersion>
		<spring.version>5.3.39</spring.version>
	</properties>
</project>


<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
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
				<artifactId>maven-war-plugin</artifactId>
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
			<version>1.4.4</version>
		</dependency>
		<dependency>
			<groupId>javax.mail</groupId>
			<artifactId>mail</artifactId>
			<version>${javax.mail.version}</version>
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
			<groupId>commons-lang</groupId>
			<artifactId>commons-lang</artifactId>
			<version>${commons-lang.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<version>${spring.version}</version>
			<scope>test</scope>
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
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>${log4j.version}</version>
		</dependency>
		<dependency>
			<groupId>net.sf.dozer</groupId>
			<artifactId>dozer</artifactId>
			<version>${dozer.version}</version>
		</dependency>
		<dependency>
			<groupId>javax.el</groupId>
			<artifactId>el-api</artifactId>
			<version>${javax.el.version}</version>
		</dependency>
		<dependency>
			<groupId>javax.servlet.jsp</groupId>
			<artifactId>jsp-api</artifactId>
			<version>${jsp-api.version}</version>
		</dependency>
		<dependency>
			<groupId>com.sun.facelets</groupId>
			<artifactId>jsf-facelets</artifactId>
			<version>${facelets.version}</version>
			<exclusions>
				<exclusion>
					<groupId>com.sun.faces</groupId>
					<artifactId>jsf-api</artifactId>
				</exclusion>
				<exclusion>
					<groupId>com.sun.faces</groupId>
					<artifactId>jsf-impl</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>javax.faces</groupId>
			<artifactId>jsf-api</artifactId>
			<version>${jsf-api.version}</version>
		</dependency>
		<dependency>
			<groupId>javax.faces</groupId>
			<artifactId>jsf-impl</artifactId>
			<version>${jsf-api.version}</version>
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
			<groupId>org.glassfish.web</groupId>
			<artifactId>el-impl</artifactId>
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
	<properties>
		<jdkTargetVersion>1.8</jdkTargetVersion>
		<spring.version>5.3.39</spring.version>
		<javax.mail.version>1.6.2</javax.mail.version>
		<richfaces.version>4.5.17.Final</richfaces.version>
		<myfaces.version>2.3.14</myfaces.version>
		<commons-lang.version>2.6</commons-lang.version>
		<jersey.version>1.19.4</jersey.version>
		<aspectj.version>1.9.7</aspectj.version>
		<asm.version>9.5</asm.version>
		<log4j.version>1.2.17</log4j.version>
		<dozer.version>5.5.1</dozer.version>
		<javax.el.version>2.2.4</javax.el.version>
		<jsp-api.version>2.2</jsp-api.version>
		<facelets.version>1.1.15</facelets.version>
		<jsf-api.version>2.3.9</jsf-api.version>
		<commons-digester.version>2.1</commons-digester.version>
		<junit.version>4.13.2</junit.version>
		<ojdbc.version>21.9.0.0</ojdbc.version>
		<glassfish-el.version>2.2.1</glassfish-el.version>
		<commons-codec.version>1.15</commons-codec.version>
		<ehcache.version>2.10.9.2</ehcache.version>
	</properties>
</project>

```
