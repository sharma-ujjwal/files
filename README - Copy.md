Exception thrown by application class 'javax.faces.webapp.FacesServlet.service:236'
javax.servlet.ServletException: javax.el.ELException: Error reading 'taskListTable' on type com.assurant.inc.sox.ar.client.bean.tasklist.MyTaskListBean$$EnhancerBySpringCGLIB$$be1495da
at javax.faces.webapp.FacesServlet.service(FacesServlet.java:236)
at [internal classes]
at com.assurant.inc.sox.ar.servlets.RedirectServlet.doPost(RedirectServlet.java:73)
at com.assurant.inc.sox.ar.servlets.BaseServlet.doGet(BaseServlet.java:17)
at javax.servlet.http.HttpServlet.service(HttpServlet.java:686)
at [internal classes]
Caused by: org.apache.myfaces.view.facelets.el.ContextAwareELException: javax.el.ELException: Error reading 'taskListTable' on type com.assurant.inc.sox.ar.client.bean.tasklist.MyTaskListBean$$EnhancerBySpringCGLIB$$be1495da
at org.apache.myfaces.view.facelets.el.ContextAwareTagValueExpression.getValue(ContextAwareTagValueExpression.java:101)
at [internal classes]
at org.richfaces.view.facelets.html.BehaviorsAddingComponentHandlerWrapper.applyNextHandler(BehaviorsAddingComponentHandlerWrapper.java:53)
at org.apache.myfaces.view.facelets.tag.jsf.ComponentTagHandlerDelegate.apply(ComponentTagHandlerDelegate.java:373)
at [internal classes]
at org.richfaces.view.facelets.html.BehaviorsAddingComponentHandlerWrapper.applyNextHandler(BehaviorsAddingComponentHandlerWrapper.java:53)
at org.apache.myfaces.view.facelets.tag.jsf.ComponentTagHandlerDelegate.apply(ComponentTagHandlerDelegate.java:373)
at [internal classes]
at org.richfaces.view.facelets.html.BehaviorsAddingComponentHandlerWrapper.applyNextHandler(BehaviorsAddingComponentHandlerWrapper.java:53)
at org.apache.myfaces.view.facelets.tag.jsf.ComponentTagHandlerDelegate.apply(ComponentTagHandlerDelegate.java:373)
at [internal classes]
at org.richfaces.view.facelets.html.BehaviorsAddingComponentHandlerWrapper.applyNextHandler(BehaviorsAddingComponentHandlerWrapper.java:53)
at org.apache.myfaces.view.facelets.tag.jsf.ComponentTagHandlerDelegate.apply(ComponentTagHandlerDelegate.java:373)
at [internal classes]
at org.richfaces.view.facelets.html.BehaviorsAddingComponentHandlerWrapper.applyNextHandler(BehaviorsAddingComponentHandlerWrapper.java:53)
at org.apache.myfaces.view.facelets.tag.jsf.ComponentTagHandlerDelegate.apply(ComponentTagHandlerDelegate.java:373)
... 5 more
Caused by: javax.el.ELException: Error reading 'taskListTable' on type com.assurant.inc.sox.ar.client.bean.tasklist.MyTaskListBean$$EnhancerBySpringCGLIB$$be1495da
at javax.el.BeanELResolver.getValue(BeanELResolver.java:97)
... 20 more
Caused by: org.springframework.dao.InvalidDataAccessResourceUsageException: could not extract ResultSet; SQL [n/a]; nested exception is org.hibernate.exception.SQLGrammarException: could not extract ResultSet
at org.springframework.orm.hibernate5.SessionFactoryUtils.convertHibernateAccessException(SessionFactoryUtils.java:195)
at org.springframework.orm.hibernate5.HibernateTemplate.doExecute(HibernateTemplate.java:370)
at org.springframework.orm.hibernate5.HibernateTemplate.executeWithNativeSession(HibernateTemplate.java:334)
at org.springframework.orm.hibernate5.HibernateTemplate.get(HibernateTemplate.java:451)
at org.springframework.orm.hibernate5.HibernateTemplate.get(HibernateTemplate.java:445)
at com.assurant.inc.sox.dao.ar.impl.ReviewerDao.findById(ReviewerDao.java:128)
at jdk.internal.reflect.GeneratedMethodAccessor910.invoke(Unknown Source)
at java.base/java.lang.reflect.Method.invoke(Method.java:568)
at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:344)
at org.springframework.aop.framework.ReflectiveMethodInvocation.invokeJoinpoint(ReflectiveMethodInvocation.java:198)
at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:163)
at org.springframework.transaction.interceptor.TransactionInterceptor$1.proceedWithInvocation(TransactionInterceptor.java:123)
at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:388)
at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:119)
at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186)
at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:241)
at jdk.proxy11/jdk.proxy11.$Proxy140.findById(Unknown Source)
at com.assurant.inc.sox.ar.service.impl.ReviewerService.retrieveByIdWithSlim(ReviewerService.java:1063)
at com.assurant.inc.sox.ar.service.impl.tasklist.ReviewerTaskListService.buildReviewerTaskListDTO(ReviewerTaskListService.java:130)
at com.assurant.inc.sox.ar.service.impl.tasklist.ReviewerTaskListService.retrieveSavvionTasksForReviewer(ReviewerTaskListService.java:179)
at com.assurant.inc.sox.ar.service.impl.tasklist.ReviewerTaskListService.retrieveTaskListForReviewer(ReviewerTaskListService.java:35)
at com.assurant.inc.sox.ar.client.bean.tasklist.MyTaskListBean.retrieveTaskListDTOs(MyTaskListBean.java:319)
at com.assurant.inc.sox.ar.client.bean.tasklist.AbstractTaskListBean.retrieveTaskLists(AbstractTaskListBean.java:406)
at com.assurant.inc.sox.ar.client.bean.tasklist.AbstractTaskListBean.buildTaskListTable(AbstractTaskListBean.java:289)
at com.assurant.inc.sox.ar.client.bean.tasklist.AbstractTaskListBean.loadBean(AbstractTaskListBean.java:145)
at com.assurant.inc.sox.ar.client.bean.tasklist.MyTaskListBean.loadBean(MyTaskListBean.java:349)
at com.assurant.inc.sox.ar.client.bean.tasklist.AbstractTaskListBean.getTaskListTable(AbstractTaskListBean.java:138)
at com.assurant.inc.sox.ar.client.bean.tasklist.AbstractTaskListBean$$FastClassBySpringCGLIB$$84c722a2.invoke()
at org.springframework.cglib.proxy.MethodProxy.invoke(MethodProxy.java:218)
at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.invokeJoinpoint(CglibAopProxy.java:792)
at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:163)
at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.proceed(CglibAopProxy.java:762)
at org.springframework.aop.support.DelegatingIntroductionInterceptor.doProceed(DelegatingIntroductionInterceptor.java:137)
at org.springframework.aop.support.DelegatingIntroductionInterceptor.invoke(DelegatingIntroductionInterceptor.java:124)
at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186)
at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.proceed(CglibAopProxy.java:762)
at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:707)
at com.assurant.inc.sox.ar.client.bean.tasklist.MyTaskListBean$$EnhancerBySpringCGLIB$$be1495da.getTaskListTable()
at jdk.internal.reflect.GeneratedMethodAccessor900.invoke(Unknown Source)
at java.base/java.lang.reflect.Method.invoke(Method.java:568)
at javax.el.BeanELResolver.getValue(BeanELResolver.java:93)
... 20 more
Caused by: org.hibernate.exception.SQLGrammarException: could not extract ResultSet
at org.hibernate.exception.internal.SQLExceptionTypeDelegate.convert(SQLExceptionTypeDelegate.java:63)
at org.hibernate.exception.internal.StandardSQLExceptionConverter.convert(StandardSQLExceptionConverter.java:37)
at org.hibernate.engine.jdbc.spi.SqlExceptionHelper.convert(SqlExceptionHelper.java:113)
at org.hibernate.engine.jdbc.spi.SqlExceptionHelper.convert(SqlExceptionHelper.java:99)
at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.extract(ResultSetReturnImpl.java:67)
at org.hibernate.loader.plan.exec.internal.AbstractLoadPlanBasedLoader.getResultSet(AbstractLoadPlanBasedLoader.java:390)
at org.hibernate.loader.plan.exec.internal.AbstractLoadPlanBasedLoader.executeQueryStatement(AbstractLoadPlanBasedLoader.java:163)
at org.hibernate.loader.plan.exec.internal.AbstractLoadPlanBasedLoader.executeLoad(AbstractLoadPlanBasedLoader.java:104)
at org.hibernate.loader.entity.plan.AbstractLoadPlanBasedEntityLoader.load(AbstractLoadPlanBasedEntityLoader.java:285)
at org.hibernate.persister.entity.AbstractEntityPersister.doLoad(AbstractEntityPersister.java:4550)
at org.hibernate.persister.entity.AbstractEntityPersister.load(AbstractEntityPersister.java:4540)
at org.hibernate.event.internal.DefaultLoadEventListener.loadFromDatasource(DefaultLoadEventListener.java:571)
at org.hibernate.event.internal.DefaultLoadEventListener.doLoad(DefaultLoadEventListener.java:539)
at org.hibernate.event.internal.DefaultLoadEventListener.load(DefaultLoadEventListener.java:208)
at org.hibernate.event.internal.DefaultLoadEventListener.proxyOrLoad(DefaultLoadEventListener.java:327)
at org.hibernate.event.internal.DefaultLoadEventListener.doOnLoad(DefaultLoadEventListener.java:108)
at org.hibernate.event.internal.DefaultLoadEventListener.onLoad(DefaultLoadEventListener.java:74)
at org.hibernate.event.service.internal.EventListenerGroupImpl.fireEventOnEachListener(EventListenerGroupImpl.java:118)
at org.hibernate.internal.SessionImpl.fireLoadNoChecks(SessionImpl.java:1231)
at org.hibernate.internal.SessionImpl.fireLoad(SessionImpl.java:1220)
at org.hibernate.internal.SessionImpl.access$2100(SessionImpl.java:202)
at org.hibernate.internal.SessionImpl$IdentifierLoadAccessImpl.doLoad(SessionImpl.java:2848)
at org.hibernate.internal.SessionImpl$IdentifierLoadAccessImpl.lambda$load$1(SessionImpl.java:2825)
at org.hibernate.internal.SessionImpl$IdentifierLoadAccessImpl.perform(SessionImpl.java:2781)
at org.hibernate.internal.SessionImpl$IdentifierLoadAccessImpl.load(SessionImpl.java:2825)
at org.hibernate.internal.SessionImpl.get(SessionImpl.java:1019)
at org.springframework.orm.hibernate5.HibernateTemplate.lambda$get$0(HibernateTemplate.java:456)
at org.springframework.orm.hibernate5.HibernateTemplate.doExecute(HibernateTemplate.java:367)
... 59 more
Caused by: java.sql.SQLSyntaxErrorException: ORA-00942: table or view does not exist
at oracle.jdbc.driver.T4CTTIoer.processError(T4CTTIoer.java:450)
at oracle.jdbc.driver.T4CTTIoer.processError(T4CTTIoer.java:399)
at oracle.jdbc.driver.T4C8Oall.processError(T4C8Oall.java:1059)
at oracle.jdbc.driver.T4CTTIfun.receive(T4CTTIfun.java:522)
at oracle.jdbc.driver.T4CTTIfun.doRPC(T4CTTIfun.java:257)
at oracle.jdbc.driver.T4C8Oall.doOALL(T4C8Oall.java:587)
at oracle.jdbc.driver.T4CPreparedStatement.doOall8(T4CPreparedStatement.java:225)
at oracle.jdbc.driver.T4CPreparedStatement.doOall8(T4CPreparedStatement.java:53)
at oracle.jdbc.driver.T4CPreparedStatement.executeForDescribe(T4CPreparedStatement.java:774)
at oracle.jdbc.driver.OracleStatement.executeMaybeDescribe(OracleStatement.java:925)
at oracle.jdbc.driver.OracleStatement.doExecuteWithTimeout(OracleStatement.java:1111)
at oracle.jdbc.driver.OraclePreparedStatement.executeInternal(OraclePreparedStatement.java:4798)
at oracle.jdbc.driver.OraclePreparedStatement.executeQuery(OraclePreparedStatement.java:4845)
at oracle.jdbc.driver.OraclePreparedStatementWrapper.executeQuery(OraclePreparedStatementWrapper.java:1501)
at com.ibm.ws.rsadapter.jdbc.WSJdbcPreparedStatement.executeQuery(WSJdbcPreparedStatement.java:477)
at [internal classes]
at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.extract(ResultSetReturnImpl.java:57)
... 82 more



package com.assurant.inc.sox.ar.dto;

import java.util.Calendar;
import java.util.Date;

import com.assurant.inc.sox.ar.dto.enums.reviewer.ReviewerStatusCode;
import com.assurant.inc.sox.domain.ar.Application;
import com.assurant.inc.sox.domain.ar.Department;
import com.assurant.inc.sox.domain.ar.Division;
import com.assurant.inc.sox.domain.ar.Reviewer;
import com.assurant.inc.sox.domain.ar.UserStatus;

public class ReviewerDTO {

	public static final String OVERDUE = "1";
	public static final String DAYS_DUE = "3";
	public static final String WEEK_DUE = "7";
	public static final String NO_WARNING = "";

	private final Reviewer reviewer;
	private final CodeDTO rejectReasonCode;
	private final CodeDTO statusCode;
	private final String bundleName;
	private ConflictDTO conflict;
	private String ownerName;
	

	// additional fields
	private int numberOfDepartments;
	private String distinctEnvName;
	private String escalationMgrName;
	private boolean hasAccessToApplication;

	public ReviewerDTO(Reviewer reviewer, CodeDTO rejectCode, CodeDTO statusCode, String bundleName) {
		this.reviewer = reviewer;
		this.rejectReasonCode = rejectCode;
		this.statusCode = statusCode;
		this.bundleName = bundleName;
	}
	
	public ReviewerDTO(Reviewer reviewer, String bundleName) {
		this.reviewer = reviewer;
		this.bundleName = bundleName;
		this.rejectReasonCode = null;
		this.statusCode = null;
	}	

	public Reviewer getReviewer() {
		return this.reviewer;
	}

	public Long getReviewerId() {
		return this.reviewer.getId();
	}

	/**
	 * @return the date that the review was created.
	 * @see com.assurant.inc.sox.domain.ar.Reviewer#getCreatedDate()
	 */
	public Date getCreatedDate() {
		return this.reviewer.getCreatedDate();
	}

	/**
	 * @return the name of the reviewer.
	 * @see com.assurant.inc.sox.domain.ar.Reviewer#getReviewerName()
	 */
	public String getReviewerName() {
		return this.reviewer.getReviewerName();
	}

	/**
	 * @return comments left by the reviewer
	 * @see com.assurant.inc.sox.domain.ar.Reviewer#getReviewerComment()
	 */
	public String getReviewerComment() {
		return this.reviewer.getReviewerComment();
	}

	public CodeDTO getStatusCode() {
		return statusCode;
	}

	/**
	 * @return the date that the review was rejected.
	 * @see com.assurant.inc.sox.domain.ar.Reviewer#getReviewerRejectDate()
	 */
	public Date getRejectDate() {
		return this.reviewer.getReviewerRejectDate();
	}

	/**
	 * @return the date that the review was released.
	 * @see com.assurant.inc.sox.domain.ar.Reviewer#getReviewerReleaseDate()
	 */
	public Date getReleaseDate() {
		return this.reviewer.getReviewerReleaseDate();
	}

	/**
	 * @return the id of the user that rejected the review.
	 * @see com.assurant.inc.sox.domain.ar.Reviewer#getReviewerRejectById()
	 */
	public String getRejectBy() {
		return this.reviewer.getReviewerRejectBy();
	}

	/**
	 * @return the id of the user that released the review.
	 * @see com.assurant.inc.sox.domain.ar.Reviewer#getReviewerReleaseById()
	 */
	public String getReleaseById() {
		return this.reviewer.getReviewerReleaseBy();
	}

	/**
	 * @return the name of the reviewer's department.
	 * @see com.assurant.inc.sox.domain.ar.Reviewer#getReviewerDepartment()
	 */
	public String getReviewerDepartment() {
		Department department = this.reviewer.getDepartment();
		return (department == null) ? null : department.getName();
	}

	/**
	 * @return the name of the reviewer's division.
	 */
	public String getDivision() {
		Division division = this.reviewer.getDivision();
		return (division == null) ? null : division.getName();
	}

	/**
	 * @return the invalidUser
	 */
	public boolean isInvalidUser() {
		// dont want to expose reviewerto the client objects so added helper method.
		return !(Reviewer.ACTIVE_MANAGER_STATUS.equals(this.getReviewerStatus()));
	}

	public String getReviewerEmailAddress() {
		//return this.reviewer.getReviewerEmailAddress();
		return "Rohit.Bajrolia@assurant.com";
	}

	public String getReviewerStatus() {
		UserStatus userStatus = this.reviewer.getUserStatus();
		return (userStatus == null) ? null : userStatus.getUserStatusDescription();
	}

	/**
	 * @return
	 * @see com.assurant.inc.sox.domain.ar.Reviewer#getUserId()
	 */
	public Long getUserId() {
		return this.reviewer.getUserId();
	}

	/**
	 * @return
	 * @see com.assurant.inc.sox.domain.ar.Reviewer#getRejectCode()
	 */
	public CodeDTO getRejectCode() {
		return this.rejectReasonCode;
	}

	/**
	 * @return
	 * @see com.assurant.inc.sox.domain.ar.Reviewer#getRejectText()
	 */
	public String getRejectText() {
		return this.reviewer.getRejectText();
	}

	public String getApprovedBy() {
		return this.reviewer.getReviewerApprovedBy();
	}

	public Date getApprovedDate() {
		return this.reviewer.getReviewerApprovedDate();
	}

	public Date getDistributionTargetCompleteDate() {
		return this.reviewer.getDistributionTargetCompleteDate();
	}

	public Long getReviewBundleId() {
		return this.reviewer.getReviewBundleId();
	}

	public Date getLastChangedDate() {
		return this.reviewer.getLastChangedDate();
	}

	/**
	 * @return the numberOfReviewedUsers
	 */
	public Long getNumberOfReviewedUsers() {
		return this.reviewer.getNumberOfDirectReports();
	}

	/**
	 * @param numberOfReviewedUsers the numberOfReviewedUsers to set
	 */
	public void setNumberOfReviewedUsers(long numberOfReviewedUsers) {
		this.reviewer.setNumberOfDirectReports(numberOfReviewedUsers);
	}

	public String getBundleName() {
		return this.bundleName;
	}

	public String getApplicationName() {
		Application application = this.reviewer.getApplication();
		return (application == null) ? null : application.getName();
	}

	public Long getApplicationId() {
		Application application = this.reviewer.getApplication();
		return (application == null) ? null : application.getId();
	}

	/**
	 * @return the numberOfDepartments
	 */
	public int getNumberOfDepartments() {
		return this.numberOfDepartments;
	}

	/**
	 * @param numberOfDepartments the numberOfDepartments to set
	 */
	public void setNumberOfDepartments(int numberOfDepartments) {
		this.numberOfDepartments = numberOfDepartments;
	}

	public String getDistinctEmployeeStatuses() {
		return reviewer.getReviewerUserStatusValues();
	}

	public void setDistinctEmployeeStatuses(String distinctEmployeeStatuses) {
		this.reviewer.setReviewerUserStatusValues(distinctEmployeeStatuses);
	}

	public String getOwnerName() {
		return this.ownerName;
	}

	public void setOwnerName(String ownerName) {
		this.ownerName = ownerName;
	}

	public String getDistinctEnvName() {
		return this.distinctEnvName;
	}

	public void setDistinctEnvName(String distinctEnvName) {
		this.distinctEnvName = distinctEnvName;
	}

	public String getAttestedByID() {
		return reviewer.getAttestedByID();
	}

	public Date getAttestedOnDate() {
		return reviewer.getAttestedOnDate();
	}

	public void setAttestedByID(String attestedByID) {
		reviewer.setAttestedByID(attestedByID);
	}

	public void setAttestedOnDate(Date attestedOnDate) {
		reviewer.setAttestedOnDate(attestedOnDate);
	}

	public String getDistributionInstructions() {
		return reviewer.getDistributionInstructions();
	}

	public boolean isActionRequired() {
		return "Y".equalsIgnoreCase(reviewer.getActionsRequired());
	}

	private long daysUntil(Date d) {
		Calendar testDate = Calendar.getInstance();
		Calendar nowDate = Calendar.getInstance();
		testDate.setTime(d);
		zeroTimes(testDate);
		zeroTimes(nowDate);
		long dateMillis = testDate.getTimeInMillis();
		long nowMillis = nowDate.getTimeInMillis();
		final long millisPerDay = 1000L * 60L * 60L * 24L;
		return (dateMillis - nowMillis) / millisPerDay;
	}

	private void zeroTimes(Calendar aCalendar) {
		aCalendar.set(Calendar.MILLISECOND, 0);
		aCalendar.set(Calendar.SECOND, 0);
		aCalendar.set(Calendar.MINUTE, 0);
		aCalendar.set(Calendar.HOUR_OF_DAY, 0);
	}

	public String calculateDaysWarning() {
		if (getDistributionTargetCompleteDate() == null || reviewer.getAttestedOnDate() != null) {
			return NO_WARNING;
		}

		long duration = daysUntil(getDistributionTargetCompleteDate());
		if (duration < 0) {
			return OVERDUE;
		}
		if (duration <= 3) {
			return DAYS_DUE;
		}
		if (duration <= 7) {
			return WEEK_DUE;
		}
		return NO_WARNING;

	}

	/**
	 * If not attested and past due date, is overdue. Does not return true if rejected.
	 * 
	 * @return
	 */
	public boolean isOverdue() {
		return !ReviewerStatusCode.REJECTED.equals(getReviewerStatus()) && reviewer.getAttestedOnDate() == null
		    && calculateDaysWarning().equals(OVERDUE);
	}

	public ConflictDTO getConflict() {
		return conflict;
	}

	public void setConflict(ConflictDTO conflict) {
		this.conflict = conflict;
	}

	public Long getEscalationMgrId() {
		return this.reviewer.getEscalationMgrId();
	}

	public void setEscalationMgrId(Long managerId) {
		reviewer.setEscalationMgrId(managerId);
	}

	public String getEscalationMgrName() {
		return escalationMgrName;
	}

	public void setEscalationMgrName(String escalationMgrName) {
		this.escalationMgrName = escalationMgrName;
	}

	public String getConflictType() {
		return (this.conflict == null) ? null : this.conflict.getConflictTypeText();
	}

	@Override
	public String toString() {
		return new StringBuffer(128).append("Reviewer -- Id: ").append(this.getReviewerId()).append(" Name: ").append(
		    this.getReviewerName()).append(" Bundle Id: ").append(this.getReviewBundleId()).append(" Bundle Name: ").append(
		    this.bundleName).append(" Status: ").append((this.statusCode == null) ? null : this.statusCode.getValue()).toString();
	}

	public Date getLast250AccessChangedDate() {
		return reviewer.getLast250AccessChangedDate();
	}

	public boolean isHasAccessToApplication() {
		return hasAccessToApplication;
	}

	public void setHasAccessToApplication(boolean hasAccessToApplication) {
		this.hasAccessToApplication = hasAccessToApplication;
	}

	public String getReviewerLastStatusCd() {
		return reviewer.getReviewerLastStatusCd();
	}

}

package com.assurant.inc.sox.domain.ar;

import java.io.Serializable;
import java.util.Date;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.FetchType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.ManyToOne;
import javax.persistence.Table;
import javax.persistence.Temporal;
import javax.persistence.TemporalType;

/**
 * Reviewer generated by hbm2java
 */
@Entity
@Table(name = "SDY230_REVIEWER")
public class Reviewer implements Serializable, IAuditableAr {

	private static final long serialVersionUID = 1L;

	public static final String TABLE_NAME = "SDT230_REVIEWER";
	public static final String REJECT_CODE_COLUMN_NAME = "REVIEWER_RJCT_CD";
	public static final String STATUS_CODE_COLUMN_NAME = "REVIEWER_LAST_STATUS_CD";

	/**
	 * lastStatusCd value for a pending manager review. Value = "PEND".
	 */
	public static final String PENDING_REVIEWER_STATUS_CD = "PEND";
	/**
	 * lastStatusCd value for a released manager review. Value = "RELE".
	 */
	public static final String RELEASED_REVIEWER_STATUS_CD = "RELE";
	/**
	 * lastStatusCd value for a rejected manager review. Value = "RJCT".
	 */
	public static final String REJECTED_REVIEWER_STATUS_CD = "RJCT";
	/**
	 * lastStatusCd value for an approved manager review. Value = "APPR".
	 */
	public static final String APPROVED_REVIEWER_STATUS_CD = "APPR";

	/**
	 * managerStatus value for an active user. Value = "ACTIVE".
	 */
	public static final String ACTIVE_MANAGER_STATUS = "ACTIVE";

	public static final String ACTION_REQUIRED_FLAG = "Y";

	private Long id;
	private Long reviewBundleId;
	private UserStatus userStatus;
	private Division division;
	private Department department;
	private Application application;
	private Long userId;
	private String reviewerName;
	private Long numberOfDirectReports;
	private String reviewerUserStatusValues;
	private String reviewerLastStatusCd;
	private Date reviewerApprovedDate;
	private String reviewerApprovedBy;
	private Date reviewerReleaseDate;
	private String reviewerReleaseBy;
	private Date reviewerRejectDate;
	private String reviewerRejectBy;
	private String rejectCode;
	private String rejectText;
	private String createdBy;
	private Date createdDate;
	private String lastChangedBy;
	private Date lastChangedDate;
	private String reviewerEmailAddress;
	private String reviewerComment;
	private Date distributionTargetCompleteDate;
	private String distributionInstructions;
	private String attestedByID;
	private Date attestedOnDate;
	private String actionsRequired;
	private Long escalationMgrId;
	private Conflict conflict;
	private Date last250AccessChangedDate;

	@Id
	@Column(name = "REVIEWER_ID", unique = true, nullable = false, insertable = true, updatable = false, precision = 38, scale = 0)
	public Long getId() {
		return this.id;
	}

	public void setId(Long reviewerId) {
		this.id = reviewerId;
	}

	@Column(name = "REVIEW_BNDL_ID", nullable = false, insertable = true, updatable = false, precision = 38, scale = 0)
	public Long getReviewBundleId() {
		return this.reviewBundleId;
	}

	public void setReviewBundleId(Long reviewBundleId) {
		this.reviewBundleId = reviewBundleId;
	}

	@ManyToOne(fetch = FetchType.EAGER, optional = false)
	@JoinColumn(name = "APPLCTN_ID", insertable = true, updatable = false)
	public Application getApplication() {
		return application;
	}

	public void setApplication(Application application) {
		this.application = application;
	}

	@Column(name = "USER_ID", nullable = false, insertable = true, updatable = true, precision = 38, scale = 0)
	public Long getUserId() {
		return this.userId;
	}

	public void setUserId(Long userId) {
		this.userId = userId;
	}

	@Column(name = "REVIEWER_NM", nullable = false, insertable = true, updatable = true, length = 30)
	public String getReviewerName() {
		return this.reviewerName;
	}

	public void setReviewerName(String reviewerName) {
		this.reviewerName = reviewerName;
	}

	@Column(name = "REVIEWER_LAST_STATUS_CD", insertable = true, updatable = true, length = 4)
	public String getReviewerLastStatusCd() {
		return this.reviewerLastStatusCd;
	}

	public void setReviewerLastStatusCd(String reviewManagerLastStatusCd) {
		this.reviewerLastStatusCd = reviewManagerLastStatusCd;
	}

	@Temporal(TemporalType.TIMESTAMP)
	@Column(name = "REVIEWER_APPRVD_DT", insertable = true, updatable = true, length = 7)
	public Date getReviewerApprovedDate() {
		return this.reviewerApprovedDate;
	}

	public void setReviewerApprovedDate(Date reviewerApprovedDate) {
		this.reviewerApprovedDate = reviewerApprovedDate;
	}

	@Column(name = "REVIEWER_APPRVD_BY", insertable = true, updatable = true, length = 10)
	public String getReviewerApprovedBy() {
		return this.reviewerApprovedBy;
	}

	public void setReviewerApprovedBy(String reviewerApprovedBy) {
		this.reviewerApprovedBy = reviewerApprovedBy;
	}

	@Temporal(TemporalType.TIMESTAMP)
	@Column(name = "REVIEWER_RLS_DT", insertable = true, updatable = true, length = 7)
	public Date getReviewerReleaseDate() {
		return this.reviewerReleaseDate;
	}

	public void setReviewerReleaseDate(Date reviewerReleaseDate) {
		this.reviewerReleaseDate = reviewerReleaseDate;
	}

	@Temporal(TemporalType.TIMESTAMP)
	@Column(name = "REVIEWER_RJCT_DT", insertable = true, updatable = true, length = 7)
	public Date getReviewerRejectDate() {
		return this.reviewerRejectDate;
	}

	public void setReviewerRejectDate(Date reviewerRejectDate) {
		this.reviewerRejectDate = reviewerRejectDate;
	}

	@Column(name = "CRTD_BY", insertable = true, updatable = false, nullable = false, length = 40)
	public String getCreatedBy() {
		return this.createdBy;
	}

	public void setCreatedBy(String createdBy) {
		this.createdBy = createdBy;
	}

	@Temporal(TemporalType.TIMESTAMP)
	@Column(name = "CRTD_DT", insertable = true, updatable = false, nullable = false, length = 7)
	public Date getCreatedDate() {
		return this.createdDate;
	}

	public void setCreatedDate(Date createdDate) {
		this.createdDate = createdDate;
	}

	@Column(name = "LST_CHNGD_BY", insertable = true, updatable = true, length = 40)
	public String getLastChangedBy() {
		return this.lastChangedBy;
	}

	public void setLastChangedBy(String lastChangedBy) {
		this.lastChangedBy = lastChangedBy;
	}

	@Temporal(TemporalType.TIMESTAMP)
	@Column(name = "LST_CHNGD_DT", insertable = true, updatable = true, length = 7)
	public Date getLastChangedDate() {
		return this.lastChangedDate;
	}

	public void setLastChangedDate(Date lastChangedDate) {
		this.lastChangedDate = lastChangedDate;
	}

	@Column(name = "REVIEWER_CMMNT", insertable = true, updatable = true, length = 250)
	public String getReviewerComment() {
		return this.reviewerComment;
	}

	public void setReviewerComment(String reviewerComment) {
		this.reviewerComment = reviewerComment;
	}

	@Column(name = "REVIEWER_RLS_BY", insertable = true, updatable = true, length = 10)
	public String getReviewerReleaseBy() {
		return this.reviewerReleaseBy;
	}

	public void setReviewerReleaseBy(String reviewerReleaseBy) {
		this.reviewerReleaseBy = reviewerReleaseBy;
	}

	@Column(name = "REVIEWER_RJCT_BY", insertable = true, updatable = true, length = 10)
	public String getReviewerRejectBy() {
		return this.reviewerRejectBy;
	}

	public void setReviewerRejectBy(String reviewerRejectBy) {
		this.reviewerRejectBy = reviewerRejectBy;
	}

	@Column(name = "REVIEWER_EMAIL_ADDR", insertable = true, updatable = true, length = 80)
	public String getReviewerEmailAddress() {
		return this.reviewerEmailAddress;
	}

	public void setReviewerEmailAddress(String reviewerEmailAddress) {
		this.reviewerEmailAddress = reviewerEmailAddress;
	}

	@Column(name = "REVIEWER_RJCT_CD", insertable = true, updatable = true, length = 4)
	public String getRejectCode() {
		return this.rejectCode;
	}

	public void setRejectCode(String rejectCode) {
		this.rejectCode = rejectCode;
	}

	@Column(name = "REVIEWER_RJCT_TXT", insertable = true, updatable = true, length = 100)
	public String getRejectText() {
		return this.rejectText;
	}

	public void setRejectText(String rejectText) {
		this.rejectText = rejectText;
	}

	@Column(name = "NBR_OF_DIRECT_RPTS", insertable = true, updatable = true, precision = 4, scale = 0)
	public Long getNumberOfDirectReports() {
		return numberOfDirectReports;
	}

	public void setNumberOfDirectReports(Long numberOfDirectReports) {
		this.numberOfDirectReports = numberOfDirectReports;
	}

	@Column(name = "REVIEWER_USER_STATUSES_VALUES", insertable = true, updatable = true, length = 80)
	public String getReviewerUserStatusValues() {
		return reviewerUserStatusValues;
	}

	public void setReviewerUserStatusValues(String reviewerUserStatusValues) {
		this.reviewerUserStatusValues = reviewerUserStatusValues;
	}

	@Column(name = "REVIEWER_INSTRCTNS", insertable = true, updatable = true, length = 4000)
	public String getDistributionInstructions() {
		return this.distributionInstructions;
	}

	public void setDistributionInstructions(String distributionInstructions) {
		this.distributionInstructions = distributionInstructions;
	}

	@Temporal(TemporalType.TIMESTAMP)
	@Column(name = "REVIEWER_TRGTD_DUE_DT", insertable = true, updatable = true, length = 7)
	public Date getDistributionTargetCompleteDate() {
		return this.distributionTargetCompleteDate;
	}

	public void setDistributionTargetCompleteDate(Date distributionTargetCompleteDate) {
		this.distributionTargetCompleteDate = distributionTargetCompleteDate;
	}

	@ManyToOne(fetch = FetchType.EAGER)
	@JoinColumn(name = "DPRTMNT_ID", insertable = true, updatable = true)
	public Department getDepartment() {
		return this.department;
	}

	@ManyToOne(fetch = FetchType.EAGER)
	@JoinColumn(name = "DIVISN_ID", insertable = true, updatable = true)
	public Division getDivision() {
		return this.division;
	}

	public void setDepartment(Department department) {
		this.department = department;
	}

	public void setDivision(Division division) {
		this.division = division;
	}

	@ManyToOne(fetch = FetchType.EAGER)
	@JoinColumn(name = "USR_STATUS_ID", insertable = true, updatable = true)
	public UserStatus getUserStatus() {
		return this.userStatus;
	}

	public void setUserStatus(UserStatus userStatus) {
		this.userStatus = userStatus;
	}

	@Override
	public String toString() {
		return new StringBuffer().append("reviewerId: ").append(this.id).append(" reviewBundleId: ").append(this.reviewBundleId)
		    .append(" managerId: ").append(this.userId).append(" reviewManagerStatus: ").append(this.reviewerLastStatusCd).toString();
	}

	@Column(name = "ATTSTD_BY_ID", insertable = true, updatable = true, length = 40)
	public String getAttestedByID() {
		return this.attestedByID;
	}

	public void setAttestedByID(String attestedByID) {
		this.attestedByID = attestedByID;
	}

	@Temporal(TemporalType.TIMESTAMP)
	@Column(name = "ATTSTD_DT", insertable = true, updatable = true, length = 7)
	public Date getAttestedOnDate() {
		return this.attestedOnDate;
	}

	public void setAttestedOnDate(Date attestedOnDate) {
		this.attestedOnDate = attestedOnDate;
	}

	@Column(name = "ACTIONS_RQRD_FLG", insertable = true, updatable = true, length = 1)
	public String getActionsRequired() {
		return actionsRequired;
	}

	public void setActionsRequired(String actionsRequired) {
		this.actionsRequired = actionsRequired;
	}

	@Column(name = "MGR_USER_ID", precision = 38, scale = 0)
	public Long getEscalationMgrId() {
		return escalationMgrId;
	}

	public void setEscalationMgrId(Long escalationMgrId) {
		this.escalationMgrId = escalationMgrId;
	}

	@ManyToOne(fetch = FetchType.EAGER)
	@JoinColumn(name = "CNFLCT_ID", insertable = false, updatable = false)
	public Conflict getConflict() {
		return conflict;
	}

	public void setConflict(Conflict conflict) {
		this.conflict = conflict;
	}

	@Temporal(TemporalType.TIMESTAMP)
	@Column(name = "LST_250_ACCESS_CHNGD_DT", insertable = true, updatable = true, length = 7)
	public Date getLast250AccessChangedDate() {
		return last250AccessChangedDate;
	}

	public void setLast250AccessChangedDate(Date last250AccessChangedDate) {
		this.last250AccessChangedDate = last250AccessChangedDate;
	}
}

package com.assurant.inc.sox.ar.dto;

import com.assurant.inc.sox.domain.ar.Code;

public class CodeDTO {

	private final Code code;
	

	public CodeDTO(Code code) {
		this.code = code;
	}

	public String getValue() {
		return this.code.getPk().getValue();
	}

	public String getDisplay() {
		return this.code.getDisplay();
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((code == null) ? 0 : code.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		final CodeDTO other = (CodeDTO) obj;
		if (code == null) {
			if (other.code != null)
				return false;
		} else if (!code.equals(other.code))
			return false;
		return true;
	}
}

package com.assurant.inc.sox.domain.ar;

import java.io.Serializable;
import java.util.Date;

import javax.persistence.Column;
import javax.persistence.EmbeddedId;
import javax.persistence.Entity;
import javax.persistence.Table;
import javax.persistence.Temporal;
import javax.persistence.TemporalType;

@Entity
@Table(name = "SDY005_RFRNC_CODES")
public class Code implements Serializable{
	private CodePk pk;
	private String display;
	private String createdBy;
	private Date createdDate;
	private String lastChangedBy;
	private Date lastChangedDate;
	private String deleteFlag;

	@EmbeddedId
	public CodePk getPk() {
		return pk;
	}

	public void setPk(CodePk pk) {
		this.pk = pk;
	}

	@Column(name = "RFRNC_DESCR", unique = false, nullable = true, length = 80)
	public String getDisplay() {
		return display;
	}

	public void setDisplay(String display) {
		this.display = display;
	}

	@Column(name = "CRTD_BY", unique = false, nullable = false, length = 40)
	public String getCreatedBy() {
		return createdBy;
	}

	public void setCreatedBy(String createdBy) {
		this.createdBy = createdBy;
	}

	@Temporal(TemporalType.TIMESTAMP)
	@Column(name = "CRTD_DT", unique = false, nullable = false, length = 7)
	public Date getCreatedDate() {
		return createdDate;
	}

	public void setCreatedDate(Date createdDate) {
		this.createdDate = createdDate;
	}

	@Column(name = "LST_CHNGD_BY", unique = false, nullable = true, length = 40)
	public String getLastChangedBy() {
		return lastChangedBy;
	}

	public void setLastChangedBy(String lastChangedBy) {
		this.lastChangedBy = lastChangedBy;
	}

	@Temporal(TemporalType.TIMESTAMP)
	@Column(name = "LST_CHNGD_DT", unique = false, nullable = true, length = 7)
	public Date getLastChangedDate() {
		return lastChangedDate;
	}

	public void setLastChangedDate(Date lastChangedDate) {
		this.lastChangedDate = lastChangedDate;
	}

	@Column(name = "DLT_FLG", unique = false, nullable = false, length = 1)
	public String getDeleteFlag() {
		return deleteFlag;
	}

	public void setDeleteFlag(String deleteFlag) {
		this.deleteFlag = deleteFlag;
	}

	@Override
  public int hashCode() {
	  final int prime = 31;
	  int result = 1;
	  result = prime * result + ((pk == null) ? 0 : pk.hashCode());
	  return result;
  }

	@Override
  public boolean equals(Object obj) {
	  if (this == obj)
		  return true;
	  if (obj == null)
		  return false;
	  if (getClass() != obj.getClass())
		  return false;
	  final Code other = (Code) obj;
	  if (pk == null) {
		  if (other.pk != null)
			  return false;
	  } else if (!pk.equals(other.pk))
		  return false;
	  return true;
  }

	@Override
	public String toString() {
		return new StringBuffer().append("TableName: ").append(this.pk.getTableName()).append(" ColumnName: ").append(this.pk.getColumnName()).append(
		    " Value: ").append(this.pk.getValue()).toString();
	}
}

package com.assurant.inc.sox.domain.ar;

import java.io.Serializable;
import java.util.Date;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.FetchType;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.ManyToOne;
import javax.persistence.SequenceGenerator;
import javax.persistence.Table;
import javax.persistence.Temporal;
import javax.persistence.TemporalType;

/**
 * ReviewBundle generated by hbm2java
 */
@Entity
@Table(name = "SDY220_REVIEW_BNDL")
public class ReviewBundle implements Serializable, IAuditableAr {

	private static final long serialVersionUID = 1L;

	public static final String TABLE_NAME = "SDT220_REVIEW_BNDL";
	public static final String STATUS_COLUMN_NAME = "BNDL_STATUS_CD";

	/**
	 * Review bundle status code that represents a status of in process. An inprocess bundle is one that the data has not been
	 * retrieved yet. Value = "INPR".
	 */
	public static final String IN_PROCESS_STATUS_CODE = "INPR";

	/**
	 * Review bundle status code that represents a status of approved. Value = "APPR".
	 */
	public static final String APPROVED_STATUS_CODE = "APPR";
	/**
	 * Review bundle status code that represents a status of rejected. Value = "RJCT".
	 */
	public static final String REJECTED_STATUS_CODE = "RJCT";
	/**
	 * Review bundle status code that represents a status of rejected. Value = "RJCT".
	 */
	public static final String REJECTED_W_NO_CONFLICTS_STATUS_CODE = "RJNC";	
	/**
	 * Review bundle status code that represents a status of submitted. Value = "SBMT".
	 */
	public static final String SUBMITTED_STATUS_CODE = "SBMT";
	/**
	 * Review bundle status code that represents a status of completed. Value = "CMPT".
	 */
	public static final String COMPLETED_STATUS_CODE = "CMPT";

	private Long id;
	private Long reviewId;
	private String taskProcessId;
	private String bundleStatusCode;
	private Date etlLoadDate;
	private Date luadCycleDate;
	private String createdBy;
	private Date createdDate;
	private String lastChangedBy;
	private Date lastChangedDate;
	private String deleteFlag;
	private Conflict conflict;

	@Id
	@SequenceGenerator(name = "SEQ", sequenceName = "SDQ220_REVIEW_BNDL_ID", allocationSize = 1)
	@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "SEQ")
	@Column(name = "REVIEW_BNDL_ID", unique = true, nullable = false, precision = 38, scale = 0)
	public Long getId() {
		return this.id;
	}

	public void setId(Long reviewBundleId) {
		this.id = reviewBundleId;
	}

	@Column(name = "REVIEW_ID", nullable = false, precision = 38, scale = 0)
	public Long getReviewId() {
		return this.reviewId;
	}

	public void setReviewId(Long review) {
		this.reviewId = review;
	}

	@Column(name = "SAVVION_PRCSS_ID", nullable = true, unique = false, length = 40)
	public String getTaskProcessId() {
		return this.taskProcessId;
	}

	public void setTaskProcessId(String taskProcessId) {
		this.taskProcessId = taskProcessId;
	}

	@Column(name = "BNDL_STATUS_CD", length = 4)
	public String getBundleStatusCode() {
		return this.bundleStatusCode;
	}

	public void setBundleStatusCode(String bundleStatusCode) {
		this.bundleStatusCode = bundleStatusCode;
	}

	@Column(name = "CRTD_BY", nullable = false, length = 40)
	public String getCreatedBy() {
		return this.createdBy;
	}

	public void setCreatedBy(String createdBy) {
		this.createdBy = createdBy;
	}

	@Temporal(TemporalType.TIMESTAMP)
	@Column(name = "CRTD_DT", nullable = false, length = 7)
	public Date getCreatedDate() {
		return this.createdDate;
	}

	public void setCreatedDate(Date createdDate) {
		this.createdDate = createdDate;
	}

	@Column(name = "LST_CHNGD_BY", length = 40)
	public String getLastChangedBy() {
		return this.lastChangedBy;
	}

	public void setLastChangedBy(String lastChangedBy) {
		this.lastChangedBy = lastChangedBy;
	}

	@Temporal(TemporalType.TIMESTAMP)
	@Column(name = "LST_CHNGD_DT", length = 7)
	public Date getLastChangedDate() {
		return this.lastChangedDate;
	}

	public void setLastChangedDate(Date lastChangedDate) {
		this.lastChangedDate = lastChangedDate;
	}

	@Temporal(TemporalType.TIMESTAMP)
	@Column(name = "ETL_LOAD_DT", length = 7)
	public Date getEtlLoadDate() {
		return etlLoadDate;
	}

	public void setEtlLoadDate(Date etlLoadDate) {
		this.etlLoadDate = etlLoadDate;
	}

	@Temporal(TemporalType.TIMESTAMP)
	@Column(name = "LUAD_CYCLE_DT", length = 7)
	public Date getLuadCycleDate() {
		return luadCycleDate;
	}

	public void setLuadCycleDate(Date luadCycleDate) {
		this.luadCycleDate = luadCycleDate;
	}

	@Column(name = "DLT_FLG", length = 1)
	public String getDeleteFlag() {
		return this.deleteFlag;
	}

	public void setDeleteFlag(String deleteFlag) {
		this.deleteFlag = deleteFlag;
	}

	@Override
	public String toString() {
		return new StringBuffer().append("reviewBundleId: ").append(this.id).append(" reviewId: ").append(this.reviewId).append(
		    " taskProcessId: ").append(this.taskProcessId).append(" bundleStatusCode: ").append(this.bundleStatusCode)
		    .toString();
	}
	
	@ManyToOne(fetch = FetchType.EAGER)
	@JoinColumn(name = "CNFLCT_ID")
	public Conflict getConflict() {
		return conflict;
	}

	public void setConflict(Conflict conflict) {
		this.conflict = conflict;
	}
}

 Caused by: java.lang.NullPointerException: Cannot invoke "com.assurant.inc.sox.domain.ar.CodePk.getValue()" because the return value of "com.assurant.inc.sox.domain.ar.Code.getPk()" is null
[INFO]  at com.assurant.inc.sox.ar.dto.CodeDTO.getValue(CodeDTO.java:15)
[INFO]  at com.assurant.inc.sox.ar.client.ui.tasklist.BundleTaskListUI.isClickable(BundleTaskListUI.java:33)
[INFO]  at jdk.internal.reflect.GeneratedMethodAccessor1402.invoke(Unknown Source)
[INFO]  at java.base/java.lang.reflect.Method.invoke(Method.java:568)
[INFO]  at javax.el.BeanELResolver.getValue(BeanELResolver.java:93)
[INFO]  ... 20 more
