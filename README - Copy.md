```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://java.sun.com/jsf/facelets"
	xmlns:h="http://xmlns.jcp.org/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	xmlns:rich="http://richfaces.org/rich"
	xmlns:a4j="http://richfaces.org/a4j">

<!--	<ui:composition template="../common/referenceCoreTableTemplate.xhtml">-->
<ui:composition template="../common/referenceCoreTableTemplate.xhtml">
		<ui:define name="styles">
			<style type="text/css">
				select {
					font-size: 1.0em;
				}
			</style>
		</ui:define>
		<ui:define name="referenceCoreTable">

					<div style="float: left">
						<!--  Search -->
								<a4j:outputPanel ajaxreferendered="true" >
						<table width="80%">
							<tbody>
								<tr>
									<td align="left"> SOX Concern </td>
									<td align="left"> 
										<h:selectOneMenu  id="privSoxConcernList" 
										value="#{privilegeCommentSummaryBean.soxConcernFilter}" >
										<f:selectItems value="#{privilegeCommentSummaryBean.availableSOXConcerns }" />
										</h:selectOneMenu>
									</td>
									<td align="left"> Priv Description </td>
									<td align="left">
										<h:inputText id="privDescFilter" value="#{privilegeCommentSummaryBean.privDescriptionFilter}"
										 maxlength="25" onchange="this.value = this.value.toUpperCase();">
										</h:inputText> 
									</td>
									<td align="left"> Application Name </td>
									<td align="left"> 
										<h:selectOneMenu id="privApplicationNameText" 
										value="#{privilegeCommentSummaryBean.applicationNameFilter}">
										<f:selectItems value="#{privilegeCommentSummaryBean.availableApplicationNames}" />
										</h:selectOneMenu>
									</td>
														
								</tr>
								<tr>
									<td align="left"> Extract System ID </td>
									<td align="left">
										<h:selectOneMenu id="extractSystemFilter" 
										value="#{privilegeCommentSummaryBean.extractSystemIdFilter}">
										<f:selectItems value="#{privilegeCommentSummaryBean.availableExtractSystems}" />
										</h:selectOneMenu>
									</td>
									<td align="left"> Priv Value </td>
									<td align="left">
										<h:inputText id="privValueFilter" value="#{privilegeCommentSummaryBean.privValueFilter}" 
										 maxlength="25" onchange="this.value = this.value.toUpperCase();" >
										</h:inputText> 
									</td>
									<td align="left"> Function Duty </td>
									<td align="left"> 
										<h:selectOneMenu id="privFunctionDutyId" 
										value="#{privilegeCommentSummaryBean.functionDutyIdFilter}">
										<f:selectItems value="#{privilegeCommentSummaryBean.availableFunctionDuties}" />
										</h:selectOneMenu>
									</td>
								</tr>	
							</tbody>
						</table>
						<table width="800">
							<tbody>
								<tr>
									<td align="left"> 
								<h:commandButton id="goSearchButton" value="Go"
									action="#{privilegeCommentSummaryBean.goSearch}" />
								<h:commandButton id="resetButton" value="Reset"
									action="#{privilegeCommentSummaryBean.resetSearch}" />
									<!--  Select Filter -->
									<h:outputLabel id="selectFilterLabel" value="Filter: " />
									<!--  Select Filter ListBox -->
									<h:selectOneMenu id="filterListbox"
										value="#{privilegeCommentSummaryBean.activeFilter}">
										<f:selectItems
											value="#{privilegeCommentSummaryBean.availableFilters}" />
									</h:selectOneMenu>
									</td>
									<td align="right">
								<h:commandButton id="addButton" value="Add new Row"
									action="#{privilegeCommentSummaryBean.showAddPrivilegeCommentPanel}" />
								<h:commandButton id="deleteButton" value="Delete Row"
									action="#{privilegeCommentSummaryBean.showDeletePrivilegeCommentPanel}" 
									rendered="#{privilegeCommentSummaryBean.deleteEnabled}" />
								<h:commandButton id="updateButton" value="Update Row"
									action="#{privilegeCommentSummaryBean.showUpdatePrivilegeCommentPanel}" />
									</td> 
									</tr>
									</tbody>
									</table>
								</a4j:outputPanel>


					</div>


					<a4j:outputPanel ajaxRendered="true"
						style="width: 100% ;clear: both;">

						<!-- **************************     PrivilegeComment Display Table     **************************   -->
							<rich:dataTable id="privCommentTable" 
							value="#{privilegeCommentSummaryBean.privilegeCommentList}"
							var="element" rows="#{privilegeCommentSummaryBean.displayAmount}" 
							headerClass="defaultTableHeader" rowClasses="oddRow, evenRow" >
								<h:column id="deleteCheckBoxColumn"
								rendered="#{privilegeCommentSummaryBean.deleteEnabled}">
								<f:facet name="header">
									<a4j:commandLink 
									action="#{privilegeCommentSummaryBean.switchCheckBoxToggle}">Select All</a4j:commandLink>
								</f:facet>
								<h:selectBooleanCheckbox id="PrivCommentCheckBox"
								value="#{element.checked}"></h:selectBooleanCheckbox>
								</h:column>
								<h:column>
									<f:facet name="header">
										<a4j:commandLink value="Privilege Description" action="#{privilegeCommentSummaryBean.doSort}"
										styleClass="headerSortLink">
										<f:param name="column" value="description" />
									</a4j:commandLink>
									</f:facet>	
										#{element.description}  
								</h:column>
								<h:column>
									<f:facet name="header">
										<a4j:commandLink value="Privilege Value"
										action="#{privilegeCommentSummaryBean.doSort}"
										styleClass="headerSortLink">
										<f:param name="column" value="value"></f:param>
										</a4j:commandLink>
									</f:facet>
										#{element.value}
								</h:column>
								
								<h:column>
								<f:facet name="header">
								<a4j:commandLink value="FunctionDutyId" action="#{privilegeCommentSummaryBean.doSort}"
									styleClass="headerSortLink">
									<f:param name="column" value="functionDutyId" />
								</a4j:commandLink>
								</f:facet>	
										#{element.functionDutyId} 
								</h:column>
								<h:column>
								<f:facet name="header">
								<a4j:commandLink value="Privilege Comment" action="#{privilegeCommentSummaryBean.doSort}"
									styleClass="headerSortLink">
									<f:param name="column" value="comment" />
								</a4j:commandLink>
								</f:facet>	
										#{element.comment} 
								</h:column>
								<h:column>
								<f:facet name="header">
								<a4j:commandLink value="SoxConcern" action="#{privilegeCommentSummaryBean.doSort}"
									styleClass="headerSortLink">
									<f:param name="column" value="soxConcern" />
								</a4j:commandLink>
								</f:facet>	
										#{element.soxConcern} 
								</h:column>
								<h:column>
								<f:facet name="header">
								<a4j:commandLink value="Application Name" action="#{privilegeCommentSummaryBean.doSort}"
									styleClass="headerSortLink">
									<f:param name="column" value="applicationName" />
								</a4j:commandLink>
								</f:facet>	
										#{element.applicationName} 
								</h:column>
								<h:column>
								<f:facet name="header">
								<a4j:commandLink value="ExtractSystemId" action="#{privilegeCommentSummaryBean.doSort}"
									styleClass="headerSortLink">
									<f:param name="column" value="extractSystemId" />
								</a4j:commandLink>
								</f:facet>	
										#{element.extractSystemId} 
								</h:column>
							<h:column>
							<f:facet name="header">
								<a4j:commandLink value="ExtractDate" action="#{privilegeCommentSummaryBean.doSort}"
									styleClass="headerSortLink">
									<f:param name="column" value="extractDate" />
								</a4j:commandLink>
							</f:facet>	
							<h:outputText value="#{element.extractDate}">
								<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}" />
							</h:outputText>  
							</h:column>
							<h:column>
							<f:facet name="header">
								<a4j:commandLink value="Active From Date" action="#{privilegeCommentSummaryBean.doSort}"
									styleClass="headerSortLink">
									<f:param name="column" value="effectiveFromDate" />
								</a4j:commandLink>
							</f:facet>	
							<h:outputText value="#{element.effectiveFromDate}">
								<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}" />
							</h:outputText>  
							</h:column>
							<h:column>
							<f:facet name="header">
								<a4j:commandLink value="Active To Date" action="#{privilegeCommentSummaryBean.doSort}"
									styleClass="headerSortLink">
									<f:param name="column" value="effectiveToDate" />
								</a4j:commandLink>
							</f:facet>	
							<h:outputText value="#{element.effectiveToDate}">
								<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}" />
							</h:outputText>  
							</h:column>
							<h:column>
							<f:facet name="header">
								<a4j:commandLink value="Last Changed By" action="#{privilegeCommentSummaryBean.doSort}"
									styleClass="headerSortLink">
									<f:param name="column" value="lastChangedBy" />
								</a4j:commandLink>
							</f:facet>	
										#{element.lastChangedBy} 
						</h:column>
						
						<h:column>
							<f:facet name="header">
								<a4j:commandLink value="Last Changed Date" action="#{privilegeCommentSummaryBean.doSort}"
									styleClass="headerSortLink">
									<f:param name="column" value="lastChangedDate" />
								</a4j:commandLink>
							</f:facet>	
							<h:outputText value="#{element.lastChangedDate}">
								<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}" />
							</h:outputText>  
						</h:column>
						<h:column>
							<f:facet name="header">
								<a4j:commandLink value="Created By" action="#{privilegeCommentSummaryBean.doSort}"
									styleClass="headerSortLink">
									<f:param name="column" value="createdBy" />
								</a4j:commandLink>
							</f:facet>	
										#{element.createdBy} 
						</h:column>
						<h:column>
							<f:facet name="header">
								<a4j:commandLink value="Created Date" action="#{privilegeCommentSummaryBean.doSort}"
									styleClass="headerSortLink">
									<f:param name="column" value="createdDate" />
								</a4j:commandLink>
							</f:facet>	
							<h:outputText value="#{element.createdDate}">
								<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}" />
							</h:outputText>  
						</h:column> 
		
			    </rich:dataTable>

						<!-- **************************     Scroll  Bars     ***********************************   -->
						<table width="100%">
							<tbody>
								<tr>
									<td align="left">
										<rich:dataScroller id="userScroller" align="left"
											binding="#{privilegeCommentSummaryBean.dataScroller}"
											for="privCommentTable"
											scrollerListener="#{privilegeCommentSummaryBean.doScrollerListener}" 
											onbeforedomupdate="window.scrollTo(0, 0);" />
									</td>

									<td align="right">
										<h:selectOneMenu id="userDisplayRows"
											value="#{privilegeCommentSummaryBean.displayAmount}"
											styleClass="fieldValue">
											<f:selectItems
												value="#{applicationBean.availableRowDisplayItems}" />
											<a4j:ajax event="change"
												action="#{privilegeCommentSummaryBean.doDisplayRowListener}" 
												onbeforedomupdate="window.scrollTo(0, 0);" />
										</h:selectOneMenu>
										<h:outputText value=" items per page " />
									</td>
								</tr>
							</tbody>
						</table>
						
					</a4j:outputPanel>


		</ui:define>

				<ui:define name="modalPanels">

				<!--  ADD   PopUp Modal Panel                    -->
				<a4j:outputPanel ajaxRendered="true" id="addprivilegeCommentModalAjaxPanel">
					<rich:popupPanel id="addModalPanel" resizeable="true"
					moveable="true" height="400" width="550"
					rendered="#{privilegeCommentSummaryBean.renderAddPrivilegeCommentModalPanel}"
					showWhenRendered="true">
						<f:facet name="header">
							<h:outputText value="Add New Row"></h:outputText>
						</f:facet>
				<!--   Messages                    -->
				<div>
					<!--<h:messages id="msgId"
					fatalClass="fatalMessage"
					errorClass="errorMessage"
					warnClass="warnMessage"
					infoClass="infoMessage"
					layout="table"></h:messages>-->
					<rich:messages id="msgId"
					fatalClass="fatalMessage"
					errorClass="errorMessage"
					warnClass="warnMessage"
					infoClass="infoMessage"
					layout="table"></rich:messages>
				</div>
	
				<h:form id="addprivilegeCommentForm">
					<table width="100%">
						<tbody>
							<tr>
								<td align="left">
									<h:outputLabel value="Priv Description" styleClass="fieldLabel" />
								</td>
								<td align="left">
									<h:inputText id="privDescText" maxlength="50"
										value="#{privilegeCommentSummaryBean.privDescription}" 
										onchange="this.value = this.value.toUpperCase();" />
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Priv Value" styleClass="fieldLabel" />
								</td>
								<td align="left">
									<h:inputText id="privValueText" maxlength="50"
										value="#{privilegeCommentSummaryBean.privValue}" 
										onchange="this.value = this.value.toUpperCase();" />
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Function Duty" styleClass="fieldLabel" />
								</td>
								<td align="left">
									<h:selectOneMenu id="functionDuty" 
										value="#{privilegeCommentSummaryBean.functionDutyId}">
										<f:selectItems value="#{privilegeCommentSummaryBean.availableFunctionDuties}" />
									</h:selectOneMenu>
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Priv Comment" styleClass="fieldLabel" />
								</td>
								<td align="left">
									<h:inputText id="privCommentText" maxlength="50"
										value="#{privilegeCommentSummaryBean.comment}" 
										onchange="this.value = this.value.toUpperCase();" />
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="SOX Concern" styleClass="fieldLabel" />
								</td>
								<td align="left">
									<h:selectOneMenu id="soxconcern" 
										value="#{privilegeCommentSummaryBean.soxConcern}">
										<f:selectItems value="#{privilegeCommentSummaryBean.availableSOXConcerns }" />
									</h:selectOneMenu>
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Application Name" styleClass="fieldLabel" />
								</td>
								<td align="left">
									<h:selectOneMenu id="applicationName" 
											value="#{privilegeCommentSummaryBean.applicationName}">
										<f:selectItems value="#{privilegeCommentSummaryBean.availableApplicationNames}" />	
									</h:selectOneMenu>
								</td>
							</tr>
							<tr>
								<td align="left" colspan="2">
									<a4j:commandButton id="save" value="Save" 
										action="#{privilegeCommentSummaryBean.doAddPrivilegeComment}">
									</a4j:commandButton>
									<a4j:commandButton id="Cancel" value="Cancel"
										action="#{privilegeCommentSummaryBean.doCancelAddPrivilegeCommentPanel}">
									</a4j:commandButton>
								</td>
							</tr>
						</tbody>
					</table>
				</h:form>
			
				</rich:popupPanel>
			</a4j:outputPanel>

		<!--  Verify Delete Modal Panel                    -->
			<a4j:outputPanel ajaxRendered="true" id="deleteprivilegeCommentModalAjaxPanel">
				<rich:popupPanel id="deleteConfirmModalPanel" resizeable="true"
					moveable="true" height="400" width="650"
					rendered="#{privilegeCommentSummaryBean.renderDeletePrivilegeCommentModalPanel}"
					showWhenRendered="true">
				<div>
					<!--<h:messages id="delmsgId"
					fatalClass="fatalMessage"
					errorClass="errorMessage"
					warnClass="warnMessage"
					infoClass="infoMessage"
					layout="table"></h:messages>-->
					<rich:messages id="delmsgId"
					fatalClass="fatalMessage"
					errorClass="errorMessage"
					warnClass="warnMessage"
					infoClass="infoMessage"
					layout="table"></rich:messages>
				</div>

					<f:facet name="header">
						<h:outputLabel value="Delete Row(s)" />
					</f:facet>

					<h:form id="deleteConfirmForm">
						<div class="verticalSpacer" />
						<div>
							<div class="sectionHeaderSmall">
								Delete Privilege Comment(s)?
							</div>
						</div>
						<rich:dataTable id="selectDeletedRowsTable"
							value="#{privilegeCommentSummaryBean.deletedPrivilegeCommentList}"
							var="privilegeComment" >
							<h:column>
							<f:facet name="header">
								<h:outputLabel id="deletePrivCommment" value="PrivilegeComment Description"></h:outputLabel>
							</f:facet>
							#{privilegeComment.description}
							</h:column>
							<h:column>
							<f:facet name="header">
								<h:outputLabel id="deletePrivValue" value="PrivilegeComment Value"></h:outputLabel>
							</f:facet>
							#{privilegeComment.value}
							</h:column>
						</rich:dataTable>

						<div>
							<a4j:commandButton id="deleteButton" value="Delete"
								action="#{privilegeCommentSummaryBean.doDelete}" />

							<a4j:commandButton id="cancelDeleteButton" value="Cancel"
								action="#{privilegeCommentSummaryBean.doCancelDelete}" />
						</div>
					</h:form>
				</rich:popupPanel>
			</a4j:outputPanel>

		<!--  Verify Update Modal Panel                    -->
		<a4j:outputPanel ajaxRendered="true" id="updateprivilegeCommentModalAjaxPanel">
			<rich:popupPanel id="updateConfirmModalPanel" resizeable="true"
				moveable="true" height="400" width="600"
				rendered="#{privilegeCommentSummaryBean.renderUpdatePrivilegeCommentModalPanel}"
				showWhenRendered="true" >

				<f:facet name="header">
					<h:outputLabel value="Update Row" />
				</f:facet>
				<div>
					<!--<h:messages id="updmsgId"
					fatalClass="fatalMessage"
					errorClass="errorMessage"
					warnClass="warnMessage"
					infoClass="infoMessage"
					layout="table"></h:messages>-->
					<rich:messages id="updmsgId"
					fatalClass="fatalMessage"
					errorClass="errorMessage"
					warnClass="warnMessage"
					infoClass="infoMessage"
					layout="table"></rich:messages>
				</div>

				<h:form id="updateConfirmForm">
					<div class="verticalSpacer" />
					<div>
						<div class="sectionHeaderSmall">
							Update Privilege Comment
						</div>
					</div>
		<!--  display in editable mode Panel                    -->

				<table width="100%">
					<tbody>
						<tr>
							<td align="left">
								<h:outputLabel value="Priv Description" styleClass="fieldLabel" />
							</td>
							<td align="left">
								<h:outputLabel value="#{privilegeCommentSummaryBean.privDescription}" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Priv Value" styleClass="fieldLabel" />
							</td>
							<td align="left">
								<h:outputLabel value="#{privilegeCommentSummaryBean.privValue}" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Function Duty" styleClass="fieldLabel" />
							</td>
							<td align="left">
								<h:selectOneMenu id="updateFunctionDuty" 
									value="#{privilegeCommentSummaryBean.functionDutyId}">
									<f:selectItems value="#{privilegeCommentSummaryBean.availableFunctionDuties}" />
								</h:selectOneMenu>
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Priv Comment" styleClass="fieldLabel" />
							</td>
							<td align="left">
								<h:inputText id="updatePrivCommentText" maxlength="50"
									value="#{privilegeCommentSummaryBean.comment}" 
									onchange="this.value = this.value.toUpperCase();" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="SOX Concern" styleClass="fieldLabel" />
							</td>
							<td align="left">
								<h:selectOneMenu id="updateSoxconcern" 
									value="#{privilegeCommentSummaryBean.soxConcern}">
									<f:selectItems value="#{privilegeCommentSummaryBean.availableSOXConcerns }" />
								</h:selectOneMenu>
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Application Name" styleClass="fieldLabel" />
							</td>
							<td align="left">
								<h:selectOneMenu id="updateApplicationName" 
										value="#{privilegeCommentSummaryBean.applicationName}">
									<f:selectItems value="#{privilegeCommentSummaryBean.availableApplicationNames}" />	
								</h:selectOneMenu>
							</td>
						</tr>
						<tr>
							<td align="left" colspan="2">
								<a4j:commandButton id="updateButton" value="Update"
									action="#{privilegeCommentSummaryBean.doUpdate}" />
		
								<a4j:commandButton id="cancelupdateButton" value="Cancel"
									action="#{privilegeCommentSummaryBean.doCancelUpdate}" />
							</td>
						</tr>
					</tbody>
				</table>
			</h:form>
		
			</rich:popupPanel>
		</a4j:outputPanel>


			
<!--  Bulk update functionality -->
			<!--  Verify BulkUpdate Modal Panel                    -->
			<a4j:outputPanel ajaxRendered="true" id="bulkUpdateprivilegeCommentModalAjaxPanel">
				<rich:popupPanel id="bulkUpdateConfirmModalPanel" resizeable="true"
					moveable="true" height="500" width="800"
					rendered="#{privilegeCommentSummaryBean.renderBulkUpdatePanel}"
					showWhenRendered="true" >
	
					<f:facet name="header">
						<h:outputLabel value="Bulk Update Privilege Comment" />
					</f:facet>
				<div>
					<!--<h:messages id="blkmsgId"
					fatalClass="fatalMessage"
					errorClass="errorMessage"
					warnClass="warnMessage"
					infoClass="infoMessage"
					layout="table"></h:messages>-->
					<rich:messages id="blkmsgId"
					fatalClass="fatalMessage"
					errorClass="errorMessage"
					warnClass="warnMessage"
					infoClass="infoMessage"
					layout="table"></rich:messages>
				</div>
	
					<h:form id="bulkUpdateConfirmForm">
	
					<div class="verticalSpacer"></div>
	
						<!--  update fields -->
					<table width="80%">
						<tbody>
							<tr>
								<td align="left"> Application Name </td>
								<td align="left"> 
									<h:selectOneMenu id="bulkAppName" 
									value="#{privilegeCommentSummaryBean.bulkAppName}">
									<f:selectItems value="#{privilegeCommentSummaryBean.availableApplicationNames}" />
									</h:selectOneMenu>
								</td>
								<td align="left"> SOX Concern </td>
								<td align="left"> 
									<h:selectOneMenu  id="bulkSoxConcern" 
									value="#{privilegeCommentSummaryBean.bulkSoxConcern}">
									<f:selectItems value="#{privilegeCommentSummaryBean.availableSOXConcerns }" />
									</h:selectOneMenu>
								</td>
													
							</tr>
							<tr>
								<td align="left"> Function Duty </td>
								<td align="left"> 
									<h:selectOneMenu id="bulkFunctionDutyId" 
									value="#{privilegeCommentSummaryBean.bulkFunctionDutyId}">
									<f:selectItems value="#{privilegeCommentSummaryBean.availableFunctionDuties}" />
									</h:selectOneMenu>
								</td>
								<td align="left"> Priv Comment </td>
								<td align="left">
									<h:inputText id="bulkPrivCommentField" maxlength="50"
										value="#{privilegeCommentSummaryBean.bulkComment}" 
										onchange="this.value = this.value.toUpperCase();" />
								</td>
							</tr>	
							<tr>
								<td align="left" colspan="4">
									<rich:dataTable id="bulkprivCommentTable" 
										value="#{privilegeCommentSummaryBean.bulkUpdateList}"
										var="priv" rows="10" >
										<h:column id="updateCheckBoxColumn"
											rendered="#{privilegeCommentSummaryBean.updateCheckbox}">
											<f:facet name="header">
											Select
											</f:facet>
											<h:selectBooleanCheckbox id="PrivCommentCheckBox"
											value="#{priv.checked}"></h:selectBooleanCheckbox>
										</h:column>
										<h:column>
												<f:facet name="header">Privilege Description </f:facet>	
													#{priv.description}  
										</h:column>
										<h:column>
												<f:facet name="header">Privilege Value </f:facet>
													#{priv.value}
										</h:column>
										
										<h:column>
											<f:facet name="header">FunctionDutyId </f:facet>	
													#{priv.functionDutyId} 
										</h:column>
										<h:column>
											<f:facet name="header">Privilege Comment </f:facet>	
													#{priv.comment} 
										</h:column>
										<h:column>
											<f:facet name="header">SoxConcern </f:facet>	
													#{priv.soxConcern} 
										</h:column>
										<h:column>
											<f:facet name="header"> Application Name </f:facet>	
													#{priv.applicationName} 
										</h:column>
									</rich:dataTable>
								</td>
							</tr>	
							<tr>
								<td align="left" colspan="4">
									<a4j:commandButton id="bulkUpdateButton" value="Update"
										reRender = "bulkUpdateConfirmModalPanel" 
										action="#{privilegeCommentSummaryBean.doBulkUpdate}" />
			
									<a4j:commandButton id="cancelBulkUpdateButton" value="Cancel"
										action="#{privilegeCommentSummaryBean.doCancelBulkUpdate}" />
								</td>
							</tr>	
						</tbody>
					</table>
					</h:form>
				</rich:popupPanel>
			</a4j:outputPanel>				


		</ui:define>
	
	</ui:composition>
</html>


