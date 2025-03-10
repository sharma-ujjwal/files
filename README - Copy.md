Exception thrown by application class 'javax.faces.webapp.FacesServlet.service:236'
javax.servlet.ServletException: javax.el.PropertyNotFoundException: Target Unreachable, identifier 'taskListBeanName' resolved to null
at javax.faces.webapp.FacesServlet.service(FacesServlet.java:236)
at [internal classes]
at com.assurant.inc.sox.ar.servlets.RedirectServlet.doPost(RedirectServlet.java:73)
at com.assurant.inc.sox.ar.servlets.BaseServlet.doGet(BaseServlet.java:17)
at javax.servlet.http.HttpServlet.service(HttpServlet.java:686)
at [internal classes]
Caused by: org.apache.myfaces.view.facelets.el.ContextAwarePropertyNotFoundException: javax.el.PropertyNotFoundException: Target Unreachable, identifier 'taskListBeanName' resolved to null
at org.apache.myfaces.view.facelets.el.ContextAwareTagValueExpression.setValue(ContextAwareTagValueExpression.java:150)
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

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html
		xmlns="http://www.w3.org/1999/xhtml"
		xmlns:ui="http://java.sun.com/jsf/facelets"
		xmlns:h="http://xmlns.jcp.org/jsf/html"
		xmlns:f="http://java.sun.com/jsf/core"
		xmlns:rich="http://richfaces.org/rich"
		xmlns:a4j="http://richfaces.org/a4j">

<ui:composition template="../common/main.xhtml">
	<ui:define name="body">
		<div id="taskListDiv" class="inline">
			<h:outputLabel id="taskListLabel" styleClass="sectionHeader" value="Task List"/>
			<a4j:status id="taskListStatus">
				<f:facet name="start"><h:graphicImage id="loaderImage" value="/images/loader.gif"/></f:facet>
			</a4j:status>
		</div>
		<rich:tabPanel id="taskListTabPanel" switchType="ajax" tabClass="tabStyle"
					   selectedTab="#{sessionDataBean.selectedTabId}">

			<rich:tab id="myTaskTab" label="My Tasks"
					  action="#{sessionDataBean.doMyTaskListTabSwitch}">
				<ui:include src="tasksTemplateComponent.xhtml">
					<ui:param name="taskListBeanName" value="#{myTaskListBean}"/>
					<ui:param name="idPrefix" value="#{myTaskListBean.idPrefix}"/>
				</ui:include>
			</rich:tab>

			<rich:tab id="reviewerTaskTab" label="Reviewer Tasks"
					  action="#{sessionDataBean.doAllTaskListTabSwitch}" rendered="#{sessionDataBean.itComplianceUser}">
				<ui:include src="selectableTasksTemplateComponent.xhtml">
					<ui:param name="taskListBeanName" value="#{allTaskListBean}"/>
					<ui:param name="idPrefix" value="#{allTaskListBean.idPrefix}"/>
				</ui:include>
			</rich:tab>

			<rich:tab id="teamTaskTab" label="My Team's Tasks"
					  action="#{sessionDataBean.doBigBrotherTaskListTabSwitch}" rendered="#{sessionDataBean.reviewer}">
				<ui:include src="tasksTemplateComponent.xhtml">
					<ui:param name="taskListBeanName" value="#{bigBrotherTaskListBean}"/>
					<ui:param name="idPrefix" value="#{bigBrotherTaskListBean.idPrefix}"/>
				</ui:include>
			</rich:tab>

			<rich:tab id="rejectedAccessesTaskTab" label="Rejected Accesses"
					  action="#{sessionDataBean.doRejectedAccessesTaskListTabSwitch}" rendered="#{sessionDataBean.itComplianceUser}">
				<ui:include src="tasksTemplateComponent.xhtml">
					<ui:param name="taskListBeanName" value="#{rejectedAccessesTaskListBean}"/>
					<ui:param name="idPrefix" value="#{rejectedAccessesTaskListBean.idPrefix}"/>
				</ui:include>
			</rich:tab>

		</rich:tabPanel>
	</ui:define>

	<ui:define name="modalPanels">
		<a4j:outputPanel ajaxRendered="true" id="reviewDetailsModalAjaxPanel">
			<ui:include src="reassignReviewerModalPanelComponent.xhtml"/>
		</a4j:outputPanel>
		<a4j:outputPanel ajaxRendered="true" id="myTaskLockPanelModalAjaxPanel">
			<ui:include src="myTaskBreakLockModalPanelComponent.xhtml"/>
		</a4j:outputPanel>
		<a4j:outputPanel ajaxRendered="true" id="rejectAccessLockPanelModalAjaxPanel">
			<ui:include src="rejectAccessBreakLockModalPanelComponent.xhtml"/>
		</a4j:outputPanel>
	</ui:define>

</ui:composition>

</html>

<html xmlns="http://www.w3.org/1999/xhtml" 
	xmlns:ui="http://java.sun.com/jsf/facelets"
	xmlns:h="http://java.sun.com/jsf/html" 
	xmlns:f="http://java.sun.com/jsf/core" 
	xmlns:rich="http://richfaces.org/rich"
	xmlns:a4j="http://richfaces.org/a4j">
	
<!-- 
Include this xhtml file with ui:include tags and provide the following parameters:

* taskListBeanName - A backing bean that is an instance of a concrete AbstractTaskListBean.

* idPrefix - A unique id prefix for the field ids of the page.
-->

<ui:composition>
		<div style="text-align:right;">
			<h:outputText value="Search by: " />
			
			<h:selectOneMenu id="#{idPrefix}FilterType" value="#{taskListBeanName.taskListFilterTypeCode}" 
				styleClass="fieldValue" >
				<f:selectItems value="#{taskListBeanName.avaliableTaskListFilterTypes}"/>
				<a4j:ajax event="change" render="#{idPrefix}FilterValue"/>
			</h:selectOneMenu>	
			
<!--			<rich:spacer width="5" />	-->
			<br/>
			<h:outputText value=" Search for: " />
			
			<a4j:outputPanel ajaxRendered="true" id="#{idPrefix}searchForPanel">
				<h:selectOneMenu id="#{idPrefix}FilterValue" value="#{taskListBeanName.taskListFilterValue}"
					styleClass="fieldValue"
					rendered="#{taskListBeanName.filterIsNotText}" >
					<f:selectItems value="#{taskListBeanName.filterValues}" /> 
				</h:selectOneMenu>
						
				<h:inputText id="#{idPrefix}FilterTextValue" value="#{taskListBeanName.taskListFilterTextValue}" styleClass="fieldValue"
					rendered="#{taskListBeanName.filterIsText}" />
			</a4j:outputPanel>
			
<!--			<rich:spacer width="10" />				-->
			<br/>
			<a4j:commandButton value="Go" action="#{taskListBeanName.doApplyFilter}" />
<!--			<rich:spacer width="5" /> -->
			<br/>
			<a4j:commandButton value="Clear" action="#{taskListBeanName.doClearFilterFields}"
				reRender="#{idPrefix}searchForPanel,#{idPrefix}FilterType,#{idPrefix}FilterValue"/>
		</div>
		<div style="text-align:right;">
			<a4j:commandLink value="Clear Filter" action="#{taskListBeanName.doClearFilter}"
				reRender="#{idPrefix}FilterType, #{idPrefix}FilterValue"/>
			<h:outputText value=" | " />
			<a4j:commandLink value="Refresh Tasklist" action="#{taskListBeanName.doRefreshTasklist}"
				reRender="#{idPrefix}FilterType, #{idPrefix}FilterValue"/>
		</div>
		<a4j:outputPanel ajaxRendered="true" id="#{idPrefix}TablePanel">
			<rich:dataTable id="#{idPrefix}Table" binding="#{taskListBeanName.taskListTable}"/>
			<table width="100%">
				<tbody>
					<tr>
						<td align="left">
							<rich:dataScroller align="left" for="#{idPrefix}Table" maxPages="20" id="#{idPrefix}TableDataScroller" />
						</td>
						<td align="right"> 
							<h:selectOneMenu value="#{taskListBeanName.displayAmount}" styleClass="fieldValue" >
								<f:selectItems value="#{applicationBean.availableRowDisplayItems}"/>
								<a4j:ajax event="change"/>
							</h:selectOneMenu>
							<h:outputText value=" items per page " />
						</td>
					</tr>
				</tbody>
	 		</table>
		</a4j:outputPanel>
</ui:composition>
</html>
