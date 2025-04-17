<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
	xmlns:h="http://xmlns.jcp.org/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	  xmlns:p="http://primefaces.org/ui">
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
				<p:ajax event="change" render="#{idPrefix}FilterValue"/>
			</h:selectOneMenu>	
			
			<!--<rich:spacer width="5" />	-->
			<h:outputText value=" Search for: " />
			
			<p:outputPanel autoUpdate="true" id="#{idPrefix}searchForPanel">
				<h:selectOneMenu id="#{idPrefix}FilterValue" value="#{taskListBeanName.taskListFilterValue}"
					styleClass="fieldValue"
					rendered="#{taskListBeanName.filterIsNotText}" >
					<f:selectItems value="#{taskListBeanName.filterValues}" /> 
				</h:selectOneMenu>
						
				<h:inputText id="#{idPrefix}FilterTextValue" value="#{taskListBeanName.taskListFilterTextValue}" styleClass="fieldValue"
					rendered="#{taskListBeanName.filterIsText}" />
			</p:outputPanel>
			
			<!--<rich:spacer width="10" />	-->
			<p:commandButton value="Go" action="#{taskListBeanName.doApplyFilter}" />
			<!--<rich:spacer width="5" /> -->
			<p:commandButton value="Clear" action="#{taskListBeanName.doClearFilterFields}"
				render="#{idPrefix}searchForPanel,#{idPrefix}FilterType,#{idPrefix}FilterValue"/>
		</div>
		<div style="text-align:right;">
			<p:commandLink value="Clear Filter" action="#{taskListBeanName.doClearFilter}"
				render="#{idPrefix}FilterType, #{idPrefix}FilterValue"/>
			<h:outputText value=" | " />
			<p:commandLink value="Refresh Tasklist" action="#{taskListBeanName.doRefreshTasklist}"
				render="#{idPrefix}FilterType, #{idPrefix}FilterValue"/>
		</div>
		<p:outputPanel autoUpdate="true" id="#{idPrefix}TablePanel">
			<p:dataTable id="#{idPrefix}Table"
						 binding="#{taskListBeanName.taskListTable}"
						 paginator="true"
						 selectionMode="single"
						 style="width:100%"
						 rowKey="#{row.id}"/>
			<table width="100%">
				<tbody>
					<tr>
						<td align="right"> 
							<h:selectOneMenu value="#{taskListBeanName.displayAmount}" styleClass="fieldValue" >
								<f:selectItems value="#{applicationBean.availableRowDisplayItems}"/>
								<p:ajax event="change"/>
							</h:selectOneMenu>
							<h:outputText value=" items per page " />
						</td>
					</tr>
				</tbody>
	 		</table>
		</p:outputPanel>
</ui:composition>
</html>
