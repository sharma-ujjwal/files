```
<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://java.sun.com/jsf/facelets"
	xmlns:h="http://xmlns.jcp.org/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	xmlns:p="http://primefaces.org/ui">
<ui:composition template="../common/adminConsoleTemplate.xhtml">
	<ui:define name="styles">
		<style type="text/css">
			select {
				font-size: 0.8em;
			}
		</style>
	</ui:define>
	<ui:define name="script">
		<script type="text/javascript">
				/* <![CDATA[ */
				function setDepartmentValue(value) {
					var selector = document.getElementById('editExceptionForm:userExceptionsTable:0:missingDepartmentNameCostCenterSelectOne');
					setSelectorValue(selector, value);
				}

				function setDivisionValue(value) {
					var selector = document.getElementById('editExceptionForm:userExceptionsTable:0:missingDivisionSelectOne');
					setSelectorValue(selector, value);
				}

				function setUserStatusValue(value) {
					var selector = document.getElementById('editExceptionForm:userExceptionsTable:0:missingUserStatusSelectOne');
					setSelectorValue(selector, value);
				}

				function setUserTypeValue(value) {
					var selector = document.getElementById('editExceptionForm:userExceptionsTable:0:missingUserTypeSelectOne');
					setSelectorValue(selector, value);
				}

				function setSelectorValue(selector, value) {
					if (selector == null) {
						return;
					}

					value = value.replace('&amp;', '&');

					for (i = 0; i < selector.options.length; i++) {
						if (selector.options[i].text == value) {
							selector.selectedIndex = i;
							break;
						}
					}
				}

				/* ]]> */
			</script>
	</ui:define>
	<ui:define name="exceptionReportingTab">
			<div style="margin:0.1rem;">
				<h:outputLabel id="selectFilterLabel6" styleClass="filterCls" value="Filter: " />
	
				<!--  Select Filter ListBox -->
				<h:selectOneMenu id="filterListbox9" styleClass="exceptionFilterDropCls"
					value="#{exceptionSummaryBean.activeFilter}">
					<p:ajax event="change"
							listener="#{exceptionSummaryBean.switchFilter}"
							update="bodyForm:tabPanel:exceptionTable"
							oncomplete="paginatorCall();"/>
					<f:selectItems value="#{exceptionSummaryBean.availableFilters}" />
				</h:selectOneMenu>
			</div>
			<div class="verticalSpacer"/>
			<p:outputPanel autoUpdate="true" id="updateLayout">
				<h:messages id="tableMessages"
							fatalClass="fatalMessage"
							errorClass="errorMessage" warnClass="warningMessage"
							infoClass="infoMessage" layout="table"/>

				<p:dataTable id="exceptionTable"
					value="#{exceptionSummaryBean.exceptionList}" var="exception"
					rows="#{exceptionSummaryBean.displayAmount}"
					paginator="true"
				    tableStyle="width: auto;"
					paginatorPosition="bottom"
					paginatorTemplate="#{not empty exceptionSummaryBean.exceptionList ? '{CurrentPageReport} {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink} {RowsPerPageDropdown}' : ''}"
					currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
					rowsPerPageTemplate="10,25,50,100"
					rowIndexVar="rowIndex"
					rowKeyVar="rowKey"
					styleClass="defaultTableHeader exceptionSummaryCls" style="overflow-x:hidden">

					<p:ajax event="page" listener="#{exceptionSummaryBean.clearMessages}" update="tableMessages"/>

					<p:column id="keyIdColumn">
						<f:facet name="header">
							<p:commandLink value="Key ID"
								action="#{exceptionSummaryBean.doSort}"
								styleClass="headerSortLink"
								update="exceptionTable"
								process="@this"
								oncomplete="paginatorCall();">
								<f:param name="column" value="keyId" />
							</p:commandLink>
						</f:facet>
						#{exception.keyId}
					</p:column>
					<p:column id="nameColumn">
						<f:facet name="header">
							<p:commandLink value="Name"
								action="#{exceptionSummaryBean.doSort}"
								styleClass="headerSortLink"
								update="exceptionTable"
								process="@this"
								oncomplete="paginatorCall();">
								<f:param name="column" value="formattedFullName" />
							</p:commandLink>
						</f:facet>
						#{exception.formattedFullName}
					</p:column>
					<p:column id="divisionColumn">
						<f:facet name="header">
							<p:commandLink value="Division"
								action="#{exceptionSummaryBean.doSort}"
								styleClass="headerSortLink"
							    update="exceptionTable"
							    process="@this"
								oncomplete="paginatorCall();">
								<f:param name="column" value="divisionName" />
							</p:commandLink>
						</f:facet>
						<p:outputPanel id="divisionDisplayPanel" autoUpdate="true">
							<p:commandLink id="missingDivisionLink"
										   action="#{exceptionSummaryBean.showExceptionEditPanel}"
										   oncomplete="PF('editExceptionA4jPanelVar').show();"
										   update="editException"
										   process="@this"
										   rendered="#{exception.missingDivision}"
										   value="edit">
								<f:param name="editId" value="#{exception.userId}" />
							</p:commandLink>
							<h:outputText rendered="#{!exception.missingDivision}"
								value="#{exception.divisionName}" />
						</p:outputPanel>
					</p:column>
					<p:column id="departmentColumn">
						<f:facet name="header">
							<p:commandLink value="Department Name/Cost Center"
								action="#{exceptionSummaryBean.doSort}"
								styleClass="headerSortLink"
							    update="exceptionTable"
							    process="@this"
								oncomplete="paginatorCall();">
								<f:param name="column" value="departmentNameCostCenter" />
							</p:commandLink>
						</f:facet>
						<p:outputPanel id="departmentDisplayPanel" autoUpdate="true">
							<p:commandLink id="missingDepartmentLink"
										   action="#{exceptionSummaryBean.showExceptionEditPanel}"
										   oncomplete="PF('editExceptionA4jPanelVar').show();"
										   update="editException"
										   process="@this"
										   rendered="#{exception.missingDepartment}"
										   value="edit">
								<f:param name="editId" value="#{exception.userId}" />
							</p:commandLink>
							<h:outputText rendered="#{!exception.missingDepartment}"
								value="#{exception.departmentNameCostCenter}" />
						</p:outputPanel>
					</p:column>
					<p:column id="supervisorColumn">
						<f:facet name="header">
							<p:commandLink value="Supervisor"
								action="#{exceptionSummaryBean.doSort}"
								styleClass="headerSortLink"
							    update="exceptionTable"
							    process="@this"
								oncomplete="paginatorCall();">
								<f:param name="column" value="supervisorName" />
							</p:commandLink>
						</f:facet>
						<p:outputPanel id="supervisorDisplayPanel" autoUpdate="true">
							<p:commandLink id="missingSupervisor"
								action="#{exceptionSummaryBean.showExceptionEditPanel}"
								oncomplete="PF('editExceptionA4jPanelVar').show();"
								update="editException"
								process="@this"
								rendered="#{exception.missingSupervisor}" value="edit">
								<f:param name="editId" value="#{exception.userId}" />
							</p:commandLink>
							<h:outputText rendered="#{!exception.missingSupervisor}"
								value="#{exception.supervisorName}" />
						</p:outputPanel>
					</p:column>
					<p:column id="jobTitleColumn">
						<f:facet name="header">
							<p:commandLink value="Job Title"
								action="#{exceptionSummaryBean.doSort}"
								styleClass="headerSortLink"
							    update="exceptionTable"
							    process="@this"
								oncomplete="paginatorCall();">
								<f:param name="column" value="jobTitle" />
							</p:commandLink>
						</f:facet>
						#{exception.jobTitle}
					</p:column>
					<p:column id="userTypeColumn">
						<f:facet name="header">
							<p:commandLink value="User Type"
								action="#{exceptionSummaryBean.doSort}"
								styleClass="headerSortLink"
							    update="exceptionTable"
							    process="@this"
								oncomplete="paginatorCall();">
								<f:param name="column" value="userTypeName" />
							</p:commandLink>
						</f:facet>
						<p:outputPanel id="userTypeDisplayPanel" autoUpdate="true">
							<p:commandLink id="missingUserType"
								action="#{exceptionSummaryBean.showExceptionEditPanel}"
								rendered="#{exception.missingUserType}" value="edit">
								<f:param name="editId" value="#{exception.userId}" />
							</p:commandLink>
							<h:outputText rendered="#{!exception.missingUserType}"
								value="#{exception.userTypeName}" />
						</p:outputPanel>
					</p:column>
					<p:column id="userStatusColumn">
						<f:facet name="header">
							<p:commandLink value="User Status"
								action="#{exceptionSummaryBean.doSort}"
								styleClass="headerSortLink"
							    update="exceptionTable"
							    process="@this"
								oncomplete="paginatorCall();">
								<f:param name="column" value="userStatusName" />
							</p:commandLink>
						</f:facet>
						<p:outputPanel id="userStatusDisplayPanel" autoUpdate="true">
							<p:commandLink id="missingUserStatus"
								action="#{exceptionSummaryBean.showExceptionEditPanel}"
								rendered="#{exception.missingUserStatus}" value="edit">
								<f:param name="editId" value="#{exception.userId}" />
							</p:commandLink>
							<h:outputText rendered="#{!exception.missingUserStatus}"
								value="#{exception.userStatusName}" />
						</p:outputPanel>
					</p:column>
					<p:column id="emailColumn">
						<f:facet name="header">
							<p:commandLink value="Email"
								action="#{exceptionSummaryBean.doSort}"
								styleClass="headerSortLink"
							    update="exceptionTable"
							    process="@this"
								oncomplete="paginatorCall();">
								<f:param name="column" value="email"/>
							</p:commandLink>
						</f:facet>
						<p:commandLink id="missingEmail"
									   action="#{exceptionSummaryBean.showExceptionEditPanel}"
									   oncomplete="PF('editExceptionA4jPanelVar').show();"
									   update="editException"
									   process="@this"
									   rendered="#{exception.missingEmail}"
									   value="edit">
							<f:param name="editId" value="#{exception.userId}" />
						</p:commandLink>
						<h:outputText rendered="#{!exception.missingEmail}"
							value="#{exception.email}" />
					</p:column>
					<p:column id="locationColumn">
						<f:facet name="header">
							<p:commandLink value="Location"
								action="#{exceptionSummaryBean.doSort}"
								styleClass="headerSortLink"
							    update="exceptionTable"
							    process="@this"
								oncomplete="paginatorCall();">
								<f:param name="column" value="location" />
							</p:commandLink>
						</f:facet>
						#{exception.location}
					</p:column>
					<p:column id="phoneColumn">
						<f:facet name="header">
							<p:commandLink value="Phone"
								action="#{exceptionSummaryBean.doSort}"
								styleClass="headerSortLink"
							    update="exceptionTable"
							    process="@this"
								oncomplete="paginatorCall();">
								<f:param name="column" value="phone" />
							</p:commandLink>
						</f:facet>
						#{exception.phone}
					</p:column>
				</p:dataTable>
			</p:outputPanel>
		</ui:define>
		<ui:define name="modalPanels">
			<p:outputPanel autoUpdate="true" id="editExceptionA4jPanel">
				<p:dialog id="editException" resizeable="true"
					draggable="true" width="1000" height="600"
					widgetVar="editExceptionA4jPanelVar"
					modal="true" closable="false" showHeader="true"
					appendTo="@body"
					style="display: none;"
					styleClass="editExcCls">

					<f:facet name="header">
						<h:outputLabel value="Edit Exception" />
					</f:facet>

					<h:form id="editExceptionForm">
						<p:dataTable id="userExceptionsTable"
							value="#{exceptionSummaryBean.exceptionList}"
							var="exception"
							rows="1" tableStyle="width: auto;" style="overflow-x:hidden"
							styleClass="defaultTableHeader excepTableCls">
		
							<p:column id="nameColumn">
								<f:facet name="header">Name</f:facet>	
								#{exceptionSummaryBean.selectedException.formattedFullName}  
							</p:column>
							<p:column id="divisionColumn">
								<f:facet name="header">Division</f:facet>
								<h:selectOneMenu id="missingDivisionSelectOne"
									 rendered="#{exceptionSummaryBean.selectedException.missingDivision}"
									 value="#{exceptionSummaryBean.selectedDivisionId}">
									<f:selectItems value="#{exceptionSummaryBean.divisionSelectItems}" />
								</h:selectOneMenu>
								<h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingDivision}"
									value="#{exceptionSummaryBean.selectedException.divisionName}" />  
							</p:column>
							<p:column id="departmentColumn">
								<f:facet name="header">Department Name/Cost Center</f:facet>
								<h:selectOneMenu id="missingDepartmentNameCostCenterSelectOne"
									 rendered="#{exceptionSummaryBean.selectedException.missingDepartment}"
									 value="#{exceptionSummaryBean.selectedDepartmentId}">
									<f:selectItems value="#{exceptionSummaryBean.departmentNameCostCenterSelectItems}" />
								</h:selectOneMenu>
								<h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingDepartment}"
									value="#{exceptionSummaryBean.selectedException.departmentNameCostCenter}" />  
							</p:column>
							<p:column id="userTypeColumn">
								<f:facet name="header">User Type</f:facet>	
								<h:selectOneMenu id="missingUserTypeSelectOne"
									 rendered="#{exceptionSummaryBean.selectedException.missingUserType}"
									 value="#{exceptionSummaryBean.selectedUserTypeId}">
									<f:selectItems value="#{exceptionSummaryBean.userTypeSelectItems}" />
								</h:selectOneMenu>
								<h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingUserType}"
									value="#{exceptionSummaryBean.selectedException.userTypeName}" />  
							</p:column>
							<p:column id="userStatusColumn">
								<f:facet name="header">User Status</f:facet>	
								<h:selectOneMenu id="missingUserStatusSelectOne"
									 rendered="#{exceptionSummaryBean.selectedException.missingUserStatus}"
									 value="#{exceptionSummaryBean.selectedUserStatusId}">
									<f:selectItems value="#{exceptionSummaryBean.userStatusSelectItems}" />
								</h:selectOneMenu>
								<h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingUserStatus}"
									value="#{exceptionSummaryBean.selectedException.userStatusName}" />  
							</p:column>
							<p:column id="emailColumn">
								<f:facet name="header">Email</f:facet>	
								<h:inputText id="exceptionEmailInputText" styleClass="editEmailCls" value="#{exceptionSummaryBean.selectedException.email}" rendered="#{exceptionSummaryBean.selectedException.missingEmail}" onchange="this.value = this.value.toUpperCase();"/>
								<h:outputText id="exceptionEmailOutputText" value="#{exceptionSummaryBean.selectedException.email}" rendered="#{!exceptionSummaryBean.selectedException.missingEmail}" />  
							</p:column>
							<p:column id="jobTitleColumn">
								<f:facet name="header">Job Title</f:facet>	
								#{exceptionSummaryBean.selectedException.jobTitle}  
							</p:column>
							<p:column id="locationColumn">
								<f:facet name="header">Location</f:facet>	
								#{exceptionSummaryBean.selectedException.location}  
							</p:column>
						</p:dataTable>
						<div class="verticalSpacer" />
						<p:outputPanel id="supervisorPanelOp" autoUpdate="false">
							<div style="width: 100%;" class="supervisorCls">
								Supervisor:
								<h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingSupervisor}"
										value="#{exceptionSummaryBean.selectedException.supervisorName}" />
								<p:commandLink rendered="#{exceptionSummaryBean.selectedException.missingSupervisor or exceptionSummaryBean.supervisorChanged}"
											   action="#{exceptionSummaryBean.doShowSearchSupervisorPanel}"
											   value="Select supervisor"
											   oncomplete="PF('searchSupervisorDialog').show()"
											   process="@this"
											   update="searchUserForm"
											   onclick="paginatorCall();"/>
							</div>
						</p:outputPanel>
						<div class="verticalSpacer" />
						<p:outputPanel autoUpdate="true">
							<p:dataTable id="directReportsTable"
								value="#{exceptionSummaryBean.directReportsList}" 
								var="directReport"
								rows="#{exceptionSummaryBean.displayAmount}"
								rowIndexVar="rowIndex" rowKeyVar="rowKey" tableStyle="width: auto;" style="overflow-x:hidden"
								styleClass="defaultTableHeader directRepCls">
			
								<p:column id="directReportKeyIdColumn">
									<f:facet name="header">
										<p:commandLink value="Key ID"
											action="#{exceptionSummaryBean.doSortDirectReports}"
											styleClass="headerSortLink"
										    update="directReportsTable"
										    process="@this">
											<f:param name="directReportColumn" value="keyId" />
										</p:commandLink>
									</f:facet>	
									#{directReport.keyId}  
								</p:column>
								<p:column id="directReportNameColumn">
									<f:facet name="header">
										<p:commandLink value="Name"
											action="#{exceptionSummaryBean.doSortDirectReports}"
											styleClass="headerSortLink"
										    update="directReportsTable"
										    process="@this">
											<f:param name="directReportColumn" value="formattedFullName" />
										</p:commandLink>
									</f:facet>	
									#{directReport.formattedFullName}  
								</p:column>
								<p:column id="directReportDivisionColumn">
									<f:facet name="header">
										<p:commandLink value="Division"
											action="#{exceptionSummaryBean.doSortDirectReports}"
											styleClass="headerSortLink"
										    update="directReportsTable"
										    process="@this">
											<f:param name="directReportColumn" value="divisionName" />
										</p:commandLink>
									</f:facet>
									<h:outputText rendered="#{!directReport.missingDivision and !exceptionSummaryBean.selectedException.missingDivision}">
										#{directReport.divisionName}
									</h:outputText>
									<h:outputText rendered="#{!directReport.missingDivision and exceptionSummaryBean.selectedException.missingDivision}">
										<a href="#" onmouseup="setDivisionValue(this.innerHTML);">#{directReport.divisionName}</a>
									</h:outputText>
								</p:column>
								<p:column id="directReportDepartmentColumn">
									<f:facet name="header">
										<p:commandLink value="Department"
											action="#{exceptionSummaryBean.doSortDirectReports}"
											styleClass="headerSortLink"
										    update="directReportsTable"
										    process="@this">
											<f:param name="directReportColumn" value="departmentNameCostCenter" />
										</p:commandLink>
									</f:facet>
									<h:outputText rendered="#{!directReport.missingDepartment and !exceptionSummaryBean.selectedException.missingDepartment}">
										#{directReport.departmentNameCostCenter}
									</h:outputText>
									<h:outputText rendered="#{!directReport.missingDepartment and exceptionSummaryBean.selectedException.missingDepartment}">
										<a href="#" onmouseup="setDepartmentValue(this.innerHTML);">#{directReport.departmentNameCostCenter}</a>
									</h:outputText>
								</p:column>
								<p:column id="directReportUserTypeColumn">
									<f:facet name="header">
										<p:commandLink value="User Type"
											action="#{exceptionSummaryBean.doSortDirectReports}"
											styleClass="headerSortLink"
										    update="directReportsTable"
										    process="@this">
											<f:param name="directReportColumn" value="userTypeName" />
										</p:commandLink>
									</f:facet>	
									<h:outputText rendered="#{!directReport.missingUserType and !exceptionSummaryBean.selectedException.missingUserType}">
										#{directReport.userTypeName}
									</h:outputText>
									<h:outputText rendered="#{!directReport.missingUserType and exceptionSummaryBean.selectedException.missingUserType}">
										<a href="#" onmouseup="setUserTypeValue(this.innerHTML);">#{directReport.userTypeName}</a>
									</h:outputText>
								</p:column>
								<p:column id="directReportUserStatusColumn">
									<f:facet name="header">
										<p:commandLink value="User Status"
											action="#{exceptionSummaryBean.doSortDirectReports}"
											styleClass="headerSortLink"
										    update="directReportsTable"
										    process="@this">
											<f:param name="directReportColumn" value="userStatusName" />
										</p:commandLink>
									</f:facet>	
									<h:outputText rendered="#{!directReport.missingUserStatus and !exceptionSummaryBean.selectedException.missingUserStatus}">
										#{directReport.userStatusName}
									</h:outputText>
									<h:outputText rendered="#{!directReport.missingUserStatus and exceptionSummaryBean.selectedException.missingUserStatus}">
										<a href="#" onmouseup="setUserStatusValue(this.innerHTML);">#{directReport.userStatusName}</a>
									</h:outputText>
								</p:column>
								<p:column id="directReportJobTitleColumn">
									<f:facet name="header">
										<p:commandLink value="Job Title"
											action="#{exceptionSummaryBean.doSortDirectReports}"
											styleClass="headerSortLink"
										    update="directReportsTable"
										    process="@this">
											<f:param name="directReportColumn" value="jobTitle" />
										</p:commandLink>
									</f:facet>	
									#{directReport.jobTitle}  
								</p:column>
								<p:column id="directReportLocationColumn">
									<f:facet name="header">
										<p:commandLink value="Location"
											action="#{exceptionSummaryBean.doSortDirectReports}"
											styleClass="headerSortLink"
										    update="directReportsTable"
										    process="@this">
											<f:param name="directReportColumn" value="location" />
										</p:commandLink>
									</f:facet>	
									#{directReport.location}  
								</p:column>
							</p:dataTable>
						</p:outputPanel>
						<div class="verticalSpacer" />
						<div class="buttonTypeClass0">
							<p:commandButton id="saveButton"
											 styleClass="plain-button"
											 value="Save"
											 action="#{exceptionSummaryBean.saveException}"
											 oncomplete="PF('editExceptionA4jPanelVar').hide();"
											 update="editExceptionA4jPanel exceptionTable tableMessages"
											 process="editExceptionForm"
											 ajax="true"/>

							<p:commandButton id="cancelButton"
											 value="Cancel"
											 styleClass="plain-button"
											 immediate="true"
											 process="@this"
											 update="editExceptionForm"
											 action="#{exceptionSummaryBean.cancelEditException}"
											 oncomplete="PF('editExceptionA4jPanelVar').hide();"
											 ajax="true" />
						</div>
					</h:form>
				</p:dialog>
				<p:dialog id="searchSupervisorModalPanel" resizeable="true"
					draggable="true" height="500" width="750"
					widgetVar="searchSupervisorDialog"
				    appendTo="@body"
					modal="true" closable="false" showHeader="true"
				  	style="overflow: hidden; display: none;"
					styleClass="searchSupervisorModalPanelCls">

					<f:facet name="header">
						<h:outputLabel value="Search Supervisor" />
					</f:facet>
					<f:facet name="controller">
						<h:panelGroup>
							<a id="hidelink" style="float: left" onclick="PF('searchSupervisorDialog').hide()">close</a>
						</h:panelGroup>
					</f:facet>
					<h:form id="searchUserForm" style="overflow-x:hidden; overflow-y:hidden;">
						<div>
							<div class="sectionHeaderSmall">
								Search Supervisor
							</div>
						</div>
						<h:panelGrid columns="6">
							<h:outputLabel value="Key ID:" />
							<h:inputText id="supervisorKeyIdSearchText"
								value="#{exceptionSummaryBean.supervisorSearchKeyId}" 
								onchange="this.value = this.value.toUpperCase();" />
							<h:outputLabel value="Last Name:" />
							<h:inputText id="supervisorLastNameSearchText"
								value="#{exceptionSummaryBean.supervisorSearchLastName}"
								onchange="this.value = this.value.toUpperCase();" />
							<h:outputLabel value="First Name:" />
							<h:inputText id="supervisorFirstNameSearchText"
								value="#{exceptionSummaryBean.supervisorSearchFirstName}"
								onchange="this.value = this.value.toUpperCase();" />
						</h:panelGrid>
						<div style="verticalSpacer" />
						<div class="buttonTypeClass goButtonCls">
							<p:commandButton id="searchButton" value="Go"
											 process="@form"
											 styleClass="plain-button"
											 update="searchUserForm:searchUserTable"
											 oncomplete="PF('searchSupervisorDialog').show(); supervisorPaginatorCall();"
											 action="#{exceptionSummaryBean.doSupervisorSearch}" />
							<p:commandButton id="resetButton5" value="Reset"
											 process="@form"
											 styleClass="plain-button"
											 update="searchUserForm:searchUserTable
											 		bodyForm:tabPanel:searchUserForm:supervisorFirstNameSearchText
											 		bodyForm:tabPanel:searchUserForm:supervisorLastNameSearchText
											 		bodyForm:tabPanel:searchUserForm:supervisorKeyIdSearchText"
											 oncomplete="PF('searchSupervisorDialog').show();"
											 action="#{exceptionSummaryBean.resetSupervisorSearch}" />
						</div>
						<div style="verticalSpacer" />
						<p:outputPanel autoUpdate="true">
							<p:dataTable id="searchUserTable"
								value="#{exceptionSummaryBean.supervisorSearchList}"
								var="user" tableStyle="width: auto;"
								rows="10" style="overflow-x:hidden; overflow-y:hidden;"
								styleClass="defaultTableHeader searchUserTableCls"
								paginator="true" paginatorPosition="bottom"
								paginatorTemplate="#{not empty exceptionSummaryBean.supervisorSearchList ? '{CurrentPageReport} {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink} {RowsPerPageDropdown}' : ''}"
								currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
								rowsPerPageTemplate="10,25,50,100"
								rowIndexVar="rowIndex" rowKeyVar="rowKey"

							>
								<p:column>
									<f:facet name="header">
										<p:commandLink value="Last Name" action="#{exceptionSummaryBean.doSupervisorSort}"
											styleClass="headerSortLink"
										    update="searchUserTable"
										    process="@this"
											oncomplete="supervisorPaginatorCall();">
											<f:param name="supervisorColumn" value="lastName" />
										</p:commandLink>
									</f:facet>
									#{user.lastName}
								</p:column>
								<p:column>
									<f:facet name="header">
										<p:commandLink value="First Name" action="#{exceptionSummaryBean.doSupervisorSort}"
											styleClass="headerSortLink"
											update="searchUserTable"
										    process="@this"
											oncomplete="supervisorPaginatorCall();">
											<f:param name="supervisorColumn" value="firstName" />
										</p:commandLink>
									</f:facet>
									#{user.firstName}
								</p:column>
								<p:column>
									<f:facet name="header">
										<p:commandLink value="Key Id"
											action="#{exceptionSummaryBean.doSupervisorSort}"
											styleClass="headerSortLink"
										    update="searchUserTable"
										    process="@this"
											oncomplete="supervisorPaginatorCall();">
											<f:param name="supervisorColumn" value="keyId" />
										</p:commandLink>
									</f:facet>
									<p:commandLink value="#{user.keyId}"
										action="#{exceptionSummaryBean.doAssignSelectedSupervisor}"
										process="@this"
										update="editExceptionA4jPanel"
										oncomplete="PF('searchSupervisorDialog').hide(); PF('editExceptionA4jPanelVar').show();"
										styleClass="headerSortLink">
										<f:param name="selectedSupervisorId" value="#{user.userId}" />
									</p:commandLink>
								</p:column>
								<p:column>
									<f:facet name="header">
										<p:commandLink value="Division" action="#{exceptionSummaryBean.doSupervisorSort}"
											styleClass="headerSortLink"
										    update="searchUserTable"
										    process="@this"
											oncomplete="supervisorPaginatorCall();">
											<f:param name="supervisorColumn" value="divisionName" />
										</p:commandLink>
									</f:facet>
									#{user.divisionName}
								</p:column>
								<p:column>
									<f:facet name="header">
										<p:commandLink value="Location" action="#{exceptionSummaryBean.doSupervisorSort}"
											styleClass="headerSortLink"
										    update="searchUserTable"
										    process="@this"
											oncomplete="supervisorPaginatorCall();">
											<f:param name="supervisorColumn" value="location" />
										</p:commandLink>
									</f:facet>
									#{user.location}
								</p:column>
								<p:column>
									<f:facet name="header">
										<p:commandLink value="Status" action="#{exceptionSummaryBean.doSupervisorSort}"
											styleClass="headerSortLink"
											update="searchUserTable"
											process="@this"
											oncomplete="supervisorPaginatorCall();">
											<f:param name="supervisorColumn" value="userStatusDescription" />
										</p:commandLink>
									</f:facet>
									#{user.userStatusDescription}
								</p:column>
								<p:column>
									<f:facet name="header">
										<p:commandLink value="Type" action="#{exceptionSummaryBean.doSupervisorSort}"
											styleClass="headerSortLink"
											update="searchUserTable"
											process="@this"
											oncomplete="supervisorPaginatorCall();">
											<f:param name="supervisorColumn" value="userTypeDescription" />
										</p:commandLink>
									</f:facet>
									#{user.userTypeDescription}
								</p:column>
							</p:dataTable>
						</p:outputPanel>
						<table width="100%">
							<tbody>
								<tr>
									<td align="left" class="buttonTypeClass1" styleClass="plain-button">
										<p:commandButton id="cancelButton" value="Cancel"
											styleClass="plain-button"
											process="@this"
											oncomplete="PF('searchSupervisorDialog').hide();"
											action="#{exceptionSummaryBean.doCancelSearchSupervisor}" />
									</td>
								</tr>
							</tbody>
						</table>
					</h:form>
				</p:dialog>
			</p:outputPanel>
		</ui:define>
		<script type="text/javascript">
				$(document).ready(function() {
					$('#bodyForm\\:tabPanel\\:exceptionTable .ui-paginator-rpp-label').text('items per page').addClass('exception-table-class');
				});
				function paginatorCall(){
				$('#bodyForm\\:tabPanel\\:exceptionTable .ui-paginator-rpp-label').text('items per page').addClass('exception-table-class');
				}
				function supervisorPaginatorCall(){
				$('#bodyForm\\:tabPanel\\:searchUserForm\\:searchUserTable_paginator_bottom .ui-paginator-rpp-label').text('items per page').addClass('search-supervisor-class');
				}
			</script>
</ui:composition>
</html>

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
	  xmlns:ui="http://java.sun.com/jsf/facelets"
	  xmlns:h="http://java.sun.com/jsf/html"
	  xmlns:f="http://java.sun.com/jsf/core"
	  xmlns:p="http://primefaces.org/ui">

<ui:composition template="../common/main.xhtml">
	<ui:define name="body">
		<p:ajaxStatus id="tableMaintenanceAjaxLoadStatus"
					  onstart="showModalInfoWindow();"
					  oncomplete="hideModalInfoWindow();" />

		<p:tabView id="tabPanel"
				   widgetVar="tabPanelWidget"
				   activeIndex="#{adminConsoleBean.selectedTab}"
				   dynamic="true"
				   cache="false"
				   styleClass="tabStyle userTableTabAdminConsole" style="min-height: 25vh;">
			<p:ajax event="tabChange"
					update="bodyForm:headerMessages"
					listener="#{adminConsoleBean.onTabChange}"
					oncomplete="triggerCommandLink();"/>
			<p:tab id="rejectsTab" title="Rejects" rendered="#{!sessionDataBean.systemUser.ipsUser}">
			  <ui:insert name="rejects"/>
			</p:tab>
			<p:tab id="exceptionReportingTab" title="Exception Reporting" rendered="#{!sessionDataBean.systemUser.ipsUser}">
			  <ui:insert name="exceptionReportingTab"/>
			</p:tab>
			<p:tab id="tableMaintenanceTab" title="Table Maintenance">
			  <ui:insert name="tableMaintenanceTab"/>
			</p:tab>
		</p:tabView>

		<script type="text/javascript">
			function triggerCommandLink() {
				var activeTab = PF('tabPanelWidget').getActiveIndex();
				if (activeTab === 2) { // Assuming "Table Maintenance" is the third tab (index 2)
					document.getElementById('bodyForm:headerAdminConsoleCommandLink').click();
				}
			}
		</script>
	</ui:define>
</ui:composition>
</html>
