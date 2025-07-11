```
<f:view xmlns="http://www.w3.org/1999/xhtml"
		xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
		xmlns:h="http://xmlns.jcp.org/jsf/html"
		xmlns:f="http://java.sun.com/jsf/core"
		xmlns:p="http://primefaces.org/ui">

	<style type="text/css">
		button#bodyForm\:tabPanel\:bodyRejectForm\:goSearchButton3,
		button#bodyForm\:tabPanel\:bodyRejectForm\:resetButton1,
		button#bodyForm\:tabPanel\:bodyRejectForm\:reconcileRejectsButton {
			margin-top: 10px;
		}
		div#bodyForm\:tabPanel\:searchUserForm1\:searchUserModalPanel,
		div#bodyForm\:tabPanel\:addSearchedAssociatedUserForm\:associatedUserSearchModalPanel,
		div#bodyForm\:tabPanel\:addAlternateIdForm\:AddAlternateIdPanel,
		div#bodyForm\:tabPanel\:associatedUserForm\:searchAssociatedUserModalPanel {
			border-radius: 0;
		}

		.addUserModalPanelClass .ui-paginator .ui-paginator-current {
			margin: 0 !important;
		}

		div#bodyForm\:tabPanel\:searchUserForm1\:searchUserModalPanel,
		div#bodyForm\:tabPanel\:associatedUserForm\:searchAssociatedUserModalPanel {
			width: 1035px !important;
		}

		div#bodyForm\:tabPanel\:addAlternateIdForm\:AddAlternateIdPanel_content {
			height: 430px !important;
		}
	</style>
	<h:form id="bodyRejectForm">
		<script src="#{request.contextPath}/resources/js/rejectedUserSummaryFunctions.js" type="text/javascript">
			// keeping it here to load the functions on page load in rejects tab is selected
		</script>
		<div class="adminAppDivCls">
			<!--  Search -->
			<p:outputPanel id="filterPanel">

				<table width="100%">
					<tbody>
					<tr>
						<td align="left">
							<h:outputLabel id="keyIdSearchLabel">Key ID:
							</h:outputLabel>
						</td>
						<td align="left">

							<h:inputText id="keyIdSearchText2" maxlength="100"
										 value="#{rejectedUserSummaryBean.searchSrcUserId}"
										 onchange="this.value = this.value.toUpperCase();">
							</h:inputText>
						</td>
						<td align="right">
							<h:outputLabel id="selectDateLabel"> Extracted Date:</h:outputLabel>
							<h:selectOneMenu id="availablecreatedDateListbox"
											 value="#{rejectedUserSummaryBean.selectedDateCriteria}">
								<f:selectItems
										value="#{rejectedUserSummaryBean.availablecreatedDateCriteria}"/>
							</h:selectOneMenu>
						</td>
						<td class="right rightSpaced calenderAlign">
							<p:calendar id="targetDateCalendar"
										readonlyInput="true"
										showOn="button" navigator="true" widgetVar="calendarWidget"
										value="#{rejectedUserSummaryBean.createdDateCriteria}"
										pattern="MM/dd/yyyy"/>
						</td>
						<td class="rightSpaced">
							&#160;
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel id="selectStatusLabel">Extract System:
							</h:outputLabel>
						</td>
						<td align="left">
							<h:selectOneMenu id="extrctSysListbox"
											 value="#{rejectedUserSummaryBean.searchExtrctSysId}">
								<f:selectItems
										value="#{rejectedUserSummaryBean.availableExtractSystem}"/>
							</h:selectOneMenu>
						</td>
					</tr>

					<tr>
						<td colspan="5" align="left">
							<!-- Go Button -->
							<p:commandButton id="goSearchButton3"
											 styleClass="plain-button"
											 value="Go"
											 process="@form"
											 update="rejectedUserTable bodyForm:headerMessages"
											 ajax="true"
											 oncomplete="setrejectedUserTablePaginator();"
											 action="#{rejectedUserSummaryBean.goSearch}"/>
							<p:commandButton id="resetButton1"
											 styleClass="plain-button"
											 value="Reset"
											 ajax="true"
											 immediate="true"
											 process="@this"
											 oncomplete="setrejectedUserTablePaginator();"
											 update="filterPanel rejectedUserTable bodyForm:headerMessages"
											 action="#{rejectedUserSummaryBean.resetSearch}"/>
							<p:commandButton id="reconcileRejectsButton"
											 styleClass="plain-button"
											 value="Reconcile Rejects"
											 ajax="true"
											 process="@this"
											 oncomplete="setrejectedUserTablePaginator();"
											 update="bodyForm:tabPanel:rejectedUserTable bodyForm:headerMessages"
											 action="#{rejectedUserSummaryBean.doReconcile}"/>
						</td>
					</tr>
					</tbody>
				</table>
			</p:outputPanel>
		</div>
	</h:form>
	<!-- **************************     Reject  summary table     ***********************************   -->
	<p:outputPanel styleClass="adminConfBlockDivCls">
		<p:dataTable id="rejectedUserTable"
					 value="#{rejectedUserSummaryBean.rejectedUserList}"
					 var="rejectedUser"
					 paginator="true"
					 paginatorPosition="bottom"
					 paginatorTemplate="#{not empty rejectedUserSummaryBean.rejectedUserList ? '{CurrentPageReport} {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink} {RowsPerPageDropdown}' : ''}"
					 currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
					 rowsPerPageTemplate="10,25,50,100"
					 rowIndexVar="rowIndex"
					 rowKeyVar="rowKey"
					 rows="10"
					 styleClass="defaultTableHeader" style="overflow-x:hidden"
					 rowStyleClass="oddRow, evenRow">
			<p:column>
				<f:facet name="header">
					<p:commandLink value="Rejected User Id"
								   action="#{rejectedUserSummaryBean.doSort}"
								   styleClass="headerSortLink"
								   update="rejectedUserTable bodyForm:headerMessages"
								   process="@this"
								   oncomplete="setrejectedUserTablePaginator()">
						<f:param name="column" value="srcUserId"/>
					</p:commandLink>
				</f:facet>
				#{rejectedUser.srcUserId}
			</p:column>
			<p:column>
				<f:facet name="header">
					<p:commandLink value="User Name"
								   action="#{rejectedUserSummaryBean.doSort}"
								   styleClass="headerSortLink"
								   update="rejectedUserTable bodyForm:headerMessages"
								   process="@this"
								   oncomplete="setrejectedUserTablePaginator()">
						<f:param name="column" value="srcUserName"/>
					</p:commandLink>
				</f:facet>
				#{rejectedUser.srcUserName}
			</p:column>
			<p:column>
				<f:facet name="header">
					<p:commandLink value="Sys Environment"
								   action="#{rejectedUserSummaryBean.doSort}"
								   styleClass="headerSortLink"
								   update="rejectedUserTable bodyForm:headerMessages"
								   process="@this"
								   oncomplete="setrejectedUserTablePaginator()">
						<f:param name="column" value="srcApplicationName"/>
					</p:commandLink>
				</f:facet>
				#{rejectedUser.srcApplicationName}
			</p:column>
			<p:column>
				<f:facet name="header">
					<p:commandLink value="Additional Values"
								   action="#{rejectedUserSummaryBean.doSort}"
								   styleClass="headerSortLink"
								   update="rejectedUserTable bodyForm:headerMessages"
								   process="@this"
								   oncomplete="setrejectedUserTablePaginator()">
						<f:param name="column" value="additionalValues"/>
					</p:commandLink>
				</f:facet>
				#{rejectedUser.additionalValues}
			</p:column>
			<p:column>
				<f:facet name="header">
					<h:outputText value="Actions">
					</h:outputText>
				</f:facet>
				<p:commandLink value="Search User"
							   action="#{rejectedUserSummaryBean.showSearchUser}"
							   styleClass="headerSortLink"
							   oncomplete="PF('searchUserModalPanelWidget').show(); setrejectedUserTablePaginator();"
							   update="bodyForm:tabPanel:searchUserForm1:searchUserTable bodyForm:headerMessages"
							   process="@this"
							   rendered="#{rejectedUser.showSearchUser}">
					<f:param name="column" value="#{rejectedUser.srcUserName}"/>
					<f:param name="rejectedIdcolumn" value="#{rejectedUser.rejectedUserId }"/>
					<f:param name="newAltIdcolumn" value="#{rejectedUser.srcUserId }"/>
				</p:commandLink>
				<p:commandLink value="Add User | "
							   action="#{rejectedUserSummaryBean.showAddUserPanel}"
							   oncomplete="PF('addModalPanelWidget').show(); setrejectedUserTablePaginator();"
							   styleClass="headerSortLink"
							   update="@this bodyForm:tabPanel:addUserForm bodyForm:headerMessages"
							   process="@this"
							   rendered="#{rejectedUser.showAddUser}">
					<f:param name="column" value="#{rejectedUser.rejectedUserId}"/>
				</p:commandLink>
				<p:commandLink value="Add Alternate Id"
							   action="#{rejectedUserSummaryBean.showAssociatedUserSearchPanel}"
							   styleClass="headerSortLink"
							   update="@this bodyForm:tabPanel:addSearchedAssociatedUserForm:associatedUserSearchModalAjaxPanel
							   		bodyForm:headerMessages"
							   process="@this"
							   oncomplete="PF('associatedUserSearchModalAjaxVar').show(); setrejectedUserTablePaginator();"
							   rendered="#{rejectedUser.showAddUser}">
					<f:param name="rejectedIdcolumn" value="#{rejectedUser.rejectedUserId }"/>
					<f:param name="newAltIdcolumn" value="#{rejectedUser.srcUserId }"/>
				</p:commandLink>
			</p:column>
			<p:column>
				<f:facet name="header">
					<p:commandLink value="Extracted Date"
								   action="#{rejectedUserSummaryBean.doSort}"
								   styleClass="headerSortLink"
								   update="rejectedUserTable bodyForm:headerMessages"
								   process="@this"
								   oncomplete="setrejectedUserTablePaginator()">
						<f:param name="column" value="createdDate"/>
					</p:commandLink>
				</f:facet>
				<h:outputText value="#{rejectedUser.createdDate}">
					<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}"/>
				</h:outputText>
			</p:column>


		</p:dataTable>
	</p:outputPanel>

	<!-- **************************    Add NEW User ModelPanel     ***********************************   -->
	<ui:define name="styles">
		<style type="text/css">
			select {
				font-size: 1.0em;
			}

			input {
				font-size: 1.0em;
			}
		</style>
	</ui:define>
	<h:form id="addUserForm">
		<p:outputPanel id="adduserModalAjaxPanel">
			<p:dialog id="addModalPanel"
					  styleClass="addUserModalPanelClass"
					  resizeable="true"
					  widgetVar="addModalPanelWidget"
					  blockScroll="true"
					  draggable="true"
					  modal="true"
					  height="600" width="800"
					  style="overflow:auto;">

				<f:facet name="header">
					<h:outputLabel value="Add User"/>
				</f:facet>
				<div>
					<h:messages id="addUserMsgId" fatalClass="fatalMessage"
								errorClass="errorMessage" warnClass="warningMessage"
								infoClass="infoMessage" layout="table"/>
				</div>
				<table style="width:100%">
					<tbody>
					<tr>
						<td align="left">
							<h:outputLabel value="Key Id" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							#{rejectedUserSummaryBean.keyId}
						</td>
						<td align="left">
							<h:outputLabel value="Last Name" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText id="addlastNameText" maxlength="50"
										 value="#{rejectedUserSummaryBean.lastName}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="First Name" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText id="addfirstNameText" maxlength="50"
										 value="#{rejectedUserSummaryBean.firstName}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
						<td align="left">
							<h:outputLabel value="Middle Name" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText id="addmiddleNameText" maxlength="50"
										 value="#{rejectedUserSummaryBean.middleName}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
					</tr>
					<tr>
						<td align="left">

							<h:outputLabel value="User Type" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="UserTypeListbox"
											 value="#{rejectedUserSummaryBean.userType}">
								<f:selectItems
										value="#{rejectedUserSummaryBean.availableUserType}"/>
							</h:selectOneMenu>
						</td>
						<td align="left">
							<h:outputLabel value="Status"
										   styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="StatusNameText"
											 value="#{rejectedUserSummaryBean.status}" rendered="true">
								<f:selectItems
										value="#{rejectedUserSummaryBean.availableStatus}"/>
							</h:selectOneMenu>
						</td>
					</tr>

					<tr>
						<td align="left">
							<h:outputLabel value="Supervisor " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="supervisorListbox"
											 value="#{rejectedUserSummaryBean.supervisorId}">
								<f:selectItems id="searchSupervisorSelectItems"
											   value="#{rejectedUserSummaryBean.availableSupervisors}"/>
								<p:ajax event="change"
										listener="#{rejectedUserSummaryBean.populateSupevisorData}"
										oncomplete="PF('addModalPanelWidget').show();"
										process="@this"
										update="departmentListbox divisionListbox bsnssSgmntListbox
										 		locationListbox jobTitleListbox directReportsTable
										 		bodyForm:headerMessages"/>
							</h:selectOneMenu>

						</td>
						<td align="left">
							<h:outputLabel value="Department CostCenter" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="departmentListbox"
											 value="#{rejectedUserSummaryBean.departmentId}">

								<f:selectItems
										value="#{rejectedUserSummaryBean.supevisorDepartments}"/>
							</h:selectOneMenu>
						</td>
					</tr>
					<tr>
						<td align="left">

							<h:outputLabel value="Division" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="divisionListbox"
											 value="#{rejectedUserSummaryBean.divisionId}">
								<f:selectItems value="#{rejectedUserSummaryBean.supevisorDivisions}"/>
							</h:selectOneMenu>
						</td>
						<td align="left">
							<h:outputLabel value="Business Segment "
										   styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="bsnssSgmntListbox"
											 value="#{rejectedUserSummaryBean.bsnssSgmnt}" rendered="true">
								<f:selectItems
										value="#{rejectedUserSummaryBean.supevisorBusinessSegments}"/>
							</h:selectOneMenu>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="Location " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="locationListbox"
											 value="#{rejectedUserSummaryBean.location}" rendered="true">
								<f:selectItems value="#{rejectedUserSummaryBean.supevisorLocations}"/>
							</h:selectOneMenu>
						</td>
						<td align="left">
							<h:outputLabel value="Job Title " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="jobTitleListbox"
											 value="#{rejectedUserSummaryBean.jobTitle}" rendered="true">
								<f:selectItems value="#{rejectedUserSummaryBean.supevisorJobTitles}"/>
							</h:selectOneMenu>
						</td>
					</tr>

					<tr>
						<td align="center" colspan="4">
							<p:outputPanel id="directReportsPanel">
								<p:dataTable id="directReportsTable"
											 value="#{rejectedUserSummaryBean.supervisorDirectReports}"
											 var="report" height="100" width="700" rows="3"
											 paginator="true" style="overflow-x:hidden; width: 100%;"
											 paginatorPosition="bottom"
											 paginatorTemplate="#{not empty rejectedUserSummaryBean.supervisorDirectReports  ? '{CurrentPageReport} {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink} {RowsPerPageDropdown}' : ''}"
											 currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
											 rowIndexVar="rowIndex"
											 rowKeyVar="rowKey"
											 styleClass="defaultTableHeader addUserModalPanelClass"
											 rowStyleClass="oddRow, evenRow">

									<p:column>
										<f:facet name="header"> Name </f:facet>
										#{report.name}
									</p:column>
									<p:column>
										<f:facet name="header"> Department </f:facet>
										<a href="#" onmouseup="setDepartmentValue(this.innerHTML);">
											#{report.departmentNmCostCenter}</a>
									</p:column>
									<p:column>
										<f:facet name="header"> CostCenter </f:facet>
										#{report.costCenter}
									</p:column>
									<p:column>
										<f:facet name="header"> Division </f:facet>
										<a href="#" onmouseup="setDivisionValue(this.innerHTML);">#{report.division}</a>
									</p:column>
									<p:column>
										<f:facet name="header"> Business Segment </f:facet>
										<a href="#" onmouseup="setBusinessSegValue(this.innerHTML);">#{report.businessSeg}</a>
									</p:column>
									<p:column>
										<f:facet name="header"> Job Title </f:facet>
										<a href="#" onmouseup="setJobTitleValue(this.innerHTML);">#{report.jobTitle}</a>
									</p:column>
									<p:column>
										<f:facet name="header"> Location </f:facet>
										<a href="#" onmouseup="setLocationValue(this.innerHTML);">#{report.location}</a>
									</p:column>

								</p:dataTable>
							</p:outputPanel>


						</td>
					</tr>

					<tr>
						<td align="left">
							<h:outputLabel value="Email Address " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText id="addEmailAddressText" maxlength="50"
										 value="#{rejectedUserSummaryBean.emailAddress}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
						<td align="left">
							<h:outputLabel value="Phone " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText id="phoneText" maxlength="50"
										 value="#{rejectedUserSummaryBean.phone}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="SAT Comment" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText id="satCommentText" maxlength="50"
										 value="#{rejectedUserSummaryBean.satComment}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
						<td align="left">
							<h:outputLabel value="SAT Job Role" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText id="satJobRoleText" maxlength="50"
										 value="#{rejectedUserSummaryBean.satJobRole}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
					</tr>

					<tr>
						<td align="left">
							<h:outputLabel value="SAT Status" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText id="satStatusText" maxlength="20" tabindex="0"
										 value="#{rejectedUserSummaryBean.satStatus}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
						<td align="left">
							<h:outputLabel value="Extract System" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:outputLabel value="#{rejectedUserSummaryBean.extrctSysName}"/>
						</td>
					</tr>

					<tr>
						<td align="left" colspan="4">
							<p:commandButton id="saveButton" value="Save"
											 styleClass="plain-button"
											 process="@form"
											 update="rejectedUserTable adduserModalAjaxPanel bodyForm:headerMessages"
											 action="#{rejectedUserSummaryBean.doAddNewUser}"/>
							<p:commandButton id="cancelButton" value="Cancel"
											 styleClass="plain-button"
											 action="#{rejectedUserSummaryBean.doCancelAddUser}"
											 process="@this" immediate="true"
											 update="addlastNameText addfirstNameText addmiddleNameText
											 		addEmailAddressText phoneText satCommentText satJobRoleText
											 		satStatusText userTypeListbox supervisorListbox StatusNameText
											 		departmentListbox divisionListbox bsnssSgmntListbox
											 		locationListbox jobTitleListbox directReportsTable
											 		bodyForm:headerMessages"
											 oncomplete="PF('addModalPanelWidget').hide()" />
						</td>
					</tr>
					</tbody>
				</table>
			</p:dialog>
		</p:outputPanel>
	</h:form>


	<!-- **************************    Search User ModelPanel     ***********************************   -->
	<h:form id="searchUserForm1">
		<p:outputPanel id="searchUserModalAjaxPanel">
			<p:dialog id="searchUserModalPanel" resizeable="true"
					  blockScroll="true"
					  styleClass="addUserModalPanelClass"
					  widgetVar="searchUserModalPanelWidget"
					  modal="true"
					  draggable="true"
					  height="500" width="750">

				<f:facet name="header">
					<h:outputLabel value="Edit Table"/>
				</f:facet>
				<f:facet name="controller">
					<p:outputPanel>
						<p:commandLink id="hidelink" style="float:left" value="close"
									   oncomplete="PF('searchUserModalPanelWidget').hide();"/>
					</p:outputPanel>
				</f:facet>
				<div>
					<div class="sectionHeaderSmall">
						Search User
					</div>
				</div>
				<p:dataTable id="searchUserTable"
							 value="#{rejectedUserSummaryBean.userList}" var="user"
							 rows="#{rejectedUserSummaryBean.popupDisplayAmount}"
							 styleClass="defaultTableHeader"
							 style="overflow-x:hidden; height: inherit;"
							 rowStyleClasses="oddRow, evenRow"
							 paginator="true"
							 paginatorPosition="bottom"
							 paginatorTemplate="#{rejectedUserSummaryBean.userListEmpty ? '{CurrentPageReport} {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink} {RowsPerPageDropdown}' : ''}"
							 currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
							 rowIndexVar="rowIndex"
							 rowKeyVar="rowKey">
					<p:column>
						<f:facet name="header">
							<p:commandLink value="Last Name"
										   action="#{rejectedUserSummaryBean.doUserSort}"
										   styleClass="headerSortLink"
										   update="searchUserTable"
										   process="@this">
								<f:param name="column" value="lastName"/>
							</p:commandLink>
						</f:facet>
						#{user.lastName}
					</p:column>
					<p:column>
						<f:facet name="header">
							<p:commandLink value="First Name"
										   action="#{rejectedUserSummaryBean.doUserSort}"
										   styleClass="headerSortLink"
										   update="searchUserTable"
										   process="@this">
								<f:param name="column" value="firstName"/>
							</p:commandLink>
						</f:facet>
						#{user.firstName}
					</p:column>
					<p:column>
						<f:facet name="header">
							<p:commandLink value="Key Id"
										   action="#{rejectedUserSummaryBean.doUserSort}"
										   styleClass="headerSortLink"
										   update="searchUserTable"
										   process="@this">
								<f:param name="column" value="keyId"/>
							</p:commandLink>
						</f:facet>
						<p:commandLink value="#{user.keyId}"
									   process="@this"
									   action="#{rejectedUserSummaryBean.showAddSearchedUserPanel}"
									   update="bodyForm:tabPanel:addAlternateIdForm:addAddAlternateIdModalAjaxPanel"
									   styleClass="headerSortLink"
									   oncomplete="PF('searchUserModalPanelWidget').hide(); PF('addAlternateIdModalVar').show();">
							<f:param name="column" value="#{user.keyId}"/>
						</p:commandLink>
					</p:column>
					<p:column>
						<f:facet name="header">
							<p:commandLink value="Division"
										   action="#{rejectedUserSummaryBean.doUserSort}"
										   styleClass="headerSortLink"
										   update="searchUserTable"
										   process="@this">
								<f:param name="column" value="division"/>
							</p:commandLink>
						</f:facet>
						#{user.division}
					</p:column>
					<p:column>
						<f:facet name="header">
							<p:commandLink value="Location"
										   action="#{rejectedUserSummaryBean.doUserSort}"
										   styleClass="headerSortLink"
										   update="searchUserTable"
										   process="@this">
								<f:param name="column" value="location"/>
							</p:commandLink>
						</f:facet>
						#{user.location}
					</p:column>
					<p:column>
						<f:facet name="header">
							<p:commandLink value="Status"
										   action="#{rejectedUserSummaryBean.doUserSort}"
										   styleClass="headerSortLink"
										   update="searchUserTable"
										   process="@this">
								<f:param name="column" value="status"/>
							</p:commandLink>
						</f:facet>
						#{user.status}
					</p:column>
					<p:column>
						<f:facet name="header">
							<p:commandLink value="Type"
										   action="#{rejectedUserSummaryBean.doUserSort}"
										   styleClass="headerSortLink"
										   update="searchUserTable"
										   process="@this">
								<f:param name="column" value="type"/>
							</p:commandLink>
						</f:facet>
						#{user.type}
					</p:column>
				</p:dataTable>
				<table width="100%">
					<tbody>
					<tr>
						<td align="left">
							<p:commandButton id="addButton" value="Add User" styleClass="plain-button"
											 action="#{rejectedUserSummaryBean.showAddUserPanelFromSearch}"
											 oncomplete="PF('searchUserModalPanelWidget').hide(); PF('addModalPanelWidget').show();"
											 ajax="true"
											 immediate="true"
											 process="@this"
											 update="bodyForm:tabPanel:addSearchedUserForm:addSearchedUserModalAjaxPanel" />
							<p:commandButton id="addAltButton" value="Add AlternateId" styleClass="plain-button"
											 immediate="true"
											 ajax="true"
											 oncomplete="PF('searchUserModalPanelWidget').hide(); PF('associatedUserSearchModalAjaxVar').show();"
											 process="@this"
											 update="bodyForm:tabPanel:addSearchedAssociatedUserForm:associatedUserSearchModalAjaxPanel"
											 action="#{rejectedUserSummaryBean.showAltUserSearchPanel}"/>
							<p:commandButton id="cancelButton" value="Cancel" styleClass="plain-button"
											 action="#{rejectedUserSummaryBean.doCancelSearchUser}"
											 oncomplete="PF('searchUserModalPanelWidget').hide();"
											 immediate="true"
											 process="@this"
											 update="searchUserTable bodyForm:headerMessages"/>
						</td>
					</tr>
					</tbody>
				</table>
			</p:dialog>
		</p:outputPanel>
	</h:form>


	<!-- **************************    Add Searched User ModelPanel     ***********************************   -->
	<h:form id="addSearchedUserForm">
		<p:outputPanel id="addSearchedUserModalAjaxPanel">
			<p:dialog id="addSearchedUserModalPanel"
					  blockScroll="true"
					  styleClass="addUserModalPanelClass"
					  widgetVar="addSearchedUserModalAjaxPanelVar"
					  modal="true"
					  resizeable="true"
					  draggable="true" height="550" width="500">

				<f:facet name="header">
					<h:outputLabel value="Edit Table"/>
				</f:facet>
				<f:facet name="controller">
					<p:outputPanel>
						<p:commandLink id="hidelinkOne" style="float: left" value="close"
									   oncomplete="PF('addSearchedUserModalAjaxPanelVar').hide();"/>
					</p:outputPanel>
				</f:facet>

				<!--   Messages                    -->
				<div>
					<h:messages id="AddSerchedUserMsgId"
								fatalClass="fatalMessage"
								errorClass="errorMessage"
								warnClass="warningMessage"
								infoClass="infoMessage"
								layout="table"/>
				</div>
				<div>
					<div class="sectionHeaderSmall">
						Add User
					</div>
				</div>
				<table width="100%">
					<tbody>
					<tr>
						<td align="left">
							<h:outputLabel value="KeyId " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:outputLabel value="#{rejectedUserSummaryBean.keyId}"/>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="First Name " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText maxlength="30"
										 value="#{rejectedUserSummaryBean.firstName}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="Middle Name  " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText maxlength="30"
										 value="#{rejectedUserSummaryBean.middleName}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="Last Name " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText maxlength="30"
										 value="#{rejectedUserSummaryBean.lastName}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="Email Address " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText maxlength="30"
										 value="#{rejectedUserSummaryBean.emailAddress}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
					</tr>
					<tr>
						<td align="left">

							<h:outputLabel value="User Type" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="UserTypeListbox"
											 value="#{rejectedUserSummaryBean.userType}">
								<f:selectItems
										value="#{rejectedUserSummaryBean.availableUserType}"/>
							</h:selectOneMenu>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="Supervisor " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="supervisorListbox"
											 value="#{rejectedUserSummaryBean.supervisorId}">
								<f:selectItems id="searchSupervisorSelectItems"
											   value="#{rejectedUserSummaryBean.availableSupervisors}"/>
								<p:ajax event="change"
										listener="#{rejectedUserSummaryBean.populateSupevisorData}"
										oncomplete="PF('addSearchedUserModalAjaxPanelVar').show();"
										process="@this"
										update="addSearchedUserModalAjaxPanel"/>
							</h:selectOneMenu>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="Department-CostCenter" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="DepartmentListbox"
											 value="#{rejectedUserSummaryBean.departmentId}">
								<f:selectItems
										value="#{rejectedUserSummaryBean.supevisorDepartments}"/>
							</h:selectOneMenu>
						</td>
					</tr>
					<tr>
						<td align="left">

							<h:outputLabel value="Division" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="DivisiontListbox"
											 value="#{rejectedUserSummaryBean.divisionId}">


								<f:selectItems
										value="#{rejectedUserSummaryBean.supevisorDivisions}"/>
							</h:selectOneMenu>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="Job Title " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="jobTitleListbox"
											 value="#{rejectedUserSummaryBean.jobTitle}" rendered="true">
								<f:selectItems
										value="#{rejectedUserSummaryBean.supevisorJobTitles}"/>
							</h:selectOneMenu>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="Location " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="locationListbox"
											 value="#{rejectedUserSummaryBean.location}" rendered="true">
								<f:selectItems
										value="#{rejectedUserSummaryBean.supevisorLocations}"/>
							</h:selectOneMenu>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="Business Segment "
										   styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:selectOneMenu id="bsnssSgmntListbox"
											 value="#{rejectedUserSummaryBean.bsnssSgmnt}"
											 rendered="true">
								<f:selectItems value="#{rejectedUserSummaryBean.supevisorBusinessSegments}"/>
							</h:selectOneMenu>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="Phone " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText maxlength="30"
										 value="#{rejectedUserSummaryBean.phone}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel styleClass="fieldLabel" id="selectStatusLabel">Status:
							</h:outputLabel>
						</td>
						<td align="left">
							<h:selectOneMenu id="StatusListbox"
											 value="#{rejectedUserSummaryBean.status}">
								<f:selectItems value="#{rejectedUserSummaryBean.availableStatus}"/>
							</h:selectOneMenu>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="Sat Status " styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText maxlength="20"
										 value="#{rejectedUserSummaryBean.satStatus}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="SAT Comment" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText id="satCommentText"
										 maxlength="50"
										 value="#{userSummaryBean.satComment}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel value="SAT Job Role" styleClass="fieldLabel"/>
						</td>
						<td align="left">
							<h:inputText id="satJobRoleText" maxlength="50"
										 value="#{userSummaryBean.satJobRole}"
										 onchange="this.value = this.value.toUpperCase();"/>
						</td>
					</tr>

					<tr>
						<td align="left">
							<p:commandButton id="saveButton"
											 value="Save"
											 styleClass="plain-button"
											 process="@this"
											 update="rejectedUserTable addSearchedUserModalAjaxPanel"
											 oncomplete="PF('addSearchedUserModalAjaxPanelVar').hide()"
											 action="#{rejectedUserSummaryBean.doAddUser}"/>
						</td>
						<td align="left">
							<p:commandButton value="Cancel"
											 styleClass="plain-button"
											 immediate="true"
											 process="@this"
											 update="addSearchedUserModalAjaxPanel"
											 oncomplete="PF('addSearchedUserModalAjaxPanelVar').hide()"
											 action="#{rejectedUserSummaryBean.doCancelAddSearhedUser}"/>
						</td>
					</tr>
					</tbody>
				</table>
			</p:dialog>
		</p:outputPanel>
	</h:form>


	<!-- **************************   April 8     ***********************************   -->


	<!-- **************************   Search associated User ModelPanel     ***********************************   -->
	<h:form id="addSearchedAssociatedUserForm">
		<p:outputPanel id="associatedUserSearchModalAjaxPanel">
			<p:dialog id="associatedUserSearchModalPanel"
					  styleClass="addUserModalPanelClass"
					  blockScroll="true"
					  widgetVar="associatedUserSearchModalAjaxVar"
					  resizeable="true"
					  modal="true"
					  draggable="true"
					  height="450" width="800">

				<f:facet name="header">
					<h:outputLabel value="Search User"/>
				</f:facet>
				<f:facet name="controller">
					<p:outputPanel>
						<p:commandLink id="hidelinkTwo" style="float: left" value="close"
									   oncomplete="PF('associatedUserSearchModalAjaxVar').hide();"/>
					</p:outputPanel>
				</f:facet>

				<div>
					<h:messages id="SerchedAssociatedUserMsgId"
								fatalClass="fatalMessage"
								errorClass="errorMessage"
								warnClass="warningMessage"
								infoClass="infoMessage"
								layout="table"/>
				</div>

				<div>
					<div class="sectionHeaderSmall">
						Filter By name or User Id
					</div>
				</div>
				<table width="100%">
					<tbody>
					<tr>
						<td align="left">
							<h:outputLabel id="firstNameLabel" styleClass="filterCls">First Name:
							</h:outputLabel>
						</td>
						<td align="left">
							<h:inputText id="firstNameText" maxlength="100"
										 value="#{rejectedUserSummaryBean.firstName}"
										 onchange="this.value = this.value.toUpperCase();">
							</h:inputText>
						</td>
						<td align="left">
							<h:outputLabel id="LastNameLabel" styleClass="filterCls">Last Name:
							</h:outputLabel>
						</td>
						<td align="left">
							<h:inputText id="lastNameText" maxlength="100"
										 value="#{rejectedUserSummaryBean.lastName}"
										 onchange="this.value = this.value.toUpperCase();">
							</h:inputText>
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel id="keyIdLabel" styleClass="filterCls">Key Id:
							</h:outputLabel>
						</td>
						<td align="left">
							<h:inputText id="keyIdText" maxlength="100"
										 value="#{rejectedUserSummaryBean.keyId}"
										 onchange="this.value = this.value.toUpperCase();">
							</h:inputText>
						</td>
						<td align="left">
							<h:outputLabel id="SupervisorLabel" styleClass="filterCls">Supervisor:
							</h:outputLabel>
						</td>
						<td align="left">
							<h:selectOneMenu id="supervisorListbox"
											 value="#{rejectedUserSummaryBean.supervisorId}">
								<f:selectItems id="searchSupervisorSelectItems"
											   value="#{rejectedUserSummaryBean.availableSupervisors}"/>
							</h:selectOneMenu>
						</td>
					</tr>

					<tr>
						<td colspan="100%">
							<hr width="100%"/>
						</td>
					</tr>
					<tr>
						<td align="left" class="sectionHeaderSmall" colspan="100%">
							Filter User By
						</td>
					</tr>
					<tr>
						<td colspan="100%" style="color: #000000; font-size: 11px; font-family: Arial, Verdana, sans-serif;">
							If nothing is selected, all values are included. User
							ctrl+click to select more than one value.
						</td>
					</tr>
					<tr>
						<td align="left">
							<h:outputLabel id="StatusLabel" styleClass="filterCls">Status</h:outputLabel>
						</td>
						<td>
							<h:selectManyListbox id="StatusListbox"
									value="#{rejectedUserSummaryBean.searchStatuses}"
									converter="javax.faces.Long">
								<f:selectItems
										value="#{rejectedUserSummaryBean.multiSelectUserStatusList}"/>
							</h:selectManyListbox>
						</td>
						<td align="left">
							<h:outputLabel id="selectDepartmentLabel" styleClass="filterCls">Department:
							</h:outputLabel>
						</td>
						<td align="left">
							<h:selectManyListbox id="DepartmentListbox"
												 value="#{rejectedUserSummaryBean.searchDepartments}" size="3"
												 converter="javax.faces.Long">
								<f:selectItems
										value="#{rejectedUserSummaryBean.multiSelectDepartmentList}"/>
							</h:selectManyListbox>
						</td>
					</tr>
					<tr>
						<td align="left">

							<h:outputLabel id="userTypeLabel" styleClass="filterCls">User Type:
							</h:outputLabel>
						</td>
						<td align="left">
							<h:selectManyListbox id="UserTypeListbox"
									value="#{rejectedUserSummaryBean.searchUserTypes}" size="3"
									converter="javax.faces.Long">
								<f:selectItems
										value="#{rejectedUserSummaryBean.multiSelectUserTypeList}"/>
							</h:selectManyListbox>
						</td>
						<td align="left">
							<h:outputLabel id="selectDivisionLabel" styleClass="filterCls">Division:
							</h:outputLabel>
						</td>
						<td align="left">
							<h:selectManyListbox id="DivisiontListbox"
												 value="#{rejectedUserSummaryBean.searchDivisions}" size="3"
												 converter="javax.faces.Long">
								<f:selectItems
										value="#{rejectedUserSummaryBean.multiSelectDivisionList}"/>
							</h:selectManyListbox>
						</td>
						<td align="left"></td>
					</tr>
					<tr>
						<td colspan="100%">
							<hr width="100%"/>
						</td>
					</tr>
					<tr>
						<td>
							<p:commandButton id="deleteButton"
											 styleClass="plain-button"
											 value="Continue"
											 action="#{rejectedUserSummaryBean.doSearchAssociatedUser}"
											 process="@form"
											 update="associatedUserSearchModalAjaxPanel bodyForm:tabPanel:associatedUserForm:searchAssociatedUserTable bodyForm:headerMessages"/>
						</td>
						<td>
							<p:commandButton id="cancelDeleteButton"
											 value="Cancel"
											 styleClass="plain-button"
											 oncomplete="PF('associatedUserSearchModalAjaxVar').hide();"
											 process="@none"
											 update="associatedUserSearchModalAjaxPanel bodyForm:headerMessages"
											 ajax="true"
											 immediate="true"
											 action="#{rejectedUserSummaryBean.doCancelSearchAssociatedUser}"/>
						</td>
					</tr>
					</tbody>
				</table>
			</p:dialog>
		</p:outputPanel>
	</h:form>

	<!-- **************************    Searched Associated User ModelPanel     ***********************************   -->
	<h:form id="associatedUserForm">
		<p:outputPanel id="searchAssociatedUserModalAjaxPanel">
			<p:dialog id="searchAssociatedUserModalPanel"
					  blockScroll="true"
					  styleClass="addUserModalPanelClass"
					  widgetVar="searchAssociatedUserModalVar"
					  modal="true"
					  resizeable="true" draggable="true" height="500" width="800">
				<f:facet name="header">
					<h:outputLabel value="Search User"/>
				</f:facet>
				<f:facet name="controller">
					<p:outputPanel>
						<p:commandLink id="hidelinkThree" style="float: left" value="close"
									   oncomplete="PF('searchAssociatedUserModalVar').hide();"/>
					</p:outputPanel>
				</f:facet>
				<p:dataTable id="searchAssociatedUserTable"
							 value="#{rejectedUserSummaryBean.userList}" var="user"
							 paginator="true"
							 paginatorTemplate="#{rejectedUserSummaryBean.userListEmpty ? '{CurrentPageReport}  {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink} {RowsPerPageDropdown}' : ''}"
							 currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
							 paginatorPosition="bottom"
							 rowIndexVar="rowIndex"
							 rowKey="#{user.keyId}"
							 rows="10"
							 styleClass="defaultTableHeader"
							 rowStyleClasses="oddRow, evenRow"
							 style="width:100%">
					<p:column>
						<f:facet name="header">
							<p:commandLink value="Last Name"
										   action="#{rejectedUserSummaryBean.doUserSort}"
										   styleClass="headerSortLink"
										   update="searchAssociatedUserTable"
										   process="@this">
								<f:param name="column" value="lastName"/>
							</p:commandLink>
						</f:facet>
						#{user.lastName}
					</p:column>
					<p:column>
						<f:facet name="header">
							<p:commandLink value="First Name"
										   action="#{rejectedUserSummaryBean.doUserSort}"
										   styleClass="headerSortLink"
										   update="searchAssociatedUserTable"
										   process="@this">
								<f:param name="column" value="firstName"/>
							</p:commandLink>
						</f:facet>
						#{user.firstName}
					</p:column>
					<p:column>
						<f:facet name="header">
							<p:commandLink value="Key Id"
										   action="#{rejectedUserSummaryBean.doUserSort}"
										   styleClass="headerSortLink"
										   update="searchAssociatedUserTable"
										   process="@this">
								<f:param name="column" value="keyId"/>
							</p:commandLink>
						</f:facet>
						<p:commandLink value="#{user.keyId}"
									   action="#{rejectedUserSummaryBean.showAddAlternateIdPanel}"
									   process="@this"
									   oncomplete="PF('searchAssociatedUserModalVar').hide(); PF('addAlternateIdModalVar').show();"
									   update="bodyForm:tabPanel:addAlternateIdForm:addAddAlternateIdModalAjaxPanel"
									   styleClass="headerSortLink">
							<f:param name="column" value="#{user.keyId}"/>
						</p:commandLink>
					</p:column>


					<p:column>
						<f:facet name="header">
							<p:commandLink value="Division"
										   action="#{rejectedUserSummaryBean.doUserSort}"
										   styleClass="headerSortLink"
										   update="searchAssociatedUserTable"
										   process="@this">
								<f:param name="column" value="division"/>
							</p:commandLink>
						</f:facet>
						#{user.division}
					</p:column>
					<p:column>
						<f:facet name="header">
							<p:commandLink value="Location"
										   action="#{rejectedUserBean.doUserSort}"
										   styleClass="headerSortLink"
										   update="searchAssociatedUserTable"
										   process="@this">
								<f:param name="column" value="location"/>
							</p:commandLink>
						</f:facet>
						#{user.location}
					</p:column>
					<p:column>
						<f:facet name="header">
							<p:commandLink value="Status"
										   action="#{rejectedUserSummaryBean.doUserSort}"
										   styleClass="headerSortLink"
										   update="searchAssociatedUserTable"
										   process="@this">
								<f:param name="column" value="status"/>
							</p:commandLink>
						</f:facet>
						#{user.status}
					</p:column>
					<p:column>
						<f:facet name="header">
							<p:commandLink value="Type"
										   action="#{rejectedUserSummaryBean.doUserSort}"
										   styleClass="headerSortLink"
										   update="searchAssociatedUserTable"
										   process="@this">
								<f:param name="column" value="type"/>
							</p:commandLink>
						</f:facet>
						#{user.type}
					</p:column>
				</p:dataTable>

				<table style="width:100%">
					<tbody>
					<tr>

						<td align="left">
							<p:commandButton id="cancelButton"
											 value="Cancel"
											 styleClass="plain-button"
											 update="@this bodyForm:headerMessages"
											 process="@this"
											 oncomplete="PF('searchAssociatedUserModalVar').hide();"
											 immediate="true"
											 action="#{rejectedUserSummaryBean.doCancelAssociatedUserSearch}"/>
						</td>
					</tr>
					</tbody>
				</table>
			</p:dialog>
		</p:outputPanel>
	</h:form>

	<!-- **************************    Add Alternate Id ModelPanel     ***********************************   -->

	<h:form id="addAlternateIdForm">
		<p:outputPanel id="addAddAlternateIdModalAjaxPanel">
			<p:dialog id="AddAlternateIdPanel" resizeable="true"
					  styleClass="addUserModalPanelClass"
					  widgetVar="addAlternateIdModalVar"
					  modal="true" blockScroll="true"
					  draggable="true"
					  height="420" width="750">

				<f:facet name="header">
					<h:outputLabel value="Add Alternate Id"/>
				</f:facet>

				<!--   Messages                    -->
				<div>
					<h:messages id="addAlternateIdMsg"
								fatalClass="fatalMessage"
								errorClass="errorMessage"
								warnClass="warningMessage"
								infoClass="infoMessage"
								layout="table"/>
				</div>
				<div style="display: block;">
					<table align="left">
						<tbody>
						<tr>
							<td align="left">
								<h:outputLabel value="Key Id " styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.keyId}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="First Name " styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.firstName}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Middle Name" styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.middleName}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Last Name " styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.lastName}" styleClass="filterClass" />
							</td>
						</tr>

						<tr>
							<td align="left">
								<h:outputLabel value="User Type " styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.userTypeDescription}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Department " styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.departmentName}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Division " styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.divisionName}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Location " styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.location}" styleClass="filterClass" />
							</td>
						</tr>

						<tr>
							<td align="left">
								<h:outputLabel value="JobTitle " styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.jobTitle}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Supervisor " styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.supervisorName}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Cost Center " styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.costCenter}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Phone " styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.phone}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Status " styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.userStatusDescription}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Sat Status " styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.satStatus}" styleClass="filterClass" />
							</td>
						</tr>

						<tr>
							<td align="left">
								<p:commandButton id="saveButton" value="Save"
												 styleClass="plain-button"
												 process="@form"
												 ajax="true"
												 update="addAlternateIdForm bodyForm:headerMessages bodyForm:tabPanel:rejectedUserTable"
												 action="#{rejectedUserSummaryBean.doAddAlternateId}">
								</p:commandButton>
							</td>
							<td align="left">
								<p:commandButton id="cancelButton" value="Cancel"
												 styleClass="plain-button"
												 ajax="true"
												 process="@this"
												 immediate="true"
												 update="addAlternateIdForm bodyForm:headerMessages"
												 oncomplete="PF('addAlternateIdModalVar').hide();"
												 action="#{rejectedUserSummaryBean.doCancelAddAlternateId}"/>
							</td>
						</tr>
						</tbody>
					</table>

					<table align="left">
						<tr>
							<td align="left" colspan="2">
								<h:outputLabel value="New Alternate Id "
											   styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:selectOneMenu id="alternateIdListbox"
												 value="#{rejectedUserSummaryBean.internalIdentifierTypeCD}">
									<f:selectItems
											value="#{rejectedUserSummaryBean.availableInternalIdentifierType}"/>

								</h:selectOneMenu>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.newAltId}" styleClass="filterClass" />
							</td>

						</tr>
						<tr>
							<td align="left" colspan="2">
								<h:outputLabel value="Existing Alternate Id(s) "
											   styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="MF ID" styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.satMFId}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="CF ID" styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.satCFId}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="GF ID" styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.satGFId}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="LCS ID" styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.satLCSId}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="FDMSId" styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.satFDMSId}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="SiteMinderId" styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.satSiteMinderId}" styleClass="filterClass" />
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Alternate Id1" styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.satAltId1}" styleClass="filterClass"/>
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Alternate Id2" styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.satAltId2}" styleClass="filterClass"/>
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Alternate Id3" styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.satAltId3}" styleClass="filterClass"/>
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel value="Email Address" styleClass="fieldLabel filterCls" style="font-weight: bold"/>
							</td>
							<td align="left">
								<h:outputText value="#{rejectedUserSummaryBean.emailAddress}" styleClass="filterClass"/>
							</td>
						</tr>
					</table>
				</div>
			</p:dialog>
		</p:outputPanel>
		<script type="text/javascript">
				$(document).ready(function() {
					$('#bodyForm\\:tabPanel\\:rejectedUserTable .ui-paginator-rpp-label').text('items per page').addClass('application-table-class');
				});
				function setrejectedUserTablePaginator() {
					$('#bodyForm\\:tabPanel\\:rejectedUserTable .ui-paginator-rpp-label').text('items per page').addClass('application-table-class');
				}
		</script>
	</h:form>
</f:view>


package com.assurant.inc.sox.ar.client.bean.admin;

import com.assurant.inc.sox.ar.client.admin.ui.RejectedUserUI;
import com.assurant.inc.sox.ar.client.admin.ui.UserUI;
import com.assurant.inc.sox.ar.client.bean.SessionDataBean;
import com.assurant.inc.sox.ar.client.bean.util.CommonPageActionHelper;
import com.assurant.inc.sox.ar.client.bean.util.JSFUtils;
import com.assurant.inc.sox.ar.service.IExtractSystemService;
import com.assurant.inc.sox.ar.service.IRejectedUserService;
import com.assurant.inc.sox.ar.service.IUserService;
import com.assurant.inc.sox.domain.ar.*;
import com.assurant.inc.sox.domain.luad.User;


import org.primefaces.PrimeFaces;
import org.primefaces.event.data.PageEvent;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

import javax.faces.application.FacesMessage;
import javax.faces.context.FacesContext;
import javax.faces.model.SelectItem;
import java.util.*;

@Component("rejectedUserSummaryBean")
@Scope("session")
public class RejectedUserSummaryBean {

	private static final Logger logger = LoggerFactory.getLogger(RejectedUserSummaryBean.class);
	private List<RejectedUserUI> rejectedUserList;
	@Autowired
	private IRejectedUserService rejectedUserService;
	@Autowired
	private IExtractSystemService extractSystemService;
	@Autowired
	private IUserService userService;

	private String displayAmount = "10";
	private String popupDisplayAmount = "10";
	private String oldSortColumn;
	@Autowired
	@Qualifier("sessionDataBean")
	private SessionDataBean sessionDataBean;

	private boolean renderAddUserModalPanel;
	private boolean renderSearchUserModalPanel;
	private boolean renderAddSearchedUserPanel;
	private boolean renderAssociatedUserSearchModalPanel;
	private boolean renderSearchedAssociatedUserModalPanel;
	private boolean renderAddAlternateIdModalPanel;
	private Long rejectedUserId;
	private Long userId;
	private String lastName;
	private String firstName;
	private String middleName;
	private Long departmentId;
	private String departmentName;
	private Long divisionId;
	private String divisionName;
	private String keyId;

	private Long supervisorId;
	private String supervisorName;
	private Long status;
	private String userStatusDescription;
	private String existingAlternateIds;

	private Long userType;
	private String userTypeDescription;
	private String costCenter;
	private String jobTitle = "";
	private String emailAddress;
	private String phone;
	private String satStatus;
	private String location = "";
	private String satAltId1;
	private String satAltId2;
	private String satAltId3;
	private String newAltId;

	private String satMFId;
	private String satGFId;
	private String satCFId;
	private String satLCSId;
	private String satFDMSId;
	private String satSiteMinderId;
	private String satJobRole;
	private String satComment;
	private String bsnssSgmnt;
	private String lookUpAltId;
	private String srcUserId;
	private String searchSrcUserId;
	private Long searchExtrctSysId;
	private boolean isInitialLoad = true;

	public String getSearchSrcUserId() {
		return searchSrcUserId;
	}

	public Long getSearchExtrctSysId() {
		return searchExtrctSysId;
	}

	public void setSearchExtrctSysId(Long searchExtrctSysId) {
		this.searchExtrctSysId = searchExtrctSysId;
	}

	public void setSearchSrcUserId(String searchSrcUserId) {
		this.searchSrcUserId = searchSrcUserId;
	}

	private List<SelectItem> availableExtractSystem;
	private List<SelectItem> availablecreatedDateCriteria;
	private String selectedDateCriteria;
	private Long extrctSysId;
	private Date extractSysDate;
	private String extrctSysName;
	private Date createdDateCriteria;

	private List<UserUI> userList;

	private List<SelectItem> supevisorDepartments = new ArrayList<SelectItem>();
	private List<SelectItem> supevisorDivisions = new ArrayList<SelectItem>();
	private List<SelectItem> supevisorBusinessSegments = new ArrayList<SelectItem>();
	private List<SelectItem> supevisorJobTitles = new ArrayList<SelectItem>();
	private List<SelectItem> supevisorLocations = new ArrayList<SelectItem>();
	private List<UserUI> supervisorDirectReports = new ArrayList<UserUI>();


	public List<UserUI> getSupervisorDirectReports() {
		if (this.supervisorDirectReports == null || this.supervisorId == null)
			this.supervisorDirectReports = new ArrayList<UserUI>();
		return this.supervisorDirectReports;
	}

	public void setSupervisorDirectReports(List<UserUI> supervisorDirectReports) {
		this.supervisorDirectReports = supervisorDirectReports;
	}

	private List<SelectItem> availableDepartment;
	private List<SelectItem> availableDivision;
	private List<SelectItem> availableStatus;
	private List<SelectItem> availableUserType;
	private List<SelectItem> availableBusinessSeg;
	private List<SelectItem> availableCostCenter;
	//private List<SelectItem> availableFilters;


	//private List<Long> userTypes = new ArrayList<Long>();
	//private List<Long> departments = new ArrayList<Long>();
	//private List<Long> divisions = new ArrayList<Long>();
	//private List<Long> userStatuses = new ArrayList<Long>();
	private List searchDepartments;
	private List searchDivisions;
	private List searchStatuses;
	private List searchUserTypes;

	public List<String> getSearchStatuses() {
		return searchStatuses;
	}

	public void setSearchStatuses(List searchStatuses) {
		this.searchStatuses = searchStatuses;
	}

	public List getSearchUserTypes() {
		return searchUserTypes;
	}

	public void setSearchUserTypes(List searchUserTypes) {
		this.searchUserTypes = searchUserTypes;
	}

	public List getSearchDepartments() {
		return searchDepartments;
	}

	public void setSearchDepartments(List searchDepartments) {
		this.searchDepartments = searchDepartments;
	}

	public List getSearchDivisions() {
		return searchDivisions;
	}

	public void setSearchDivisions(List searchDivisions) {
		this.searchDivisions = searchDivisions;
	}

	private Long internalIdentifierTypes;
	private String internalIdentifierTypeCD;

	public String getInternalIdentifierTypeCD() {
		return internalIdentifierTypeCD;
	}

	public void setInternalIdentifierTypeCD(String internalIdentifierTypeCD) {
		this.internalIdentifierTypeCD = internalIdentifierTypeCD;
	}

	private boolean activeChecked;
	private boolean leaveChecked;
	private boolean inactiveChecked;

	public List<RejectedUserUI> getRejectedUserList() {
		if (rejectedUserList == null) {
			if(this.isInitialLoad){
				this.isInitialLoad = false;
				refreshList(false);
			}
			else
				refreshList(true);
		}

		return rejectedUserList;
	}

	public String getDisplayAmount() {
		return displayAmount;
	}

	public void setDisplayAmount(String displayAmount) {
		this.displayAmount = displayAmount;
	}

	public String getPopupDisplayAmount() {
		return popupDisplayAmount;
	}

	public void setPopupDisplayAmount(String popupDisplayAmount) {
		this.popupDisplayAmount = popupDisplayAmount;
	}

	public void setRejectedUserList(List<RejectedUserUI> rejectedUserList) {
		this.rejectedUserList = rejectedUserList;
	}

	public IRejectedUserService getRejectedUserService() {
		return rejectedUserService;
	}

	public void setRejectedUserService(IRejectedUserService rejectedUserService) {
		this.rejectedUserService = rejectedUserService;
	}

	public boolean isRenderAddUserModalPanel() {
		return renderAddUserModalPanel;
	}

	public void setRenderAddUserModalPanel(boolean renderAddUserModalPanel) {
		this.renderAddUserModalPanel = renderAddUserModalPanel;
	}

	public boolean isRenderSearchUserModalPanel() {
		return renderSearchUserModalPanel;
	}

	public void setRenderSearchUserModalPanel(boolean renderSearchUserModalPanel) {
		this.renderSearchUserModalPanel = renderSearchUserModalPanel;
	}

	public boolean isRenderAddSearchedUserPanel() {
		return renderAddSearchedUserPanel;
	}

	public void setRenderAddSearchedUserPanel(boolean renderAddSearchedUserPanel) {
		this.renderAddSearchedUserPanel = renderAddSearchedUserPanel;
	}

	public boolean isRenderAssociatedUserSearchModalPanel() {
		return renderAssociatedUserSearchModalPanel;
	}

	public void setRenderAssociatedUserSearchModalPanel(
			boolean renderAssociatedUserSeachModalPanel) {
		this.renderAssociatedUserSearchModalPanel = renderAssociatedUserSeachModalPanel;
	}

	public boolean isRenderSearchedAssociatedUserModalPanel() {
		return renderSearchedAssociatedUserModalPanel;
	}

	public void setRenderSearchedAssociatedUserModalPanel(
			boolean renderSearchedAssociatedUserModalPanel) {
		this.renderSearchedAssociatedUserModalPanel = renderSearchedAssociatedUserModalPanel;
	}

	public boolean isRenderAddAlternateIdModalPanel() {
		return renderAddAlternateIdModalPanel;
	}

	public void setRenderAddAlternateIdModalPanel(
			boolean renderAddAlternateIdModalPanel) {
		this.renderAddAlternateIdModalPanel = renderAddAlternateIdModalPanel;
	}

	public Long getRejectedUserId() {
		return this.rejectedUserId;
	}

	public void setRejectedUserId(Long rejectedUserId) {
		this.rejectedUserId = rejectedUserId;
	}

	public Long getUserId() {
		return userId;
	}

	public void setUserId(Long userId) {
		this.userId = userId;
	}

	public String getEmailAddress() {
		return emailAddress;
	}

	public void setEmailAddress(String emailAddress) {
		this.emailAddress = emailAddress;
	}

	public List<SelectItem> getSupevisorDepartments() {

		if (supevisorDepartments == null) {
			this.supevisorDepartments = new ArrayList<SelectItem>();
			this.supevisorDepartments.add(new SelectItem(0, "Not Available"));

		} else if (supevisorDepartments.isEmpty()) {
			this.supevisorDepartments = new ArrayList<SelectItem>();
			this.supevisorDepartments.add(new SelectItem(0, "Not Available"));
		}

		return supevisorDepartments;
	}

	public List<SelectItem> getSupevisorDivisions() {

		if (supevisorDivisions == null) {
			supevisorDivisions = new ArrayList<SelectItem>();
			this.supevisorDivisions.add(new SelectItem(0, "Not Available"));
		} else if (supevisorDivisions.isEmpty()) {
			supevisorDivisions = new ArrayList<SelectItem>();
			this.supevisorDivisions.add(new SelectItem(0, "Not Available"));
		}

		return supevisorDivisions;
	}
	/*
        public List<SelectItem> getSupevisorCostCenters() {

            if (supevisorCostCenters == null) {
                supevisorCostCenters = new ArrayList<SelectItem>();
                this.supevisorCostCenters.add(new SelectItem(0, "Not Available"));
            } else if (supevisorCostCenters.isEmpty()) {
                supevisorCostCenters = new ArrayList<SelectItem>();
                this.supevisorCostCenters.add(new SelectItem(0, "Not Available"));
            }

            return supevisorCostCenters;
        }
    */
	public List<SelectItem> getSupevisorBusinessSegments() {

		if (supevisorBusinessSegments == null) {
			supevisorBusinessSegments = new ArrayList<SelectItem>();
			this.supevisorBusinessSegments.add(new SelectItem(0,
					"Not Available"));
		} else if (supevisorBusinessSegments.isEmpty()) {
			supevisorBusinessSegments = new ArrayList<SelectItem>();
			this.supevisorBusinessSegments.add(new SelectItem(0,
					"Not Available"));
		}

		return supevisorBusinessSegments;
	}

	public List<SelectItem> getSupevisorJobTitles() {

		if (supevisorJobTitles == null) {
			this.supevisorJobTitles = new ArrayList<SelectItem>();
			this.supevisorJobTitles.add(new SelectItem(0, "Not Available"));
		} else if (supevisorJobTitles.isEmpty()) {
			this.supevisorJobTitles = new ArrayList<SelectItem>();
			this.supevisorJobTitles.add(new SelectItem(0, "Not Available"));
		}
		return supevisorJobTitles;
	}

	public List<SelectItem> getSupevisorLocations() {

		if (supevisorLocations == null) {
			supevisorLocations = new ArrayList<SelectItem>();
			supevisorLocations.add(new SelectItem(0, "Not Available"));
		} else if (supevisorLocations.isEmpty()) {
			supevisorLocations = new ArrayList<SelectItem>();
			supevisorLocations.add(new SelectItem(0, "Not Available"));
		}

		return supevisorLocations;
	}

	// ******* Refresh the List RESET button *******
	public void refreshList(boolean resort) {
		oldSortColumn = "";

		if (this.rejectedUserList == null) {
			System.out.println("Rejected user summary bean in If condition, 405" + rejectedUserList );
			resort = false;
		}



		logger.debug("******************** Refresh List on reject user summary *****************************");
		if (searchExtrctSysId != null && searchExtrctSysId < 0) {
			searchExtrctSysId = null;
		}

		System.out.println("searchSrcUserId : "+this.searchSrcUserId);
		System.out.println("searchExtrctSysId : "+this.searchExtrctSysId);
		System.out.println("selectedDateCriteria : "+selectedDateCriteria);
		System.out.println("createdDateCriteria : "+createdDateCriteria);
		System.out.println("************************************************************");

		this.rejectedUserList = this.rejectedUserService.findByValue(this.searchSrcUserId,
				this.searchExtrctSysId, selectedDateCriteria, createdDateCriteria);

		if (resort)
			this.doSort();
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public String getFirstName() {
		return firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getMiddleName() {
		return middleName;
	}

	public void setMiddleName(String middleName) {
		this.middleName = middleName;
	}

	public Long getDepartmentId() {
		return departmentId;
	}

	public void setDepartmentId(Long departmentId) {
		this.departmentId = departmentId;
	}

	public String getDepartmentName() {
		return departmentName;
	}

	public Long getDivisionId() {
		return divisionId;
	}

	public void setDivisionId(Long divisionId) {
		this.divisionId = divisionId;
	}

	public String getDivisionName() {
		return divisionName;
	}

	public String getKeyId() {
		return keyId;
	}

	public void setKeyId(String keyId) {
		this.keyId = keyId;
	}

	public Long getSupervisorId() {
		return supervisorId;
	}

	public void setSupervisorId(Long supervisorId) {
		this.supervisorId = supervisorId;
	}

	public String getSupervisorName() {
		return supervisorName;
	}

	public Long getStatus() {
		return status;
	}

	public void setStatus(Long status) {
		this.status = status;
	}

	public String getUserStatusDescription() {
		return this.userStatusDescription;
	}

	public String getExistingAlternateIds() {
		return existingAlternateIds;
	}

	public Long getUserType() {
		return userType;
	}

	public void setUserType(Long userType) {
		this.userType = userType;
	}

	public String getUserTypeDescription() {
		return userTypeDescription;
	}

	public String getCostCenter() {
		return costCenter;
	}

	public void setCostCenter(String costCenter) {
		this.costCenter = costCenter;
	}

	public String getJobTitle() {
		return jobTitle;
	}

	public void setJobTitle(String jobTitle) {
		this.jobTitle = jobTitle;
	}

	public String getPhone() {
		return phone;
	}

	public void setPhone(String phone) {
		this.phone = phone;
	}

	public String getSatStatus() {
		return satStatus;
	}

	public void setSatStatus(String satStatus) {
		this.satStatus = satStatus;
	}

	public String getLocation() {
		return location;
	}

	public void setLocation(String location) {
		this.location = location;
	}

	public String getSatAltId1() {
		return satAltId1;
	}

	public void setSatAltId1(String satAltId1) {
		this.satAltId1 = satAltId1;
	}

	public String getSatAltId2() {
		return satAltId2;
	}

	public void setSatAltId2(String satAltId2) {
		this.satAltId2 = satAltId2;
	}

	public String getSatAltId3() {
		return satAltId3;
	}

	public void setSatAltId3(String satAltId3) {
		this.satAltId3 = satAltId3;
	}

	public String getNewAltId() {
		return newAltId;
	}

	public void setNewAltId(String newAltId) {
		this.newAltId = newAltId;
	}

	public String getSatMFId() {
		return satMFId;
	}

	public void setSatMFId(String satMFId) {
		this.satMFId = satMFId;
	}

	public String getSatGFId() {
		return satGFId;
	}

	public void setSatGFId(String satGFId) {
		this.satGFId = satGFId;
	}

	public String getSatCFId() {
		return satCFId;
	}

	public void setSatCFId(String satCFId) {
		this.satCFId = satCFId;
	}

	public String getSatLCSId() {
		return satLCSId;
	}

	public void setSatLCSId(String satLCSId) {
		this.satLCSId = satLCSId;
	}

	public String getSatFDMSId() {
		return satFDMSId;
	}

	public void setSatFDMSId(String satFDMSId) {
		this.satFDMSId = satFDMSId;
	}

	public String getSatSiteMinderId() {
		return satSiteMinderId;
	}

	public void setSatSiteMinderId(String satSiteMinderId) {
		this.satSiteMinderId = satSiteMinderId;
	}

	public String getSatJobRole() {
		return satJobRole;
	}

	public void setSatJobRole(String satJobRole) {
		this.satJobRole = satJobRole;
	}

	public String getSatComment() {
		return satComment;
	}

	public void setSatComment(String satComment) {
		this.satComment = satComment;
	}

	public String getBsnssSgmnt() {
		return bsnssSgmnt;
	}

	public void setBsnssSgmnt(String bsnssSgmnt) {
		this.bsnssSgmnt = bsnssSgmnt;
	}

	public String getLookUpAltId() {
		return lookUpAltId;
	}

	public void setLookUpAltId(String lookUpAltId) {
		this.lookUpAltId = lookUpAltId;
	}

	public String getSrcUserId() {
		return srcUserId;
	}

	public void setSrcUserId(String srcUserId) {
		this.srcUserId = srcUserId;
	}

	public List<SelectItem> getAvailableExtractSystem() {
		List<ExtractSystem> extractSystemList = this.rejectedUserService.retrieveExtractSystems();
		//Sort
		Collections.sort(extractSystemList, new Comparator<ExtractSystem>() {
			public int compare(ExtractSystem sys1, ExtractSystem sys2) {
				return sys1.getExtrctSysId().compareTo(sys2.getExtrctSysId());
			}
		});
		availableExtractSystem = new ArrayList<SelectItem>();
		availableExtractSystem.add(new SelectItem("-1","Please select"));
		for (ExtractSystem es : extractSystemList) {
			if (es.getExtrctSysNm()!=null)
				availableExtractSystem.add(new SelectItem(es.getExtrctSysId(),
						es.getExtrctSysId() +" - "+es.getExtrctSysNm()));
		}
		return availableExtractSystem;
	}

	public List<SelectItem> getAvailablecreatedDateCriteria() {
		availablecreatedDateCriteria = new ArrayList<SelectItem>();
		availablecreatedDateCriteria.add(new SelectItem("lt", "Before"));
		availablecreatedDateCriteria.add(new SelectItem("gt", "After"));
		availablecreatedDateCriteria.add(new SelectItem("eq", "Equal"));
		return availablecreatedDateCriteria;
	}

	public Long getExtrctSysId() {
		return this.extrctSysId;
	}

	public void setExtrctSysId(Long extrctSysId) {
		this.extrctSysId = extrctSysId;
	}

	public Date getExtractSysDate() {
		return extractSysDate;
	}

	public void setExtractSysDate(Date extractSysDate) {
		this.extractSysDate = extractSysDate;
	}

	public String getExtrctSysName() {
		return this.extrctSysName;
	}

	public void setExtrctSysName(String extrctSysName) {
		this.extrctSysName = extrctSysName;
	}

	public Date getCreatedDateCriteria() {
		return createdDateCriteria;
	}

	public void setCreatedDateCriteria(Date createdDateCriteria) {
		this.createdDateCriteria = createdDateCriteria;
	}

	public String getSelectedDateCriteria() {
		return selectedDateCriteria;
	}

	public void setSelectedDateCriteria(String selectedDateCriteria) {
		this.selectedDateCriteria = selectedDateCriteria;
	}

	public List<UserUI> getUserList() {
		return userList;
	}

	public boolean isUserListEmpty() {
		return !(userList == null || userList.isEmpty());
	}

	public void setUserList(ArrayList<UserUI> userList) {
		this.userList = userList;
	}
	/*
        public List<Long> getUserTypes() {
            return userTypes;
        }

        public void setUserTypes(List<Long> selectedUserTypes) {
            this.userTypes = selectedUserTypes;
        }

        public List<Long> getDepartments() {
            return departments;
        }

        public void setDepartments(List<Long> departments) {
            this.departments = departments;
        }

        public List<Long> getDivisions() {
            return divisions;
        }

        public void setDivisions(List<Long> divisions) {
            this.divisions = divisions;
        }
    */
	public Long getInternalIdentifierTypes() {
		return internalIdentifierTypes;
	}

	public void setInternalIdentifierTypes(Long internalIdentifierTypes) {
		this.internalIdentifierTypes = internalIdentifierTypes;
	}

	public boolean isActiveChecked() {
		return activeChecked;
	}

	public void setActiveChecked(boolean activeChecked) {
		this.activeChecked = activeChecked;
	}

	public boolean isLeaveChecked() {
		return leaveChecked;
	}

	public void setLeaveChecked(boolean leaveChecked) {
		this.leaveChecked = leaveChecked;
	}

	public boolean isInactiveChecked() {
		return inactiveChecked;
	}

	public void setInactiveChecked(boolean inactiveChecked) {
		this.inactiveChecked = inactiveChecked;
	}

	// ******* GO button *******
	public String goSearch() {
//		this.rejectedUserList = this.rejectedUserService.findByValue(this.searchSrcUserId,
//				this.searchExtrctSysId, selectedDateCriteria, createdDateCriteria);
		this.refreshList(false);
		return null;
	}

	// ******* RESET button *******
	public String resetSearch() {
		this.searchSrcUserId = null;
		this.searchExtrctSysId = null;
		this.createdDateCriteria = null;
		refreshList(false);
		return null;
	}

	public void init() {
		this.isInitialLoad = true;
		this.searchSrcUserId = null;
		this.searchExtrctSysId = null;
		this.createdDateCriteria = null;
		this.rejectedUserList = null;
	}


	// ******* List Headers *******
	public void doSort() {
		final String column = (JSFUtils.getParameter("column") == null ? "createdDate"
				: JSFUtils.getParameter("column"));
		CommonPageActionHelper.sortListByField(rejectedUserList, column,
				this.oldSortColumn);
		this.oldSortColumn = column;
	}
	public void doUserSort() {
		final String column = (JSFUtils.getParameter("column") == null ? "lastName"
				: JSFUtils.getParameter("column"));
		CommonPageActionHelper.sortListByField(userList, column,
				this.oldSortColumn);
		this.oldSortColumn = column;
	}

	public void doDisplayRowListener() {
		logger.debug("doDisplayRowListener(DataScrollerEvent event) --> being executed.");
	}

	public void doScrollerListener(PageEvent event) {
		logger.debug("doScrollerListener(DataScrollerEvent event) --> being executed.");
	}
	// ******* CLEAR FIELDS ******
	public void clearFields(){
		this.userId = null;
		this.lastName = "";
		this.firstName = "";
		this.middleName = "";
		this.departmentId = null;
		this.divisionId = null;
		this.keyId = "";
		this.supervisorId = null;
		this.populateSupevisorData();
		this.status = null;
		this.userType = null;
		this.costCenter = "";
		//this.deleteFlag = ;
		this.jobTitle = "";
		this.emailAddress = "";
		this.phone = "";
		this.location = "";

		this.satJobRole = "";
		this.bsnssSgmnt = "";
		this.satAltId1 = "";
		this.satAltId2 = "";
		this.satAltId3 = "";
		this.satMFId = "";
		this.satGFId = "";
		this.satCFId = "";
		this.satFDMSId = "";
		this.satLCSId = "";
		this.satSiteMinderId = "";

	}

	public String showAddUserPanel() {
		String rejectedUserIdParam = (JSFUtils.getParameter("column") == null ? "name"
				: JSFUtils.getParameter("column"));

		RejectedUser selectedRejectedUser;

		this.clearFields();

		for (RejectedUserUI rejectedUserUI : this.rejectedUserList) {
			if (rejectedUserIdParam.equals(rejectedUserUI.getRejectedUserId()
					.toString())) {

				selectedRejectedUser = rejectedUserUI.getRejectedUser();
				this.emailAddress = selectedRejectedUser.getSrcEmailAddress();
				this.rejectedUserId = Long.parseLong(rejectedUserIdParam);
				this.keyId = rejectedUserUI.getSrcUserId();
				this.extrctSysId = rejectedUserUI.getExtractSystemId();
				this.extractSysDate = rejectedUserUI.getCreatedDate();
				this.extrctSysName = rejectedUserUI.getExtractSystemName();
			}
		}

		this.renderAddUserModalPanel = true;
		return null;
	}

	public String doAddNewUser() {

		System.out.println("this.keyId "+this.keyId);
		System.out.println("this.firstName "+this.firstName);
		System.out.println("this.lastName "+this.lastName);
		System.out.println("this.status "+this.status);
		System.out.println("this.supervisorId "+this.supervisorId);
		System.out.println("this.userType "+this.userType);
		System.out.println("this.departmentId "+this.departmentId);
		System.out.println("this.divisionId "+this.divisionId);
		System.out.println("this.bsnssSgmnt "+this.bsnssSgmnt);



		if (this.keyId == null || "".equals(this.keyId)) {
			String message = "Key Id is required.";
			JSFUtils.addFacesErrorMessage(message);
			this.renderAddUserModalPanel = true;
			PrimeFaces.current().executeScript("PF('addModalPanelWidget').show();");
			return null;
		}
		System.out.println("Above First Name is required. ");
		if (this.firstName == null || "".equals(this.firstName)) {
			String message = "First Name is required.";
			JSFUtils.addFacesErrorMessage(message);
			this.renderAddUserModalPanel = true;
			PrimeFaces.current().executeScript("PF('addModalPanelWidget').show();");
			return null;
		}
		System.out.println("Above last Name is required. ");
		if (this.lastName == null || "".equals(this.lastName)) {
			String message = "Last Name is required.";
			JSFUtils.addFacesErrorMessage(message);
			this.renderAddUserModalPanel = true;
			PrimeFaces.current().executeScript("PF('addModalPanelWidget').show();");
			return null;
		}
		System.out.println("Above user status is required. ");
		if (this.status == null || this.status ==0) {
			String message = "User Status is required.";
			JSFUtils.addFacesErrorMessage(message);
			PrimeFaces.current().executeScript("PF('addModalPanelWidget').show();");
			this.renderAddUserModalPanel = true;
			return null;
		}
		System.out.println("Above Supervisor is required. ");
		if (this.supervisorId == null || this.supervisorId ==0) {
			String message = "Supervisor is required.";
			JSFUtils.addFacesErrorMessage(message);
			PrimeFaces.current().executeScript("PF('addModalPanelWidget').show();");
			this.renderAddUserModalPanel = true;
			return null;
		}
		System.out.println("Above user type is required. ");
		if (this.userType == null || this.userType ==0) {
			String message = "User Type is required.";
			JSFUtils.addFacesErrorMessage(message);
			PrimeFaces.current().executeScript("PF('addModalPanelWidget').show();");
			this.renderAddUserModalPanel = true;
			return null;
		}
		System.out.println("Above department is required. ");
		if (this.departmentId == null || this.departmentId ==0) {
			String message = "Department is required.";
			JSFUtils.addFacesErrorMessage(message);
			PrimeFaces.current().executeScript("PF('addModalPanelWidget').show();");
			this.renderAddUserModalPanel = true;
			return null;
		}
		System.out.println("Above division is required. ");
		if (this.divisionId == null || this.divisionId ==0) {
			String message = "Division is required.";
			JSFUtils.addFacesErrorMessage(message);
			PrimeFaces.current().executeScript("PF('addModalPanelWidget').show();");
			this.renderAddUserModalPanel = true;
			return null;
		}
		System.out.println("Above business segment is required. ");
		if (this.bsnssSgmnt == null) {
			String message = "Business Segment is required.";
			JSFUtils.addFacesErrorMessage(message);
			this.renderAddUserModalPanel = true;
			PrimeFaces.current().executeScript("PF('addModalPanelWidget').show();");
			return null;
		}
		System.out.println("end of business segment is required. ");

		this.keyId = this.keyId.toUpperCase();
		this.firstName = this.firstName.toUpperCase();
		this.middleName = this.middleName.toUpperCase();
		this.lastName = this.lastName.toUpperCase();
		System.out.println("***** Job Title is ***** :"+this.jobTitle);
		if(this.jobTitle!=null) {
			this.jobTitle = this.jobTitle.toUpperCase();
		}
		String userExistMsg = this.rejectedUserService.findUserExist(-1L, this.keyId, "", "", "","", "", "","", "", "");
		if (!"".equals(userExistMsg)) {
			JSFUtils.addFacesErrorMessage(userExistMsg);
			this.renderAddUserModalPanel = true;
			PrimeFaces.current().executeScript("PF('addModalPanelWidget').show();");
			return null;
		}
		this.rejectedUserService.addUser(this.keyId, this.firstName, this.middleName, this.lastName,
				this.userType, this.departmentId, this.divisionId, this.location, this.jobTitle,
				this.supervisorId, this.bsnssSgmnt, this.phone, this.emailAddress,
				this.status, satStatus,this.satJobRole, this.satComment,
				this.rejectedUserId, this.extrctSysId, this.extractSysDate);

		// Display Message.
		String message = "Added User " + this.lastName + " " + this.firstName + " " + this.middleName;
		FacesContext.getCurrentInstance().addMessage(message, new FacesMessage(message));
		refreshList(false);
		this.renderAddUserModalPanel = false;
		clearSelection();
		PrimeFaces.current().executeScript("PF('addModalPanelWidget').hide();");
		return null;
	}

	public String doAddUser() {

		if (this.firstName==null || "".equals(this.firstName)) {
			String message = "First Name is required.";
			JSFUtils.addFacesErrorMessage(message);
			this.renderAddUserModalPanel = true;
			return null;
		}
		if (this.lastName==null || "".equals(this.lastName)) {
			String message = "Last Name is required.";
			JSFUtils.addFacesErrorMessage(message);
			this.renderAddUserModalPanel = true;
			return null;
		}
		if (this.status == null|| this.status ==0) {
			String message = "User Status is required.";
			JSFUtils.addFacesErrorMessage(message);
			this.renderAddUserModalPanel = true;
			return null;
		}
		if (this.supervisorId == null|| this.supervisorId ==0) {
			String message = "Supervisor is required.";
			JSFUtils.addFacesErrorMessage(message);
			this.renderAddUserModalPanel = true;
			return null;
		}
		if (this.userType == null ||this.userType==0) {
			String message = "User Type is required.";
			JSFUtils.addFacesErrorMessage(message);
			this.renderAddUserModalPanel = true;
			return null;
		}
		if (this.departmentId == null || this.departmentId==0) {
			String message = "Department is required.";
			JSFUtils.addFacesErrorMessage(message);
			this.renderAddUserModalPanel = true;
			return null;
		}
		if (this.divisionId == null|| this.divisionId==0) {
			String message = "Division is required.";
			JSFUtils.addFacesErrorMessage(message);
			this.renderAddUserModalPanel = true;
			return null;
		}
		if (this.bsnssSgmnt == null) {
			String message = "Business Segment is required.";
			JSFUtils.addFacesErrorMessage(message);
			this.renderAddUserModalPanel = true;
			return null;
		}

		this.rejectedUserService.updateUser(this.rejectedUserId, userId, keyId, firstName, middleName, lastName,
				userType, departmentId, divisionId, location, jobTitle,
				supervisorId, costCenter, bsnssSgmnt, phone, emailAddress,
				status, satStatus, this.satJobRole, this.satComment);
		// Display Message.
		String message = "Added User " + lastName + " " + firstName + " "
				+ middleName;
		FacesContext.getCurrentInstance().addMessage(message,
				new FacesMessage(message));
		refreshList(false);
		this.renderAddSearchedUserPanel = false;
		clearSelection();
		return null;
	}

	public String doAddAlternateId() {

		boolean isUpdatable = true;
		if (internalIdentifierTypeCD.equalsIgnoreCase("SAT_ALT_ID1")){
			if (this.satAltId1!=null && !"".equalsIgnoreCase(satAltId1)){
				JSFUtils.addFacesErrorMessage("SatAltID1 already exists");
				isUpdatable = false;
			}
			else
				this.satAltId1 = this.newAltId;
		}
		else if (internalIdentifierTypeCD.equalsIgnoreCase("SAT_ALT_ID2")){
			if (this.satAltId2!=null && !"".equalsIgnoreCase(satAltId2)){
				JSFUtils.addFacesErrorMessage("SatAltID2 already exists");
				isUpdatable = false;
			}
			else
				this.satAltId2 = this.newAltId;
		}
		else if (internalIdentifierTypeCD.equalsIgnoreCase("SAT_ALT_ID3")){
			if (this.satAltId3!=null && !"".equalsIgnoreCase(satAltId3)){
				JSFUtils.addFacesErrorMessage("SatAltID3 already exists");
				isUpdatable = false;
			}
			else
				this.satAltId3 = this.newAltId;
		}
		else if (internalIdentifierTypeCD.equalsIgnoreCase("SAT_MF_ID")){
			if (this.satMFId!=null && !"".equalsIgnoreCase(satMFId)){
				JSFUtils.addFacesErrorMessage("SAT_MF_ID already exists");
				isUpdatable = false;
			}
			else
				this.satMFId = this.newAltId;
		}
		else if (internalIdentifierTypeCD.equalsIgnoreCase("SAT_CF_ID")){
			if (this.satCFId!=null && !"".equalsIgnoreCase(satCFId)){
				JSFUtils.addFacesErrorMessage("SAT_CF_ID already exists");
				isUpdatable = false;
			}
			else
				this.satCFId = this.newAltId;
		}
		else if (internalIdentifierTypeCD.equalsIgnoreCase("SAT_GF_ID")){
			if (this.satGFId!=null && !"".equalsIgnoreCase(satGFId)){
				JSFUtils.addFacesErrorMessage("SAT_GF_ID already exists");
				isUpdatable = false;
			}
			else
				this.satGFId = this.newAltId;
		}
		else if (internalIdentifierTypeCD.equalsIgnoreCase("SAT_LCS_ID")){
			if (this.satLCSId!=null && !"".equalsIgnoreCase(satLCSId)){
				JSFUtils.addFacesErrorMessage("SAT_LCS_ID already exists");
				isUpdatable = false;
			}
			else
				this.satLCSId = this.newAltId;
		}
		else if (internalIdentifierTypeCD.equalsIgnoreCase("SAT_FDMS_ID")){
			if (this.satFDMSId!=null && !"".equalsIgnoreCase(satFDMSId)){
				JSFUtils.addFacesErrorMessage("SAT_FDMS_ID already exists");
				isUpdatable = false;
			}
			else
				this.satFDMSId = this.newAltId;
		}
		else if (internalIdentifierTypeCD.equalsIgnoreCase("SAT_SITEMIND_ID")){
			if (this.satSiteMinderId!=null && !"".equalsIgnoreCase(satSiteMinderId)){
				JSFUtils.addFacesErrorMessage("SAT_SITEMIND_ID already exists");
				isUpdatable = false;
			}
			else
				this.satSiteMinderId = this.newAltId;
		}
		else if (internalIdentifierTypeCD.equalsIgnoreCase("EMAIL_ADDR")){
			if (this.emailAddress!=null && !"".equalsIgnoreCase(emailAddress)){
				JSFUtils.addFacesErrorMessage("EMAIL_ADDR already exists");
				isUpdatable = false;
			}
			else
				this.emailAddress = this.newAltId;
		}
		else{
			JSFUtils.addFacesErrorMessage("Please select available Alternate Id Types");
			isUpdatable = false;
		}

		if(this.rejectedUserService.isAltIdExists(this.newAltId)){
			JSFUtils.addFacesErrorMessage("Alternate Id exists "+ this.newAltId);
			isUpdatable = false;
		}

		if(!isUpdatable){
			this.renderAddAlternateIdModalPanel = true;
			PrimeFaces.current().executeScript("PF('addAlternateIdModalVar').show();");
			return null;
		}
		else{
			this.rejectedUserService.updateAltIdsForUser( this.userId,
					this.satMFId, this.satGFId, this.satCFId, this.satLCSId, this.satFDMSId,
					this.satSiteMinderId, this.satAltId1, this.satAltId2, this.satAltId3);

			this.rejectedUserService.updateAltIds(this.rejectedUserId,
					this.userId, this.keyId, this.emailAddress,
					this.satMFId, this.satGFId, this.satCFId, this.satLCSId, this.satFDMSId,
					this.satSiteMinderId, this.satAltId1, this.satAltId2, this.satAltId3);

			String message = "Added Alternate Id " + this.newAltId ;
			FacesContext.getCurrentInstance().addMessage(message, new FacesMessage(message));

			this.renderAddAlternateIdModalPanel = false;
			this.refreshList(false);
			clearSelection();
			PrimeFaces.current().executeScript("PF('addAlternateIdModalVar').hide();");
		}
		return null;
	}

	// ******* Add User Panel CANCEL *******
	public String doCancelAddUser() {
		logger.debug("doCancelAddUserPanel() --> being executed.");
		this.renderAddUserModalPanel = false;
		//clearSelection();
		return null;
	}

	public String showSearchUser() {
		final String srcUserName = (JSFUtils.getParameter("column") == null ? "name"
				: JSFUtils.getParameter("column"));
		if (JSFUtils.getParameter("rejectedIdcolumn") != null)
			this.rejectedUserId  = Long.parseLong(JSFUtils.getParameter("rejectedIdcolumn"));
		System.out.println("******Rejected ID Column** "+this.rejectedUserId);
		if (JSFUtils.getParameter("newAltIdcolumn") != null)
			this.newAltId  = JSFUtils.getParameter("newAltIdcolumn");

		List<User> usersRetrieved = new ArrayList<User>();
		this.userList = new ArrayList<UserUI>(usersRetrieved.size() + 1);

		String searchLastName;
		String searchFirstName;
		if(srcUserName.indexOf(",") != -1){
			searchLastName = srcUserName.substring(0, srcUserName
					.indexOf(',') - 1).trim();
			searchFirstName = srcUserName
					.substring(srcUserName.indexOf(',') + 1).trim();
		}
		else{
			searchLastName = srcUserName;
			searchFirstName = srcUserName;
		}
		usersRetrieved = this.rejectedUserService.findByUserName(searchFirstName,
				searchLastName);
		for (User user : usersRetrieved) {
			this.userList.add(new UserUI(user));
		}
		this.renderSearchUserModalPanel = true;
		return null;
	}

	public String doCancelSearchUser() {
		logger.debug("doCancelSearchUserPanel() --> being executed.");
		this.userList = null;
		this.renderSearchUserModalPanel = false;
		return null;
	}

	public String showAddUserPanelFromSearch() {

		this.clearFields();

		RejectedUser selectedRejectedUser = this.rejectedUserService.findByRejectedUserId(this.rejectedUserId);
		if (selectedRejectedUser!=null){
			this.emailAddress = selectedRejectedUser.getSrcEmailAddress();
			this.keyId = selectedRejectedUser.getSrcUserId();
			ExtractSystem es = selectedRejectedUser.getExtractSystem();
			this.extrctSysId = es.getExtrctSysId();
			this.extrctSysName = es.getExtrctSysNm();
		}

		this.renderSearchUserModalPanel = false;
		this.renderAddUserModalPanel = true;
		return null;
	}

	public String showAddSearchedUserPanel() {
		final String searchKeyId = (JSFUtils.getParameter("column") == null ? "name"
				: JSFUtils.getParameter("column"));
		for (UserUI userUI : userList) {
			if (userUI.getKeyId().equals(searchKeyId)) {
				this.populateSearchedUserData(userUI.getUser());
				break;
//				populateSupevisorData();
			}
		}
		this.renderSearchUserModalPanel = false;
		//commented renderAddSearchedUserPanel to force only pick alternate Id
		//this.renderAddSearchedUserPanel = true;
		this.renderAddAlternateIdModalPanel = true;
		return null;
	}

	public String doCancelAssociatedUserSearch() {
		logger.debug("doCancelAssociatedUserSearch() --> being executed.");
		this.renderSearchedAssociatedUserModalPanel = true;
		this.renderSearchedAssociatedUserModalPanel = false;
		return null;
	}

	public String showAddAlternateIdPanel() {
		final String searchKeyId = (JSFUtils.getParameter("column") == null ? "name"
				: JSFUtils.getParameter("column"));

		this.firstName = null;
		this.lastName = null;
		this.supervisorId = null;
		this.keyId = null;
		this.searchStatuses = null;
		this.searchUserTypes = null;
		this.searchDepartments = null;
		this.searchDivisions = null;

		for (UserUI userUI : userList) {
			if (userUI.getKeyId().equals(searchKeyId)) {
				this.populateSearchedUserData(userUI.getUser());
				break;
			}
		}
		this.renderSearchedAssociatedUserModalPanel = false;
		this.renderAddAlternateIdModalPanel = true;
		return null;
	}

	public String doCancelAddAlternateId() {
		logger.debug("doCancelAddUserPanel() --> being executed.");
		this.renderAddAlternateIdModalPanel = false;
		clearSelection();
		return null;
	}

	public void populateSearchedUserData(User selectedUser){
		System.out.println("*** 1326 Populate Search User Data");
		this.keyId = selectedUser.getPk().getKeyId();
		this.userId = selectedUser.getUserId();
		this.firstName = selectedUser.getFirstName();
		this.lastName = selectedUser.getLastName();
		this.userTypeDescription = selectedUser.getUserType()
				.getUserTypeDescription();
		this.departmentName = selectedUser.getDepartment().getName();
		this.divisionName = selectedUser.getDivision().getName();
		this.emailAddress = selectedUser.getEmailAddress();
		this.supervisorId = selectedUser.getSupervisorId();
		this.supervisorName = selectedUser.getSupervisor();
		this.departmentId = selectedUser.getDepartment().getId();
		this.divisionId = selectedUser.getDivision().getId();
		this.costCenter = selectedUser.getCostCenter();
		this.bsnssSgmnt = selectedUser.getBusinessSegment();
		this.jobTitle = selectedUser.getJobTitle();
		this.location = selectedUser.getLocation();
		this.phone = selectedUser.getLocation();
		this.satStatus = selectedUser.getSatStatus();
		this.satComment = selectedUser.getSatComment();
		this.satJobRole = selectedUser.getSatJobRole();
		this.userStatusDescription = selectedUser.getUserStatus()
				.getUserStatusDescription();
		this.existingAlternateIds = rejectedUserService
				.getAlternateIds(keyId.toString());
		this.satMFId = selectedUser.getSatMfId();
		this.satGFId = selectedUser.getSatGfId();
		this.satCFId = selectedUser.getSatCfId();
		this.satLCSId = selectedUser.getSatLcsId();
		this.satFDMSId = selectedUser.getSatFdmsId();
		this.satSiteMinderId = selectedUser.getSatSiteminderId();
		this.satAltId1 = selectedUser.getSatAltId1();
		this.satAltId2 = selectedUser.getSatAltId2();
		this.satAltId3= selectedUser.getSatAltId3();

		System.out.println("*** 1362 Populate Search User Data End");
	}

	public void populateSupevisorData() {

		supevisorDepartments = new ArrayList<SelectItem>();
		supevisorDivisions = new ArrayList<SelectItem>();
		supevisorBusinessSegments = new ArrayList<SelectItem>();
		supevisorJobTitles = new ArrayList<SelectItem>();
		supevisorLocations = new ArrayList<SelectItem>();

		if(this.supervisorId!=null){
			this.rejectedUserService.populatSupevisorData(this.supervisorId);
			this.supevisorDepartments = this.rejectedUserService.populateSupevisorDepartments(this.supervisorId);
			this.supevisorDivisions = this.rejectedUserService.populateSupevisorDivisions(this.supervisorId);
			this.supevisorBusinessSegments = this.rejectedUserService
					.populateSupevisorBusinessSegments(this.supervisorId);
			this.supevisorJobTitles = this.rejectedUserService.populateSupevisorJobTitles(this.supervisorId,"");
			this.supevisorLocations = this.rejectedUserService.populateSupevisorLocations(this.supervisorId,"");
			List<User> directReports = this.rejectedUserService.findUsersBySupevisorId(this.supervisorId);
			this.supervisorDirectReports = new ArrayList<UserUI>();
			for (User user : directReports) {
				this.supervisorDirectReports.add(new UserUI(user));
			}
			CommonPageActionHelper.sortListByField(this.supervisorDirectReports, "name",
					this.oldSortColumn);

		}
	}

	public String doCancelAddSearhedUser() {
		logger.debug("doCancelAddUserPanel() --> being executed.");
		this.renderAddSearchedUserPanel = false;
		clearSelection();
		return null;
	}

	public String showAltUserSearchPanel() {
		this.firstName = null;
		this.lastName = null;
		this.userId = null;
		this.supervisorId = null;
		this.keyId = null;
		this.searchStatuses = null;
		this.searchUserTypes = null;
		this.searchDepartments = null;
		this.searchDivisions = null;

		this.renderSearchUserModalPanel = false;
		this.renderAssociatedUserSearchModalPanel = true;
		return null;
	}

	public String showAssociatedUserSearchPanel() {

		if (JSFUtils.getParameter("rejectedIdcolumn") != null)
			this.rejectedUserId  = Long.parseLong(JSFUtils.getParameter("rejectedIdcolumn"));
		if (JSFUtils.getParameter("newAltIdcolumn") != null)
			this.newAltId  = JSFUtils.getParameter("newAltIdcolumn");
		this.firstName = null;
		this.lastName = null;
		this.userId = null;
		this.keyId = null;
		this.supervisorId = null;
		this.searchStatuses = null;
		this.searchUserTypes = null;
		this.searchDepartments = null;
		this.searchDivisions = null;

		this.renderAssociatedUserSearchModalPanel = true;
		return null;
	}

	public boolean areMandatoryFieldsEmpty() {
		if (this.firstName != null && !this.firstName.isEmpty()) {
			return false;
		}
		if (this.lastName != null && !this.lastName.isEmpty()) {
			return false;
		}
		if (this.keyId != null && !this.keyId.isEmpty()) {
			return false;
		}
        return this.supervisorId == null;
    }

	public String doSearchAssociatedUser() {
		if(areMandatoryFieldsEmpty()) {
			String message = "First Name, Last Name, Key Id or Supervisor is required.";
			JSFUtils.addFacesErrorMessage(message);
			this.renderAssociatedUserSearchModalPanel = true;
			PrimeFaces.current().executeScript("PF('associatedUserSearchModalAjaxVar').show();");
			return null;
		}
		this.userList = this.rejectedUserService.searchAssociatedUser(firstName, lastName, keyId,
				supervisorId, searchStatuses, searchUserTypes, searchDepartments, searchDivisions,
				location);
		this.renderAssociatedUserSearchModalPanel = false;
		this.renderSearchedAssociatedUserModalPanel = true;
		PrimeFaces.current().executeScript("PF('associatedUserSearchModalAjaxVar').hide();");
		PrimeFaces.current().executeScript("PF('searchAssociatedUserModalVar').show();");
		return null;
	}

	public void doCancelSearchAssociatedUser() {
		logger.debug("doCancelSearchAssociatedUser() --> being executed.");
		this.rejectedUserId = null;
		this.firstName = null;
		this.lastName = null;
		this.keyId = null;
		this.supervisorId = null;
		this.searchStatuses = new ArrayList<>();
		this.searchUserTypes = new ArrayList<>();
		this.searchDepartments = new ArrayList<>();
		this.searchDivisions = new ArrayList<>();
		this.renderSearchedAssociatedUserModalPanel = false;
		this.renderAssociatedUserSearchModalPanel = false;
	}

	public void clearSelection() {
		this.userId = null;
		this.keyId = "";
		this.userId = null;
		this.firstName = "";
		this.middleName = "";
		this.lastName = "";
		this.userType = null;
		this.status = null;
		this.departmentId = null;
		this.divisionId = null;
		this.emailAddress = "";
		this.supervisorId = null;
		this.supevisorDepartments = null;
		this.supevisorDivisions = null;
		//this.supevisorCostCenters = null;
		this.supevisorJobTitles = null;
		this.supevisorLocations = null;
		this.supevisorBusinessSegments = null;
		this.costCenter = "";
		this.bsnssSgmnt = "";
		this.jobTitle = "";
		this.location = "";
		this.phone = "";
		this.satStatus = "";
		this.satComment = "";
		this.satJobRole = "";

	}
	public List<SelectItem> getAvailableStatus() {

		List<UserStatus> userStatusList = this.rejectedUserService.findUserStatusList();
		//Sort
		Collections.sort(userStatusList, new Comparator<UserStatus>() {
			public int compare(UserStatus userStatus1, UserStatus userStatus2) {
				return userStatus1.getUserStatusDescription().compareTo(userStatus2.getUserStatusDescription());
			}
		});
		availableStatus = new ArrayList<SelectItem>();
		for (UserStatus us : userStatusList) {
			if (us.getUserStatusDescription()!=null)
				availableStatus.add(new SelectItem(us.getId(), us.getUserStatusDescription()));
		}
		return availableStatus;
	}

	public List<SelectItem> getAvailableUserType() {
		List<UserType> userTypeName = this.rejectedUserService.retrieveAllUserTypes();
		//Sort
		Collections.sort(userTypeName, new Comparator<UserType>() {
			public int compare(UserType userType1, UserType userType2) {
				return userType1.getUserTypeDescription().compareTo(userType2.getUserTypeDescription());
			}
		});

		this.availableUserType = new ArrayList<SelectItem>();
		this.availableUserType.add(new SelectItem("", ""));
		for(UserType ut:userTypeName){
			if(ut.getUserTypeDescription()!=null){
				this.availableUserType.add(new SelectItem(ut.getId(), ut.getUserTypeDescription()));
			}
		}
		return this.availableUserType;
	}

	public List<SelectItem> getMultiSelectDepartmentList(){
		int countDepartments = rejectedUserService.countAllDepartments();
		int availableDepartmentCount = availableDepartment != null ? availableDepartment.size() : 0;
		int ifAnyRecordAdded = Math.abs(availableDepartmentCount - countDepartments); // this tells if any record added or not if size is more than 1
		System.out.println("Available Department Count: " + availableDepartmentCount);
		System.out.println("Count Departments: " + countDepartments);
		if (availableDepartment == null || ifAnyRecordAdded > 1) { // if availableDepartment is null or if any record added get the latest records
			List<Department> deptNames = this.rejectedUserService.retrieveAllDepartments();
			//Sort
			Collections.sort(deptNames, new Comparator<>() {
				public int compare(Department dept1, Department dept2) {
					if (dept1 == null || dept2 == null) {
						return 0;
					}

					if (dept1.getName() == null || dept2.getName() == null) {
						return 0;
					}
					return dept1.getName().compareTo(dept2.getName());
				}
			});
			this.availableDepartment = new ArrayList<>();
			this.availableDepartment.add(new SelectItem("", ""));
			for (Department dep : deptNames) {
				if (dep.getName() != null && dep.getDepartmentNmCostCenter() != null)
					this.availableDepartment.add(new SelectItem("" + dep.getId(), dep.getDepartmentNmCostCenter()));
			}
		}
		return this.availableDepartment;

	}


	public List<SelectItem> getMultiSelectDivisionList() {
		int divisionCount = this.rejectedUserService.countAllDivisions();
		int availableDivisionCount = availableDivision != null ? availableDivision.size() - 1 : 0;
		System.out.println("Available Division Count: " + availableDivisionCount);
		System.out.println("Count Divisions: " + divisionCount);
		int ifAnyRecordAdded = Math.abs(availableDivisionCount - divisionCount); // this tells if any record added or not if size is more than 1
		if (availableDivision == null || ifAnyRecordAdded > 1) { // if availableDivision is null or if any record added get the latest records
			List<Division> divName = this.rejectedUserService.retrieveAllDivisions();
			//Sort
			Collections.sort(divName, new Comparator<>() {
				public int compare(Division div1, Division div2) {
					if (div1 == null || div2 == null) {
						return 0;
					}
					if (div1.getName() == null || div2.getName() == null) {
						return 0;
					}
					return div1.getName().compareTo(div2.getName());
				}
			});
			this.availableDivision = new ArrayList<>();
			this.availableDivision.add(new SelectItem("", ""));
			for (Division div : divName) {
				if (div.getName() != null)
					this.availableDivision.add(new SelectItem("" + div.getId(), div.getName()));
			}
		}
		return this.availableDivision;
	}

	public List<SelectItem> getMultiSelectUserTypeList() {
		List<UserType> userTypeName = this.rejectedUserService.retrieveAllUserTypes();

		//Sort
		Collections.sort(userTypeName, new Comparator<>() {
			public int compare(UserType userType1, UserType userType2) {
				if (userType1 == null || userType2 == null) {
					return 0;
				}
				if (userType1.getUserTypeDescription() == null || userType2.getUserTypeDescription() == null) {
					return 0;
				}
				return userType1.getUserTypeDescription().compareTo(userType2.getUserTypeDescription());
			}
		});

		List<SelectItem> usrTypeList = new ArrayList<>();
		usrTypeList.add(new SelectItem("", ""));
		for(UserType ut:userTypeName){
			if(ut.getUserTypeDescription()!=null){
				usrTypeList.add(new SelectItem(""+ut.getId(), ut.getUserTypeDescription()));
			}
		}
		return usrTypeList;
	}

	public List<SelectItem> getMultiSelectUserStatusList() {
		List<UserStatus> userStatusList = this.rejectedUserService.findUserStatusList();

		//Sort
		Collections.sort(userStatusList, new Comparator<>() {
			public int compare(UserStatus userStatus1, UserStatus userStatus2) {
				if (userStatus1 == null || userStatus2 == null) {
					return 0;
				}
				if (userStatus1.getUserStatusDescription() == null || userStatus2.getUserStatusDescription() == null) {
					return 0;
				}
				return userStatus1.getUserStatusDescription().compareTo(userStatus2.getUserStatusDescription());
			}
		});

		List<SelectItem> statusList = new ArrayList<>();
		for (UserStatus us : userStatusList) {
			if (us.getUserStatusDescription()!=null)
				statusList.add(new SelectItem(""+us.getId(), us.getUserStatusDescription()));
		}
		return statusList;
	}

	public List<SelectItem> getAvailableInternalIdentifierType() {
		return rejectedUserService.getAvailableInternalIdentifierType();
	}

	public void setSessionDataBean(SessionDataBean sessionDataBean) {
		this.sessionDataBean = sessionDataBean;
	}

	public SessionDataBean getSessionDataBean() {
		return sessionDataBean;
	}

	public List<SelectItem> getAvailableSupervisors() {
		List<Supervisor> supervisorsList = this.rejectedUserService.retrieveAllSupervisors();

		List<SelectItem> availableSupervisors = new ArrayList<SelectItem>();
		availableSupervisors.add(new SelectItem("", ""));
		for (Supervisor supervisor : supervisorsList) {
			StringBuilder formattedName = new StringBuilder();
			String firstName = supervisor.getFirstName();
			String lastName = supervisor.getLastName();
			String middleName = (supervisor.getMiddleName()!=null)?supervisor.getMiddleName():"";

			if (!"".equals(lastName)) {
				formattedName.append(lastName);

				if (!"".equals(firstName)) {
					formattedName.append(", ");
				}
			}

			if (!"".equals(firstName)) {
				formattedName.append(firstName);

				if (!"".equals(middleName)) {
					formattedName.append(" ").append(middleName.substring(0, 1));
				}
			}

			availableSupervisors.add(new SelectItem(supervisor.getUserId(),
					formattedName.toString()));
		}

		return availableSupervisors;
	}

	public String doReconcile() {
		// reconcile rejects
		logger.debug("******************** Do Reconsile - Start *****************************");
		logger.debug("searchExtrctSysId before reconcile : "+this.searchExtrctSysId);
		this.rejectedUserService.reconcileRejects();
		if(this.searchExtrctSysId==-1){
			this.searchExtrctSysId = null;
			System.out.println("In doreconcile line 1642" +searchExtrctSysId);
		}
		logger.debug("searchExtrctSysId after reconcile: "+this.searchExtrctSysId);
		this.refreshList(false);
		logger.debug("searchExtrctSysId after refreshing: "+this.searchExtrctSysId);
		logger.debug("******************** Do Reconsile - End *****************************");
		return null;
	}


} // end DepartmentSummaryBean

