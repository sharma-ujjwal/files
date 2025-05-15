```
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'accessListBean': Invocation of init method failed; nested exception is java.lang.NullPointerException: Cannot invoke "java.util.List.size()" because the return value of "com.assurant.inc.sox.ar.client.bean.reviewerreport.AccessListBean.getReviewUserApplicationAccessesDTOs()" is null
at org.springframework.beans.factory.annotation.InitDestroyAnnotationBeanPostProcessor.postProcessBeforeInitialization(InitDestroyAnnotationBeanPostProcessor.java:160)
at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.applyBeanPostProcessorsBeforeInitialization(AbstractAutowireCapableBeanFactory.java:440)
at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1796)
at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:620)
at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:542)
at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$1(AbstractBeanFactory.java:374)
at org.springframework.web.context.request.AbstractRequestAttributesScope.get(AbstractRequestAttributesScope.java:45)
at org.springframework.web.context.request.SessionScope.get(SessionScope.java:57)
at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:371)
at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:208)
at org.springframework.context.support.AbstractApplicationContext.getBean(AbstractApplicationContext.java:1168)
at org.springframework.web.jsf.el.SpringBeanFacesELResolver.getValue(SpringBeanFacesELResolver.java:82)
at javax.el.CompositeELResolver.getValue(CompositeELResolver.java:66)
at [internal classes]
at com.assurant.inc.sox.ar.client.bean.util.JSFUtils.lookupBean(JSFUtils.java:82)
at com.assurant.inc.sox.ar.client.bean.dashboard.ReviewUserDashboardBean.doSelectUser(ReviewUserDashboardBean.java:131)
at jdk.internal.reflect.GeneratedMethodAccessor1757.invoke(Unknown Source)
at java.base/java.lang.reflect.Method.invoke(Method.java:568)
at org.apache.el.parser.AstValue.invoke(AstValue.java:245)
... 4 more
