javax.servlet.ServletException: /xhtml/tasklist/selectableTasksTemplateComponent.xhtml at line 58 and column 42 <rich:toolBar> Tag Library supports namespace: http://richfaces.org/rich, but no tag was defined for name: toolBar
at javax.faces.webapp.FacesServlet.service(FacesServlet.java:220)
at [internal classes]
at com.assurant.inc.sox.ar.servlets.RedirectServlet.doPost(RedirectServlet.java:73)
at com.assurant.inc.sox.ar.servlets.BaseServlet.doGet(BaseServlet.java:17)
at javax.servlet.http.HttpServlet.service(HttpServlet.java:686)
at [internal classes]

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
			<br />
			<br />
			<a4j:commandButton value="Go" action="#{taskListBeanName.doApplyFilter}" />
<!--			<rich:spacer width="5" /> -->
			<br />
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
			<rich:toolBar id="allTaskListToolBar">
				<rich:toolBarGroup>
					<a4j:commandLink id="allTaskListToolBarReassignLink" value="Reassign"
						action="#{allTaskListBean.doPrepareReassignReviewersPanel}" styleClass="undecoratedDistributeLink"/>
				</rich:toolBarGroup>
			</rich:toolBar>
			<rich:dataTable id="#{idPrefix}Table" binding="#{taskListBeanName.taskListTable}"/>
			<rich:toolBar id="bottomAllTaskListToolBar">
				<rich:toolBarGroup>
					<a4j:commandLink id="bottomAllTaskListToolBarReassignLink" value="Reassign"
						action="#{allTaskListBean.doPrepareReassignReviewersPanel}" 
						styleClass="undecoratedDistributeLink"
						oncomplete="window.scrollTo(0, 0);" />
				</rich:toolBarGroup>
			</rich:toolBar>
			<table width="100%">
				<tbody>
					<tr>
						<td align="left">
							<rich:datascroller align="left" for="#{idPrefix}Table" maxPages="20" id="#{idPrefix}TableDataScroller" 
								  scrollerListener="#{taskListBeanName.doScrollerListener}" />
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
