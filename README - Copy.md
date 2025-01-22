Cannot invoke "sunlife.us.dc.employer.service.SystemAlertService.getAllSystemAlerts()" because "this.systemAlertService" is null
java.lang.NullPointerException: Cannot invoke "sunlife.us.dc.employer.service.SystemAlertService.getAllSystemAlerts()" because "this.systemAlertService" is null
	at controller.TestEmployerSystemAlertController.testGetAllSystemAlerts(TestEmployerSystemAlertController.java:46)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:77)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:568)
	at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:50)
	at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
	at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:47)
	at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
	at org.junit.internal.runners.statements.RunBefores.evaluate(RunBefores.java:26)
	at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:325)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:78)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:57)
	at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
	at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
	at org.gradle.api.internal.tasks.testing.junit.JUnitTestClassExecutor.runTestClass(JUnitTestClassExecutor.java:110)
	at org.gradle.api.internal.tasks.testing.junit.JUnitTestClassExecutor.execute(JUnitTestClassExecutor.java:58)
	at org.gradle.api.internal.tasks.testing.junit.JUnitTestClassExecutor.execute(JUnitTestClassExecutor.java:38)
	at org.gradle.api.internal.tasks.testing.junit.AbstractJUnitTestClassProcessor.processTestClass(AbstractJUnitTestClassProcessor.java:62)
	at org.gradle.api.internal.tasks.testing.SuiteTestClassProcessor.processTestClass(SuiteTestClassProcessor.java:51)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:77)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:568)
	at org.gradle.internal.dispatch.ReflectionDispatch.dispatch(ReflectionDispatch.java:36)
	at org.gradle.internal.dispatch.ReflectionDispatch.dispatch(ReflectionDispatch.java:24)
	at org.gradle.internal.dispatch.ContextClassLoaderDispatch.dispatch(ContextClassLoaderDispatch.java:33)
	at org.gradle.internal.dispatch.ProxyDispatchAdapter$DispatchingInvocationHandler.invoke(ProxyDispatchAdapter.java:94)
	at jdk.proxy2/jdk.proxy2.$Proxy5.processTestClass(Unknown Source)
	at org.gradle.api.internal.tasks.testing.worker.TestWorker$2.run(TestWorker.java:176)
	at org.gradle.api.internal.tasks.testing.worker.TestWorker.executeAndMaintainThreadName(TestWorker.java:129)
	at org.gradle.api.internal.tasks.testing.worker.TestWorker.execute(TestWorker.java:100)
	at org.gradle.api.internal.tasks.testing.worker.TestWorker.execute(TestWorker.java:60)
	at org.gradle.process.internal.worker.child.ActionExecutionWorker.execute(ActionExecutionWorker.java:56)
	at org.gradle.process.internal.worker.child.SystemApplicationClassLoaderWorker.call(SystemApplicationClassLoaderWorker.java:133)
	at org.gradle.process.internal.worker.child.SystemApplicationClassLoaderWorker.call(SystemApplicationClassLoaderWorker.java:71)
	at worker.org.gradle.process.internal.worker.GradleWorkerMain.run(GradleWorkerMain.java:69)
	at worker.org.gradle.process.internal.worker.GradleWorkerMain.main(GradleWorkerMain.java:74)




package controller;

import org.apache.commons.lang3.reflect.FieldUtils;
import org.junit.Assert;
import org.junit.Before;
import org.junit.Test;
import org.junit.jupiter.api.BeforeEach;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import sunlife.us.dc.employer.controller.EmployerSystemAlertController;
import sunlife.us.dc.employer.dto.generic.ResponseEntity;
import sunlife.us.dc.employer.dto.generic.SystemAlertDTO;
import sunlife.us.dc.employer.dto.response.SystemAlertResponseDTO;
import sunlife.us.dc.employer.service.SystemAlertService;
import util.TestUtil;

import java.util.Collections;

import static org.mockito.Mockito.when;


public class TestEmployerSystemAlertController extends TestUtil {

    @Mock
    private SystemAlertService systemAlertService;
    private EmployerSystemAlertController sysAlertController;

    @Before
    public void setUp() throws IllegalAccessException {
        sysAlertController = new EmployerSystemAlertController();
        FieldUtils.writeField(sysAlertController, "systemAlertService", systemAlertService, true);
    }

    @Test
    public void testGetAllSystemAlerts() {
        // Define the expected response
        SystemAlertResponseDTO responseDTO = new SystemAlertResponseDTO();
        SystemAlertDTO dto = new SystemAlertDTO();
        dto.setSystemAlertCd("ERR55");
        responseDTO.setSystemAlertDTOs(Collections.singletonList(dto));
        ResponseEntity<SystemAlertResponseDTO> expectedResponse = new ResponseEntity<>();
        expectedResponse.setSystemAlertDTOs(Collections.singletonList(dto));

        // Define behavior of mock service
        when(systemAlertService.getAllSystemAlerts()).thenReturn(expectedResponse);

        // Call the method to test
        ResponseEntity<SystemAlertResponseDTO> resp = sysAlertController.getAllSystemAlerts();

        // Assert the response
        Assert.assertNotNull("ERR55", resp.getSystemAlertDTOs().iterator().next().getSystemAlertCd());
    }
}
