```
package com.assurant.inc.sox.ar.client.bean.admin;

import org.mockito.Mock;
import org.primefaces.PrimeFaces;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.MockedStatic;

import java.util.Optional;

import static org.junit.Assert.*;
import static org.mockito.Mockito.*;

public class UserSummaryBeanTest {
    private UserSummaryBean bean;

    @BeforeEach
    void setUp() {
        bean = new UserSummaryBean();

    }

    @Test
    void testShowAddUserPanel() {
        try (MockedStatic<PrimeFaces> primeFacesMockedStatic = mockStatic(PrimeFaces.class)) {
            PrimeFaces primeFacesMock = mock(PrimeFaces.class);
            primeFacesMockedStatic.when(PrimeFaces::current).thenReturn(primeFacesMock);

            String result = bean.showAddUserPanel();

            assertTrue(bean.isRenderAddUserModalPanel());
            assertEquals(Optional.of(1L), bean.getStatus());
            assertNull(result);
            verify(primeFacesMock).executeScript("PF('addUserModalPanelVar').show();");
        }

    }
}


getting error 
"C:\Program Files\Java\jdk-17.0.5\bin\java.exe" -ea -Didea.test.cyclic.buffer.size=1048576 "-javaagent:C:\Program Files\JetBrains\IntelliJ IDEA 2024.3.2.2\lib\idea_rt.jar=53507:C:\Program Files\JetBrains\IntelliJ IDEA 2024.3.2.2\bin" -javaagent:C:\Users\fy84\AppData\Local\JetBrains\IntelliJIdea2024.3\captureAgent\debugger-agent.jar -Dkotlinx.coroutines.debug.enable.creation.stack.trace=false -Ddebugger.agent.enable.coroutines=true -Dkotlinx.coroutines.debug.enable.flows.stack.trace=true -Dkotlinx.coroutines.debug.enable.mutable.state.flows.stack.trace=true -Dfile.encoding=UTF-8 -classpath "C:\Users\fy84\.m2\repository\org\junit\platform\junit-platform-launcher\1.10.0\junit-platform-launcher-1.10.0.jar;C:\Users\fy84\.m2\repository\org\junit\vintage\junit-vintage-engine\5.10.0\junit-vintage-engine-5.10.0.jar;C:\Program Files\JetBrains\IntelliJ IDEA 2024.3.2.2\lib\idea_rt.jar;C:\Program Files\JetBrains\IntelliJ IDEA 2024.3.2.2\plugins\junit\lib\junit5-rt.jar;C:\Program Files\JetBrains\IntelliJ IDEA 2024.3.2.2\plugins\junit\lib\junit-rt.jar;C:\Users\fy84\Desktop\GroupApps\soxautoreviews\target\test-classes;C:\Users\fy84\Desktop\GroupApps\soxautoreviews\target\classes;C:\Users\fy84\.m2\repository\com\assurant\inc\sox\SoxDataAccess\4.0.31-SNAPSHOT\SoxDataAccess-4.0.31-SNAPSHOT.jar;C:\Users\fy84\.m2\repository\javax\annotation\javax.annotation-api\1.3.2\javax.annotation-api-1.3.2.jar;C:\Users\fy84\.m2\repository\org\springframework\spring-orm\5.3.31\spring-orm-5.3.31.jar;C:\Users\fy84\.m2\repository\org\springframework\spring-jdbc\5.3.31\spring-jdbc-5.3.31.jar;C:\Users\fy84\.m2\repository\org\springframework\spring-tx\5.3.31\spring-tx-5.3.31.jar;C:\Users\fy84\.m2\repository\org\springframework\spring-core\5.3.31\spring-core-5.3.31.jar;C:\Users\fy84\.m2\repository\org\springframework\spring-jcl\5.3.31\spring-jcl-5.3.31.jar;C:\Users\fy84\.m2\repository\org\springframework\spring-context\5.3.31\spring-context-5.3.31.jar;C:\Users\fy84\.m2\repository\org\springframework\spring-beans\5.3.31\spring-beans-5.3.31.jar;C:\Users\fy84\.m2\repository\commons-lang\commons-lang\2.6\commons-lang-2.6.jar;C:\Users\fy84\.m2\repository\org\slf4j\slf4j-api\2.0.7\slf4j-api-2.0.7.jar;C:\Users\fy84\.m2\repository\javax\persistence\javax.persistence-api\2.2\javax.persistence-api-2.2.jar;C:\Users\fy84\.m2\repository\org\codehaus\jackson\jackson-core-asl\1.9.13\jackson-core-asl-1.9.13.jar;C:\Users\fy84\.m2\repository\org\codehaus\jackson\jackson-mapper-asl\1.9.13\jackson-mapper-asl-1.9.13.jar;C:\Users\fy84\.m2\repository\commons-dbcp\commons-dbcp\1.4\commons-dbcp-1.4.jar;C:\Users\fy84\.m2\repository\commons-pool\commons-pool\1.5.4\commons-pool-1.5.4.jar;C:\Users\fy84\.m2\repository\dom4j\dom4j\1.6.1\dom4j-1.6.1.jar;C:\Users\fy84\.m2\repository\xml-apis\xml-apis\1.0.b2\xml-apis-1.0.b2.jar;C:\Users\fy84\.m2\repository\org\hibernate\hibernate-core\5.6.15.Final\hibernate-core-5.6.15.Final.jar;C:\Users\fy84\.m2\repository\org\jboss\logging\jboss-logging\3.4.3.Final\jboss-logging-3.4.3.Final.jar;C:\Users\fy84\.m2\repository\net\bytebuddy\byte-buddy\1.12.18\byte-buddy-1.12.18.jar;C:\Users\fy84\.m2\repository\antlr\antlr\2.7.7\antlr-2.7.7.jar;C:\Users\fy84\.m2\repository\org\jboss\spec\javax\transaction\jboss-transaction-api_1.2_spec\1.1.1.Final\jboss-transaction-api_1.2_spec-1.1.1.Final.jar;C:\Users\fy84\.m2\repository\org\jboss\jandex\2.4.2.Final\jandex-2.4.2.Final.jar;C:\Users\fy84\.m2\repository\com\fasterxml\classmate\1.5.1\classmate-1.5.1.jar;C:\Users\fy84\.m2\repository\javax\activation\javax.activation-api\1.2.0\javax.activation-api-1.2.0.jar;C:\Users\fy84\.m2\repository\javax\xml\bind\jaxb-api\2.3.1\jaxb-api-2.3.1.jar;C:\Users\fy84\.m2\repository\org\glassfish\jaxb\jaxb-runtime\2.3.1\jaxb-runtime-2.3.1.jar;C:\Users\fy84\.m2\repository\org\glassfish\jaxb\txw2\2.3.1\txw2-2.3.1.jar;C:\Users\fy84\.m2\repository\com\sun\istack\istack-commons-runtime\3.0.7\istack-commons-runtime-3.0.7.jar;C:\Users\fy84\.m2\repository\org\jvnet\staxex\stax-ex\1.8\stax-ex-1.8.jar;C:\Users\fy84\.m2\repository\com\sun\xml\fastinfoset\FastInfoset\1.2.15\FastInfoset-1.2.15.jar;C:\Users\fy84\.m2\repository\org\hibernate\common\hibernate-commons-annotations\5.1.2.Final\hibernate-commons-annotations-5.1.2.Final.jar;C:\Users\fy84\.m2\repository\aspectj\aspectjweaver\1.5.3\aspectjweaver-1.5.3.jar;C:\Users\fy84\.m2\repository\net\sf\ehcache\ehcache\2.10.9.2\ehcache-2.10.9.2.jar;C:\Users\fy84\.m2\repository\org\apache\myfaces\core\myfaces-impl\2.2.15\myfaces-impl-2.2.15.jar;C:\Users\fy84\.m2\repository\org\apache\myfaces\core\myfaces-api\2.2.15\myfaces-api-2.2.15.jar;C:\Users\fy84\.m2\repository\org\apache\geronimo\specs\geronimo-atinject_1.0_spec\1.0\geronimo-atinject_1.0_spec-1.0.jar;C:\Users\fy84\.m2\repository\commons-collections\commons-collections\3.2.2\commons-collections-3.2.2.jar;C:\Users\fy84\.m2\repository\commons-beanutils\commons-beanutils\1.9.4\commons-beanutils-1.9.4.jar;C:\Users\fy84\.m2\repository\org\apache\commons\commons-lang3\3.14.0\commons-lang3-3.14.0.jar;C:\Users\fy84\.m2\repository\org\springframework\spring-test\5.3.31\spring-test-5.3.31.jar;C:\Users\fy84\.m2\repository\com\sun\mail\jakarta.mail\2.0.1\jakarta.mail-2.0.1.jar;C:\Users\fy84\.m2\repository\com\sun\activation\jakarta.activation\2.0.1\jakarta.activation-2.0.1.jar;C:\Users\fy84\.m2\repository\org\springframework\spring-web\5.3.31\spring-web-5.3.31.jar;C:\Users\fy84\.m2\repository\org\springframework\spring-webmvc\5.3.31\spring-webmvc-5.3.31.jar;C:\Users\fy84\.m2\repository\org\springframework\spring-expression\5.3.31\spring-expression-5.3.31.jar;C:\Users\fy84\.m2\repository\org\springframework\spring-aop\5.3.31\spring-aop-5.3.31.jar;C:\Users\fy84\.m2\repository\org\aspectj\aspectjweaver\1.9.7\aspectjweaver-1.9.7.jar;C:\Users\fy84\.m2\repository\org\ow2\asm\asm\9.5\asm-9.5.jar;C:\Users\fy84\.m2\repository\net\sf\dozer\dozer\5.5.1\dozer-5.5.1.jar;C:\Users\fy84\.m2\repository\org\slf4j\jcl-over-slf4j\1.7.5\jcl-over-slf4j-1.7.5.jar;C:\Users\fy84\.m2\repository\javax\el\javax.el-api\3.0.0\javax.el-api-3.0.0.jar;C:\Users\fy84\.m2\repository\javax\servlet\javax.servlet-api\4.0.1\javax.servlet-api-4.0.1.jar;C:\Users\fy84\.m2\repository\javax\servlet\jsp\javax.servlet.jsp-api\2.3.3\javax.servlet.jsp-api-2.3.3.jar;C:\Users\fy84\.m2\repository\org\primefaces\primefaces\14.0.6\primefaces-14.0.6.jar;C:\Users\fy84\.m2\repository\commons-digester\commons-digester\2.1\commons-digester-2.1.jar;C:\Users\fy84\.m2\repository\commons-logging\commons-logging\1.1.1\commons-logging-1.1.1.jar;C:\Users\fy84\.m2\repository\junit\junit\4.13.2\junit-4.13.2.jar;C:\Users\fy84\.m2\repository\org\hamcrest\hamcrest-core\1.3\hamcrest-core-1.3.jar;C:\Users\fy84\.m2\repository\com\oracle\database\jdbc\ojdbc8\23.6.0.24.10\ojdbc8-23.6.0.24.10.jar;C:\Users\fy84\.m2\repository\ch\qos\logback\logback-classic\1.4.14\logback-classic-1.4.14.jar;C:\Users\fy84\.m2\repository\ch\qos\logback\logback-core\1.4.14\logback-core-1.4.14.jar;C:\Users\fy84\.m2\repository\org\slf4j\slf4j-simple\2.0.16\slf4j-simple-2.0.16.jar;C:\Users\fy84\.m2\repository\org\glassfish\expressly\expressly\5.0.0\expressly-5.0.0.jar;C:\Users\fy84\.m2\repository\jakarta\el\jakarta.el-api\5.0.0\jakarta.el-api-5.0.0.jar;C:\Users\fy84\.m2\repository\commons-codec\commons-codec\1.15\commons-codec-1.15.jar;C:\Users\fy84\.m2\repository\org\junit\jupiter\junit-jupiter\5.10.0\junit-jupiter-5.10.0.jar;C:\Users\fy84\.m2\repository\org\junit\jupiter\junit-jupiter-api\5.10.0\junit-jupiter-api-5.10.0.jar;C:\Users\fy84\.m2\repository\org\opentest4j\opentest4j\1.3.0\opentest4j-1.3.0.jar;C:\Users\fy84\.m2\repository\org\junit\platform\junit-platform-commons\1.10.0\junit-platform-commons-1.10.0.jar;C:\Users\fy84\.m2\repository\org\apiguardian\apiguardian-api\1.1.2\apiguardian-api-1.1.2.jar;C:\Users\fy84\.m2\repository\org\junit\jupiter\junit-jupiter-params\5.10.0\junit-jupiter-params-5.10.0.jar;C:\Users\fy84\.m2\repository\org\junit\jupiter\junit-jupiter-engine\5.10.0\junit-jupiter-engine-5.10.0.jar;C:\Users\fy84\.m2\repository\org\junit\platform\junit-platform-engine\1.10.0\junit-platform-engine-1.10.0.jar;C:\Users\fy84\.m2\repository\org\mockito\mockito-inline\5.2.0\mockito-inline-5.2.0.jar;C:\Users\fy84\.m2\repository\org\mockito\mockito-core\5.2.0\mockito-core-5.2.0.jar;C:\Users\fy84\.m2\repository\net\bytebuddy\byte-buddy-agent\1.14.1\byte-buddy-agent-1.14.1.jar;C:\Users\fy84\.m2\repository\org\objenesis\objenesis\3.3\objenesis-3.3.jar" com.intellij.rt.junit.JUnitStarter -ideVersion5 -junit5 com.assurant.inc.sox.ar.client.bean.admin.UserSummaryBeanTest,testShowAddUserPanel
SLF4J: Class path contains multiple SLF4J providers.
SLF4J: Found provider [ch.qos.logback.classic.spi.LogbackServiceProvider@5fe94a96]
SLF4J: Found provider [org.slf4j.simple.SimpleServiceProvider@443118b0]
SLF4J: See https://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual provider is of type [ch.qos.logback.classic.spi.LogbackServiceProvider@5fe94a96]
Java HotSpot(TM) 64-Bit Server VM warning: Sharing is only supported for boot loader classes because bootstrap classpath has been appended

java.lang.InternalError: class redefinition failed: invalid class

	at java.instrument/sun.instrument.InstrumentationImpl.retransformClasses0(Native Method)
	at java.instrument/sun.instrument.InstrumentationImpl.retransformClasses(InstrumentationImpl.java:169)
	at org.mockito.internal.creation.bytebuddy.InlineBytecodeGenerator.triggerRetransformation(InlineBytecodeGenerator.java:281)
	at org.mockito.internal.creation.bytebuddy.InlineBytecodeGenerator.mockClassStatic(InlineBytecodeGenerator.java:226)
	at org.mockito.internal.creation.bytebuddy.TypeCachingBytecodeGenerator.mockClassStatic(TypeCachingBytecodeGenerator.java:63)
	at org.mockito.internal.creation.bytebuddy.InlineDelegateByteBuddyMockMaker.createStaticMock(InlineDelegateByteBuddyMockMaker.java:566)
	at org.mockito.internal.creation.bytebuddy.InlineByteBuddyMockMaker.createStaticMock(InlineByteBuddyMockMaker.java:83)
	at org.mockito.internal.util.MockUtil.createStaticMock(MockUtil.java:202)
	at org.mockito.internal.MockitoCore.mockStatic(MockitoCore.java:134)
	at org.mockito.Mockito.mockStatic(Mockito.java:2307)
	at org.mockito.Mockito.mockStatic(Mockito.java:2244)
	at com.assurant.inc.sox.ar.client.bean.admin.UserSummaryBeanTest.testShowAddUserPanel(UserSummaryBeanTest.java:27)
	at java.base/java.lang.reflect.Method.invoke(Method.java:568)
	at java.base/java.util.ArrayList.forEach(ArrayList.java:1511)
	at java.base/java.util.ArrayList.forEach(ArrayList.java:1511)
