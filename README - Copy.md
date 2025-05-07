```

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:ui="http://java.sun.com/jsf/facelets"
      xmlns:h="http://xmlns.jcp.org/jsf/html"
      xmlns:f="http://java.sun.com/jsf/core"
      xmlns:p="http://primefaces.org/ui">

<ui:composition template="../common/adminConsoleTemplate.xhtml" id="exceptionComposition">
	<ui:define name="styles" id="stylesDefine">
		<style type="text/css" id="exceptionStyles">
			select {
				font-size: 0.8em;
			}
		</style>
	</ui:define>
	<ui:define name="script" id="scriptDefine">
		<script type="text/javascript" id="exceptionScript">
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
					if (selector.options[i].text === value) {
						selector.selectedIndex = i;
						break;
					}
				}
			}
			
			/* ]]> */
		</script>
	</ui:define>
	<ui:define name="exceptionTab" id="exceptionTabDefine">
		<div id="filterContainer">
			<h:outputLabel id="selectFilterLabel" value="Filter: " />  

			<!--  Select Filter ListBox -->
			<h:selectOneMenu id="filterListbox"
				value="#{exceptionSummaryBean.activeFilter}">
				<p:ajax event="change" ajaxSingle="true" id="filterAjax"
						listener="#{exceptionSummaryBean.switchFilter}"
						update="@form" />
				<f:selectItems value="#{exceptionSummaryBean.availableFilters}" id="filterItems"/>
			</h:selectOneMenu>
		</div>
		<div class="verticalSpacer" id="spacer1"></div>
		<p:outputPanel autoUpdate="true" id="exceptionOutputPanel">
			<p:dataTable id="exceptionTable"
				value="#{exceptionSummaryBean.exceptionList}" var="exception"
				rows="#{exceptionSummaryBean.displayAmount}"
				paginator="true"
				paginatorPosition="bottom"
				paginatorTemplate="{CurrentPageReport}  {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink}"
				currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
				rowsPerPageTemplate="10,25,50,100"
				rowIndexVar="rowIndex" rowKeyVar="rowKey"
				scrollable="true"
				styleClass="defaultTableHeader"
				rowStyleClasses="oddRow, evenRow">

				<p:column id="keyIdColumn">
					<f:facet name="header">
						<p:commandLink value="Key ID" id="keyIdSortLink"
							action="#{exceptionSummaryBean.doSort}"
							styleClass="headerSortLink">
							<f:param name="column" value="keyId" />
						</p:commandLink>
					</f:facet>	
					#{exception.keyId}  
				</p:column>
				<p:column id="nameColumn">
					<f:facet name="header">
						<p:commandLink value="Name" id="nameSortLink"
							action="#{exceptionSummaryBean.doSort}"
							styleClass="headerSortLink">
							<f:param name="column" value="formattedFullName" />
						</p:commandLink>
					</f:facet>	
					#{exception.formattedFullName}  
				</p:column>
				<p:column id="divisionColumn">
					<f:facet name="header">
						<p:commandLink value="Division" id="divisionSortLink"
							action="#{exceptionSummaryBean.doSort}"
							styleClass="headerSortLink">
							<f:param name="column" value="divisionName" />
						</p:commandLink>
					</f:facet>
					<p:outputPanel id="divisionDisplayPanel" autoUpdate="true">
						<p:commandLink id="missingDivisionLink"
							action="#{exceptionSummaryBean.showExceptionEditPanel}"
							rendered="#{exception.missingDivision}" value="edit">
							<f:param name="editId" value="#{exception.userId}" />
						</p:commandLink>
						<h:outputText rendered="#{!exception.missingDivision}"
							value="#{exception.divisionName}" id="divisionNameText"/>
					</p:outputPanel>
				</p:column>
				<p:column id="departmentColumn">
					<f:facet name="header">
						<p:commandLink value="Department Name/Cost Center" id="deptSortLink"
							action="#{exceptionSummaryBean.doSort}"
							styleClass="headerSortLink">
							<f:param name="column" value="departmentNameCostCenter" />
						</p:commandLink>
					</f:facet>
					<p:outputPanel id="departmentDisplayPanel" autoUpdate="true">
						<p:commandLink id="missingDepartmentLink"
							action="#{exceptionSummaryBean.showExceptionEditPanel}"
							rendered="#{exception.missingDepartment}" value="edit">
							<f:param name="editId" value="#{exception.userId}" />
						</p:commandLink>
						<h:outputText rendered="#{!exception.missingDepartment}"
							value="#{exception.departmentNameCostCenter}" id="deptNameText"/>
					</p:outputPanel>
				</p:column>
				<p:column id="supervisorColumn">
					<f:facet name="header">
						<p:commandLink value="Supervisor" id="supervisorSortLink"
							action="#{exceptionSummaryBean.doSort}"
							styleClass="headerSortLink">
							<f:param name="column" value="supervisorName" />
						</p:commandLink>
					</f:facet>
					<p:outputPanel id="supervisorDisplayPanel" autoUpdate="true">
						<p:commandLink id="missingSupervisor"
							action="#{exceptionSummaryBean.showExceptionEditPanel}"
							rendered="#{exception.missingSupervisor}" value="edit">
							<f:param name="editId" value="#{exception.userId}" />
						</p:commandLink>
						<h:outputText rendered="#{!exception.missingSupervisor}"
							value="#{exception.supervisorName}" id="supervisorNameText"/>
					</p:outputPanel>
				</p:column>
				<p:column id="jobTitleColumn">
					<f:facet name="header">
						<p:commandLink value="Job Title" id="jobTitleSortLink"
							action="#{exceptionSummaryBean.doSort}"
							styleClass="headerSortLink">
							<f:param name="column" value="jobTitle" />
						</p:commandLink>
					</f:facet>	
					#{exception.jobTitle}  
				</p:column>
				<p:column id="userTypeColumn">
					<f:facet name="header">
						<p:commandLink value="User Type" id="userTypeSortLink"
							action="#{exceptionSummaryBean.doSort}"
							styleClass="headerSortLink">
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
							value="#{exception.userTypeName}" id="userTypeText"/>
					</p:outputPanel>
				</p:column>
				<p:column id="userStatusColumn">
					<f:facet name="header">
						<p:commandLink value="User Status" id="userStatusSortLink"
							action="#{exceptionSummaryBean.doSort}"
							styleClass="headerSortLink">
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
							value="#{exception.userStatusName}" id="userStatusText"/>
					</p:outputPanel>
				</p:column>
				<p:column id="emailColumn">
					<f:facet name="header">
						<p:commandLink value="Email" id="emailSortLink"
							action="#{exceptionSummaryBean.doSort}"
							styleClass="headerSortLink">
							<f:param name="column" value="email" />
						</p:commandLink>
					</f:facet>	
					<p:commandLink id="missingEmail"
						action="#{exceptionSummaryBean.showExceptionEditPanel}"
						rendered="#{exception.missingEmail}" value="edit">
						<f:param name="editId" value="#{exception.userId}" />
					</p:commandLink>
					<h:outputText rendered="#{!exception.missingEmail}"
						value="#{exception.email}" id="emailText"/>
				</p:column>
				<p:column id="locationColumn">
					<f:facet name="header">
						<p:commandLink value="Location" id="locationSortLink"
							action="#{exceptionSummaryBean.doSort}"
							styleClass="headerSortLink">
							<f:param name="column" value="location" />
						</p:commandLink>
					</f:facet>	
					#{exception.location}  
				</p:column>
				<p:column id="phoneColumn">
					<f:facet name="header">
						<p:commandLink value="Phone" id="phoneSortLink"
							action="#{exceptionSummaryBean.doSort}"
							styleClass="headerSortLink">
							<f:param name="column" value="phone" />
						</p:commandLink>
					</f:facet>	
					#{exception.phone}  
				</p:column>
			</p:dataTable>
		</p:outputPanel>
	</ui:define>
	<ui:define name="modalPanels" id="modalPanelsDefine">
		<p:outputPanel autoUpdate="true" id="editExceptionA4jPanel">
			<p:dialog id="editExceptionDialog" resizeable="true"
				draggable="true" width="1000" height="600"
				rendered="#{exceptionSummaryBean.renderEditExceptionPanel}"
				visible="true" style="overflow: auto;">

				<f:facet name="header">
					<h:outputLabel value="Edit Exception" id="editExceptionHeader"/>
				</f:facet>

				<div id="messagesContainer">
					<h:messages id="msgId" fatalClass="fatalMessage"
						errorClass="errorMessage" warnClass="warningMessage"
						infoClass="infoMessage" layout="table" />
				</div>

				<h:form id="editExceptionForm">
					<p:dataTable id="userExceptionsTable"
						value="#{exceptionSummaryBean.exceptionList}" var="exception"
						rows="1" 
						paginator="true" paginatorPosition="bottom"
						paginatorTemplate="{CurrentPageReport}  {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink}"
						currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
						rowsPerPageTemplate="10,25,50,100"
						rowIndexVar="rowIndex" rowKeyVar="rowKey"
						scrollable="true" 
						headerClass="defaultTableHeader">
	
						<p:column id="modalNameColumn">
							<f:facet name="header">Name</f:facet>	
							#{exceptionSummaryBean.selectedException.formattedFullName}  
						</p:column>
						<p:column id="modalDivisionColumn">
							<f:facet name="header">Division</f:facet>
							<h:selectOneMenu id="missingDivisionSelectOne"
								 rendered="#{exceptionSummaryBean.selectedException.missingDivision}"
								 value="#{exceptionSummaryBean.selectedDivisionId}">
								<f:selectItems value="#{exceptionSummaryBean.divisionSelectItems}" id="divisionItems"/>
							</h:selectOneMenu>
							<h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingDivision}"
								value="#{exceptionSummaryBean.selectedException.divisionName}" id="modalDivisionText"/>  
						</p:column>
						<p:column id="modalDepartmentColumn">
							<f:facet name="header">Department Name/Cost Center</f:facet>
							<h:selectOneMenu id="missingDepartmentNameCostCenterSelectOne"
								 rendered="#{exceptionSummaryBean.selectedException.missingDepartment}"
								 value="#{exceptionSummaryBean.selectedDepartmentId}">
								<f:selectItems value="#{exceptionSummaryBean.departmentNameCostCenterSelectItems}" id="departmentItems"/>
							</h:selectOneMenu>
							<h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingDepartment}"
								value="#{exceptionSummaryBean.selectedException.departmentNameCostCenter}" id="modalDepartmentText"/>  
						</p:column>
						<p:column id="modalUserTypeColumn">
							<f:facet name="header">User Type</f:facet>	
							<h:selectOneMenu id="missingUserTypeSelectOne"
								 rendered="#{exceptionSummaryBean.selectedException.missingUserType}"
								 value="#{exceptionSummaryBean.selectedUserTypeId}">
								<f:selectItems value="#{exceptionSummaryBean.userTypeSelectItems}" id="userTypeItems"/>
							</h:selectOneMenu>
							<h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingUserType}"
								value="#{exceptionSummaryBean.selectedException.userTypeName}" id="modalUserTypeText"/>  
						</p:column>
						<p:column id="modalUserStatusColumn">
							<f:facet name="header">User Status</f:facet>	
							<h:selectOneMenu id="missingUserStatusSelectOne"
								 rendered="#{exceptionSummaryBean.selectedException.missingUserStatus}"
								 value="#{exceptionSummaryBean.selectedUserStatusId}">
								<f:selectItems value="#{exceptionSummaryBean.userStatusSelectItems}" id="userStatusItems"/>
							</h:selectOneMenu>
							<h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingUserStatus}"
								value="#{exceptionSummaryBean.selectedException.userStatusName}" id="modalUserStatusText"/>  
						</p:column>
						<p:column id="modalEmailColumn">
							<f:facet name="header">Email</f:facet>	
							<h:inputText id="exceptionEmailInputText" value="#{exceptionSummaryBean.selectedException.email}" rendered="#{exceptionSummaryBean.selectedException.missingEmail}" onchange="this.value = this.value.toUpperCase();"/>  
							<h:outputText id="exceptionEmailOutputText" value="#{exceptionSummaryBean.selectedException.email}" rendered="#{!exceptionSummaryBean.selectedException.missingEmail}" />  
						</p:column>
						<p:column id="modalJobTitleColumn">
							<f:facet name="header">Job Title</f:facet>	
							#{exceptionSummaryBean.selectedException.jobTitle}  
						</p:column>
						<p:column id="modalLocationColumn">
							<f:facet name="header">Location</f:facet>	
							#{exceptionSummaryBean.selectedException.location}  
						</p:column>
					</p:dataTable>
					<div class="verticalSpacer" id="spacer2"/>
					<div style="width: 100%;" id="supervisorPanel">
						<p:outputPanel ajautoUpdate="true" id="supervisorOutputPanel">
							Supervisor: 
							<h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingSupervisor}"
									value="#{exceptionSummaryBean.selectedException.supervisorName}" id="supervisorNameOutput"/>
							<p:commandLink rendered="#{exceptionSummaryBean.selectedException.missingSupervisor or exceptionSummaryBean.supervisorChanged}" action="#{exceptionSummaryBean.doShowSearchSupervisorPanel}" value="Select supervisor" id="selectSupervisorLink"/>
						</p:outputPanel>
					</div>
					<div class="verticalSpacer" id="spacer3"/>
					<p:outputPanel autoUpdate="true" id="directReportsPanel">
						<p:dataTable id="directReportsTable"
							value="#{exceptionSummaryBean.directReportsList}" 
							var="directReport"
							rows="#{exceptionSummaryBean.displayAmount}"
							paginator="true" paginatorPosition="bottom"
							paginatorTemplate="{CurrentPageReport}  {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink}"
							currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
							rowsPerPageTemplate="10,25,50,100"
							rowIndexVar="rowIndex" rowKeyVar="rowKey"
							scrollable="true"
							headerClass="defaultTableHeader" 
							rowClasses="oddRow, evenRow">
		
							<p:column id="directReportKeyIdColumn">
								<f:facet name="header">
									<p:commandLink value="Key ID" id="drKeyIdSortLink"
										action="#{exceptionSummaryBean.doSortDirectReports}"
										styleClass="headerSortLink" ajaxSingle="true">
										<f:param name="directReportColumn" value="keyId" />
									</p:commandLink>
								</f:facet>	
								#{directReport.keyId}  
							</p:column>
							<p:column id="directReportNameColumn">
								<f:facet name="header">
									<p:commandLink value="Name" id="drNameSortLink"
										action="#{exceptionSummaryBean.doSortDirectReports}"
										styleClass="headerSortLink" ajaxSingle="true">
										<f:param name="directReportColumn" value="formattedFullName" />
									</p:commandLink>
								</f:facet>	
								#{directReport.formattedFullName}  
							</p:column>
							<p:column id="directReportDivisionColumn">
								<f:facet name="header">
									<p:commandLink value="Division" id="drDivisionSortLink"
										action="#{exceptionSummaryBean.doSortDirectReports}"
										styleClass="headerSortLink" ajaxSingle="true">
										<f:param name="directReportColumn" value="divisionName" />
									</p:commandLink>
								</f:facet>
								<h:outputText rendered="#{!directReport.missingDivision and !exceptionSummaryBean.selectedException.missingDivision}" id="drDivisionText1">
									#{directReport.divisionName}
								</h:outputText>
								<h:outputText rendered="#{!directReport.missingDivision and exceptionSummaryBean.selectedException.missingDivision}" id="drDivisionText2">
									<a href="#" onmouseup="setDivisionValue(this.innerHTML);">#{directReport.divisionName}</a>
								</h:outputText>
							</p:column>
							<p:column id="directReportDepartmentColumn">
								<f:facet name="header">
									<p:commandLink value="Department" id="drDepartmentSortLink"
										action="#{exceptionSummaryBean.doSortDirectReports}"
										styleClass="headerSortLink" ajaxSingle="true">
										<f:param name="directReportColumn" value="departmentNameCostCenter" />
									</p:commandLink>
								</f:facet>
								<h:outputText rendered="#{!directReport.missingDepartment and !exceptionSummaryBean.selectedException.missingDepartment}" id="drDepartmentText1">
									#{directReport.departmentNameCostCenter}
								</h:outputText>
								<h:outputText rendered="#{!directReport.missingDepartment and exceptionSummaryBean.selectedException.missingDepartment}" id="drDepartmentText2">
									<a href="#" onmouseup="setDepartmentValue(this.innerHTML);">#{directReport.departmentNameCostCenter}</a>
								</h:outputText>
							</p:column>
							<p:column id="directReportUserTypeColumn">
								<f:facet name="header">
									<p:commandLink value="User Type" id="drUserTypeSortLink"
										action="#{exceptionSummaryBean.doSortDirectReports}"
										styleClass="headerSortLink" ajaxSingle="true">
										<f:param name="directReportColumn" value="userTypeName" />
									</p:commandLink>
								</f:facet>	
								<h:outputText rendered="#{!directReport.missingUserType and !exceptionSummaryBean.selectedException.missingUserType}" id="drUserTypeText1">
									#{directReport.userTypeName}
								</h:outputText>
								<h:outputText rendered="#{!directReport.missingUserType and exceptionSummaryBean.selectedException.missingUserType}" id="drUserTypeText2">
									<a href="#" onmouseup="setUserTypeValue(this.innerHTML);">#{directReport.userTypeName}</a>
								</h:outputText>
							</p:column>
							<p:column id="directReportUserStatusColumn">
								<f:facet name="header">
									<p:commandLink value="User Status" id="drUserStatusSortLink"
										action="#{exceptionSummaryBean.doSortDirectReports}"
										styleClass="headerSortLink" ajaxSingle="true">
										<f:param name="directReportColumn" value="userStatusName" />
									</p:commandLink>
								</f:facet>	
								<h:outputText rendered="#{!directReport.missingUserStatus and !exceptionSummaryBean.selectedException.missingUserStatus}" id="drUserStatusText1">
									#{directReport.userStatusName}
								</h:outputText>
								<h:outputText rendered="#{!directReport.missingUserStatus and exceptionSummaryBean.selectedException.missingUserStatus}" id="drUserStatusText2">
									<a href="#" onmouseup="setUserStatusValue(this.innerHTML);">#{directReport.userStatusName}</a>
								</h:outputText>
							</p:column>
							<p:column id="directReportJobTitleColumn">
								<f:facet name="header">
									<p:commandLink value="Job Title" id="drJobTitleSortLink"
										action="#{exceptionSummaryBean.doSortDirectReports}"
										styleClass="headerSortLink" ajaxSingle="true">
										<f:param name="directReportColumn" value="jobTitle" />
									</p:commandLink>
								</f:facet>	
								#{directReport.jobTitle}  
							</p:column>
							<p:column id="directReportLocationColumn">
								<f:facet name="header">
									<p:commandLink value="Location" id="drLocationSortLink"
										action="#{exceptionSummaryBean.doSortDirectReports}"
										styleClass="headerSortLink" ajaxSingle="true">
										<f:param name="directReportColumn" value="location" />
									</p:commandLink>
								</f:facet>	
								#{directReport.location}  
							</p:column>
						</p:dataTable>
					</p:outputPanel>
					<div class="verticalSpacer" id="spacer4"/>
					<div id="buttonContainer">
						<h:commandButton id="saveButton" value="Save"
							action="#{exceptionSummaryBean.saveException}" />

						<h:commandButton id="cancelButton" value="Cancel"
							action="#{exceptionSummaryBean.cancelEditException}" 
							immediate="true"/>
					</div>
				</h:form>
			</p:dialog>
			<p:dialog id="searchSupervisorModalPanel" resizeable="true"
				draggable="true" height="500" width="750"
				rendered="#{exceptionSummaryBean.renderSearchSupervisorModalPanel}"
				visible="true" widgetVar="searchSupervisorDialog">

				<f:facet name="header">
					<h:outputLabel value="Search Supervisor" id="searchSupervisorHeader"/>
				</f:facet>
				<f:facet name="controller">
					<h:panelGroup id="controllerPanel">
<h:panelGroup id="closeLinkPanel">
                        <a id="hidelink" style="float: left" onclick="PF('searchSupervisorDialog').hide()">close</a>
                    </h:panelGroup>
                </f:facet>
                <h:form id="searchUserForm">
                    <div id="searchHeaderContainer">
                        <div class="sectionHeaderSmall" id="searchHeader">
                            Search Supervisor
                        </div>
                    </div>
                    <h:panelGrid columns="6" id="searchGrid">
                        <h:outputLabel value="Key ID:" id="keyIdLabel"/>
                        <h:inputText id="supervisorKeyIdSearchText"
                                     value="#{exceptionSummaryBean.supervisorSearchKeyId}"
                                     onchange="this.value = this.value.toUpperCase();"/>
                        <h:outputLabel value="Last Name:" id="lastNameLabel"/>
                        <h:inputText id="supervisorLastNameSearchText"
                                     value="#{exceptionSummaryBean.supervisorSearchLastName}"
                                     onchange="this.value = this.value.toUpperCase();"/>
                        <h:outputLabel value="First Name:" id="firstNameLabel"/>
                        <h:inputText id="supervisorFirstNameSearchText"
                                     value="#{exceptionSummaryBean.supervisorSearchFirstName}"
                                     onchange="this.value = this.value.toUpperCase();"/>
                    </h:panelGrid>
                    <div id="searchButtonContainer">
                        <h:commandButton id="searchButton" value="Go"
                                         action="#{exceptionSummaryBean.doSupervisorSearch}"/>
                        <h:commandButton id="resetButton" value="Reset"
                                         action="#{exceptionSummaryBean.resetSupervisorSearch}"/>
                    </div>
                    <p:outputPanel autoUpdate="true" id="searchResultsPanel">
                        <p:dataTable id="searchUserTable"
                                     value="#{exceptionSummaryBean.supervisorSearchList}"
                                     var="user" rows="10" headerClass="defaultTableHeader"
                                     paginator="true" paginatorPosition="bottom"
                                     paginatorTemplate="{CurrentPageReport}  {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink}"
                                     currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
                                     rowsPerPageTemplate="10,25,50,100"
                                     rowIndexVar="rowIndex" rowKeyVar="rowKey"
                                     scrollable="true"
                                     rowStyleClasses="oddRow, evenRow" style="width:100%">
                            <p:column id="searchLastNameColumn">
                                <f:facet name="header">
                                    <p:commandLink value="Last Name" id="lastNameSortLink" action="#{exceptionSummaryBean.doSupervisorSort}"
                                                   styleClass="headerSortLink">
                                        <f:param name="supervisorColumn" value="lastName"/>
                                    </p:commandLink>
                                </f:facet>
                                #{user.lastName}
                            </p:column>
                            <p:column id="searchFirstNameColumn">
                                <f:facet name="header">
                                    <p:commandLink value="First Name" id="firstNameSortLink" action="#{exceptionSummaryBean.doSupervisorSort}"
                                                   styleClass="headerSortLink">
                                        <f:param name="supervisorColumn" value="firstName"/>
                                    </p:commandLink>
                                </f:facet>
                                #{user.firstName}
                            </p:column>
                            <p:column id="searchKeyIdColumn">
                                <f:facet name="header">
                                    <p:commandLink value="Key Id" id="keyIdSortLink2"
                                                   action="#{exceptionSummaryBean.doSupervisorSort}"
                                                   styleClass="headerSortLink">
                                        <f:param name="supervisorColumn" value="keyId"/>
                                    </p:commandLink>
                                </f:facet>
                                <p:commandLink value="#{user.keyId}" id="selectSupervisorLink"
                                               action="#{exceptionSummaryBean.doAssignSelectedSupervisor}"
                                               styleClass="headerSortLink">
                                    <f:param name="selectedSupervisorId" value="#{user.userId}"/>
                                </p:commandLink>
                            </p:column>
                            <p:column id="searchDivisionColumn">
                                <f:facet name="header">
                                    <p:commandLink value="Division" id="divisionSortLink2" action="#{exceptionSummaryBean.doSupervisorSort}"
                                                   styleClass="headerSortLink">
                                        <f:param name="supervisorColumn" value="divisionName"/>
                                    </p:commandLink>
                                </f:facet>
                                #{user.divisionName}
                            </p:column>
                            <p:column id="searchLocationColumn">
                                <f:facet name="header">
                                    <p:commandLink value="Location" id="locationSortLink2" action="#{exceptionSummaryBean.doSupervisorSort}"
                                                   styleClass="headerSortLink">
                                        <f:param name="supervisorColumn" value="location"/>
                                    </p:commandLink>
                                </f:facet>
                                #{user.location}
                            </p:column>
                            <p:column id="searchStatusColumn">
                                <f:facet name="header">
                                    <p:commandLink value="Status" id="statusSortLink" action="#{exceptionSummaryBean.doSupervisorSort}"
                                                   styleClass="headerSortLink">
                                        <f:param name="supervisorColumn" value="userStatusDescription"/>
                                    </p:commandLink>
                                </f:facet>
                                #{user.userStatusDescription}
                            </p:column>
                            <p:column id="searchTypeColumn">
                                <f:facet name="header">
                                    <p:commandLink value="Type" id="typeSortLink" action="#{exceptionSummaryBean.doSupervisorSort}"
                                                   styleClass="headerSortLink">
                                        <f:param name="supervisorColumn" value="userTypeDescription"/>
                                    </p:commandLink>
                                </f:facet>
                                #{user.userTypeDescription}
                            </p:column>
                        </p:dataTable>
                    </p:outputPanel>
                </h:form>
            </p:dialog>
        </p:outputPanel>
    </ui:define>
</ui:composition>
