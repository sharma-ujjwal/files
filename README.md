```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
	xmlns:h="http://xmlns.jcp.org/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	xmlns:p="http://primefaces.org/ui">

	<ui:composition template="../common/referenceCoreTableTemplate.xhtml">
		<ui:define name="referenceCoreTable">
			<div style="float: left; display: ruby; margin: 1rem;">
			<!-- **************************     User Filters     **************************   -->

			<div style="float: left">
				<!--  Search -->
			<p:commandLink id="filterPanel" autoUpdate="true">
				<table width="800">
					<tbody>
						<tr>

							<td align="left">
								<h:outputLabel id="keyIdSearchLabel">Key Id/Alternate ID:</h:outputLabel>
							</td>
							<td align="left">

								<h:inputText id="keyIdSearchText" maxlength="100"
									value="#{userSummaryBean.searchKeyId}"
									onchange="this.value = this.value.toUpperCase();">
								</h:inputText>
							</td>

							<td align="left">
								<h:outputLabel id="SupervisorSearchLabel">Supervisor:</h:outputLabel>
							</td>
							<td align="left">
								<h:selectOneMenu id="searchSupervisorSelectOneMenu" 
									value="#{userSummaryBean.searchSupervisorId}">
									<f:selectItems id="searchSupervisorSelectItems"
										value="#{userSummaryBean.availableSupervisors}"/>
								</h:selectOneMenu>
							</td>

							<td align="left">
								<h:outputLabel id="selectDepartmentLabel">Department-CostCenter:</h:outputLabel>
							</td>
							<td align="left">
								<h:selectManyMenu id="searchDepartmentSelectManyMenu"
									value="#{userSummaryBean.searchDepartmentId}" style="height: 50px">
									<f:selectItems value="#{userSummaryBean.multiSelectDepartmentList}" />
								</h:selectManyMenu>
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel id="LastNameLabel">Last Name:</h:outputLabel>
							</td>
							<td align="left">
								<h:inputText id="LastNameText" maxlength="100"
									value="#{userSummaryBean.searchLastName}"
									onchange="this.value = this.value.toUpperCase();">
								</h:inputText>
							</td>
							<td align="left">
								<h:outputLabel id="selectStatusLabel">Status: </h:outputLabel>
							</td>
							<td align="left">
								<h:selectManyMenu id="searchStatusSelectManyMenu"
									value="#{userSummaryBean.searchStatus}" style="height: 50px">
									<f:selectItems value="#{userSummaryBean.multiSelectUserStatusList}" />
								</h:selectManyMenu>
							</td>
							<td align="left">
								<h:outputLabel id="selectDivisionLabel">Division: </h:outputLabel>
							</td>
							<td align="left">
								<h:selectManyMenu id="searchDivisionSelectManyMenu"
									value="#{userSummaryBean.searchDivisionId}" style="height: 50px">
									<f:selectItems value="#{userSummaryBean.multiSelectDivisionList}" />
								</h:selectManyMenu>
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel id="FirstNameLabel">First Name:  </h:outputLabel>
							</td>

							<td align="left">
								<h:inputText id="FirstNameText" maxlength="100"
									value="#{userSummaryBean.searchFirstName}"
									onchange="this.value = this.value.toUpperCase();">
								</h:inputText>
							</td>
							<td align="left">

								<h:outputLabel id="userTypeLabel">User Type: </h:outputLabel>
							</td>
							<td align="left">
								<h:selectManyMenu id="searchUserTypeSelectManyMenu"
									value="#{userSummaryBean.searchUserType}" style="height: 50px">
									<f:selectItems value="#{userSummaryBean.multiSelectUserTypeList}" />
								</h:selectManyMenu>
							</td>
							<td align="left">
								<h:outputLabel id="businessSgmntLabel">Business Segment:  </h:outputLabel>
							</td>
							<td align="left">
								<h:selectManyMenu id="searchBusinessSgmntSelectManyMenu"
									value="#{userSummaryBean.searchBsnssSgmnt}" style="height: 50px">
									<f:selectItems value="#{userSummaryBean.availableBusinessSeg}" />
								</h:selectManyMenu>
							</td>
						</tr>
						<tr>
							<td colspan="5" align="right">
								<h:outputLabel id="costCenterLabel">Cost Center: </h:outputLabel>
							</td>
							<td align="left">
								<h:selectManyMenu id="searchCostCenterTextSelectManyMenu"
									value="#{userSummaryBean.searchCostCenter}" style="height: 50px">
									<f:selectItems value="#{userSummaryBean.availableCostCenter}" />
								</h:selectManyMenu>
							</td>
						</tr>
						</tbody>
						</table>
						

				<table width="800">
					<tbody>
							<tr>
								<td align="left">
									<!-- Go Button -->
									<h:commandButton id="goSearchButton"
													 value="Go"
													 action="#{userSummaryBean.goSearch}" onclick="showModalInfoWindow();"/>
									<!-- Reset Button -->
									<h:commandButton id="resetButton"
													 value="Reset"
													 action="#{userSummaryBean.resetSearch}" />
									<!--  Select Filter -->
									<h:outputLabel id="selectFilterLabel12" value="Filter: " />
									<!--  Select Filter ListBox -->
									<h:selectOneMenu id="filterListbox12"
													 value="#{userSummaryBean.activeFilter}">
										<f:selectItems
											value="#{userSummaryBean.availableFilters}" />
									</h:selectOneMenu>
								</td>
								<td align="right">
									<h:commandButton id="addNewButton" 
													 value="Add New Row"
													 action="#{userSummaryBean.showAddUserPanel}" 
													 rendered="true">
									</h:commandButton>
									<h:commandButton id="deleteRowButton" 
													 value="Delete Row" 
													 action="#{userSummaryBean.showDeleteUserPanel}" 
													 rendered="#{userSummaryBean.showDeleteButton}">
									</h:commandButton>
									<h:commandButton id="updateRowButton" 
													 value="Update Row" 
													 action="#{userSummaryBean.showUpdateUserPanel}" 
													 rendered="true">
									</h:commandButton>
									<div class="verticalSpacer" />
								</td>
							</tr>

					</tbody>
				</table>
						</p:commandLink>

			</div>
			<!-- **************************     end of Filters     **************************   -->
					</div>

						<!-- **************************     User Display Table     **************************   -->
			<p:commandLink id="userTablePanel" ajax="true">
				<p:dataTable id="userTable" 
							 value="#{userSummaryBean.userList}" 
							 var="element" 
							 rows="#{userSummaryBean.displayAmount}"
							 paginator="true"
							 paginatorPosition="bottom"
							 paginatorTemplate="{CurrentPageReport}  {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink}  {RowsPerPageDropdown}"
							 currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
							 rowsPerPageTemplate="10,25,50,100"
							 rowIndexVar="rowIndex"
							 rowKeyVar="rowKey"
							 scrollable="true"
							 styleClass="defaultTableHeader" 
							 rowStyleClasses="oddRow, evenRow" >


					<p:column id="deleteCheckBoxColumn" rendered="true">
						<f:facet name="header">
							<p:commandLink action="#{userSummaryBean.switchCheckBoxToggle}">Select All</p:commandLink>
						</f:facet>
						<h:selectBooleanCheckbox id="userSummary" 
												 value="#{element.checked}" />
					</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="User Id" 
										   action="#{userSummaryBean.doSort}" 
										   styleClass="headerSortLink">
								<f:param name="column" value="userId" />
							</p:commandLink>
						</f:facet>	
										#{element.userId}  
						</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="Key Id"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="keyId" />
							</p:commandLink>
						</f:facet>	
							<p:commandLink value="#{element.keyId}"
								action="#{userSummaryBean.showViewUserPanel}"
								styleClass="headerSortLink">
								<f:param name="viewUserId" value="#{element.userId}" />
							</p:commandLink>
						</p:column>
					<p:column>
						<f:facet name="header">
							<p:commandLink value="Name" action="#{userSummaryBean.doSort}"
								styleClass="headerSortLink">
								<f:param name="column" value="name" />
							</p:commandLink>
						</f:facet>	
										#{element.name} 
						</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="User Type"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="userType" />
							</p:commandLink>
						</f:facet>	
										#{element.userType} 
						</p:column>


					<p:column>

						<f:facet name="header">
							<p:commandLink value="Department-CostCenter"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="department" />
							</p:commandLink>
						</f:facet>	
										#{element.department}-#{element.costCenter}
						</p:column>

					<p:column>

						<f:facet name="header">
							<p:commandLink value="Division"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="division" />
							</p:commandLink>
						</f:facet>	
										#{element.division}
						</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="Supervisor Name"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="supervisorName" />
							</p:commandLink>
						</f:facet>	
										#{element.supervisorName} 
						</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="Status"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="userStatusDescription" />
							</p:commandLink>
						</f:facet>	
										#{element.userStatusDescription} 
						
						</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="Location"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="location" />
							</p:commandLink>
						</f:facet>	
										#{element.location}
						</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="Business Segment"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="businessSeg" />
							</p:commandLink>
						</f:facet>	
										#{element.businessSeg}
						</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="Last Changed By"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="lastUpdatedBy" />
							</p:commandLink>
						</f:facet>	
										#{element.lastUpdatedBy}
						</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="Last Changed Date"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="lastUpdatedDate" />
							</p:commandLink>
						</f:facet>	
						<h:outputText value="#{element.lastUpdatedDate}">
							<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}" />
						</h:outputText>  
					</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="Created By"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="createdBy" />
							</p:commandLink>
						</f:facet>	
						#{element.createdBy}
						</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="Created Date"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="createdDate" />
							</p:commandLink>
						</f:facet>	
						<h:outputText value="#{element.createdDate}">
							<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}" />
						</h:outputText>  
						</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="Extract Date"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="extractDate" />
							</p:commandLink>
						</f:facet>	
						<h:outputText value="#{element.extractDate}">
							<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}" />
						</h:outputText>  
						</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="Active From"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="activeFromDate" />
							</p:commandLink>
						</f:facet>	
						<h:outputText value="#{element.activeFromDate}">
							<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}" />
						</h:outputText>  
						</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="Active To"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="activeToDate" />
							</p:commandLink>
						</f:facet>	
						<h:outputText value="#{element.activeToDate}">
							<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}" />
						</h:outputText>  
						</p:column>

					<p:column>
						<f:facet name="header">
							<p:commandLink value="Delete Flag"
								action="#{userSummaryBean.doSort}" styleClass="headerSortLink">
								<f:param name="column" value="deleteFlag" />
							</p:commandLink>
						</f:facet>	
										#{element.deleteFlag}
						</p:column>


				</p:dataTable>
			</p:commandLink>
		</ui:define>

<!-- Java script for auto populate user -->
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
		<ui:define name="script">
			<script type="text/javascript">
				/* <![CDATA[ */
				function setDepartmentValue(value) {
					var selector = document.getElementById('addUserForm:departmentListbox');
					setSelectorValue(selector, value);
				}

				function setDivisionValue(value) {
					var selector = document.getElementById('addUserForm:divisionListbox');
					setSelectorValue(selector, value);
				}

				function setBusinessSegValue(value) {
					var selector = document.getElementById('addUserForm:bsnssSgmntListbox');
					setSelectorValue(selector, value);
				}

				function setLocationValue(value) {
					var selector = document.getElementById('addUserForm:locationListbox');
					setSelectorValue(selector, value);
				}

				function setJobTitleValue(value) {
					var selector = document.getElementById('addUserForm:jobTitleListbox');
					setSelectorValue(selector, value);
				}

				function setSelectorValue(selector, value) {
					if (selector == null) { 
						return;
					}
					value = value.replace('&amp;', '&');
					
					for (i = 0; i < selector.options.length; i++) {
						if (unescape(selector.options[i].text) == value) {
							selector.selectedIndex = i;
							break;
						}
					}
				}
				
				/* ]]> */
			</script>
		</ui:define>

		<!--  ADD   PopUp Modal Panel                    -->
		<ui:define name="modalPanels">
			<p:outputPanel  id="adduserModalAjaxPanel" autoUpdate="true">

				<p:dialog id="addModalPanel"
						  widgetVar="addUserModalPanelVar"
						  resizeable="true"
						  draggable="true" height="600" width="750"
						  rendered="#{userSummaryBean.renderAddUserModalPanel}"
						  visible="true" style="overflow:auto;">

					<f:facet name="header">
						<h:outputLabel value="Add User" />
					</f:facet>

					<!--   Messages                    -->
					<div>
						<h:messages id="msgId"
									fatalClass="fatalMessage"
									errorClass="errorMessage"
									warnClass="warningMessage"
									infoClass="infoMessage"
									layout="table" />
<!--						<rich:messages id="msgId" fatalClass="fatalMessage"-->
<!--							errorClass="errorMessage" warnClass="warningMessage"-->
<!--							infoClass="infoMessage" layout="table" />-->
					</div>

					<h:form id="addUserForm">
						<table width="100%">
							<tbody>
								<tr>
									<td align="left">
										<h:outputLabel value="Key Id" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="addkeyIdText" maxlength="30"
											value="#{userSummaryBean.keyId}" 
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="Last Name" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="addlastNameText" maxlength="50"
											value="#{userSummaryBean.lastName}" 
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="First Name" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="addfirstNameText" maxlength="50"
											value="#{userSummaryBean.firstName}" 
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="Middle Name" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="addmiddleNameText" maxlength="50"
											value="#{userSummaryBean.middleName}" 
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left">

										<h:outputLabel value="User Type" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="UserTypeListbox"
											value="#{userSummaryBean.userType}">
											<f:selectItems
												value="#{userSummaryBean.availableUserType}" />
										</h:selectOneMenu>
									</td>
									<td align="left">
										<h:outputLabel value="Status"
											styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="StatusNameText"
											value="#{userSummaryBean.status}" rendered="true">
											<f:selectItems
												value="#{userSummaryBean.availableStatus}" />
										</h:selectOneMenu>
									</td>
								</tr>

								<tr>
									<td align="left" colspan="4">
			<p:outputPanel id="supervisorPanel"  >

						<table width="100%">
							<tbody>
								<tr>

									<td align="left">
										<h:outputLabel value="Supervisor " styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="supervisorSelectOneMenu" 
											value="#{userSummaryBean.supervisorId}">
											<f:selectItems id="supervisorSelectItems"
												value="#{userSummaryBean.availableSupervisors}"/>
											<p:ajax event="change" update="supervisorPanel"
											action="#{userSummaryBean.populateSupevisorData}"/>
										</h:selectOneMenu>
									</td>
									<td align="left">
										<h:outputLabel value="Department" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="departmentListbox"
											value="#{userSummaryBean.departmentId}">
											<f:selectItems
												value="#{userSummaryBean.supevisorDepartments}" />
										</h:selectOneMenu>
									</td>
								</tr>

								<tr>
									<td align="left">

										<h:outputLabel value="Division" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="divisionListbox"
											value="#{userSummaryBean.divisionId}">
											<f:selectItems value="#{userSummaryBean.supevisorDivisions}" />
										</h:selectOneMenu>
									</td>
									<td align="left">
										<h:outputLabel value="Business Segment "
											styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="bsnssSgmntListbox"
											value="#{userSummaryBean.bsnssSgmnt}" rendered="true">
											<f:selectItems
												value="#{userSummaryBean.supevisorBusinessSegments}" />
										</h:selectOneMenu>
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="Location " styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="locationListbox"
											value="#{userSummaryBean.location}" rendered="true">
											<f:selectItems value="#{userSummaryBean.supevisorLocations}" />
										</h:selectOneMenu>
									</td>
									<td align="left">
										<h:outputLabel value="Job Title " styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="jobTitleListbox"
											value="#{userSummaryBean.jobTitle}" rendered="true">
											<f:selectItems value="#{userSummaryBean.supevisorJobTitles}" />
										</h:selectOneMenu>
									</td>
								</tr>
							</tbody>
						</table>
					</p:outputPanel>
									</td>
								</tr>


								<tr>
									<td align="center" colspan="4">

			<p:outputPanel id="directReportsPanel" ajaxRendered="true" >
				<p:dataTable  id="directReportsTable"
							  value="#{userSummaryBean.supervisorDirectReports}"
							  var="report" height="100" width="700" rows="3"
							  paginator="true"
							  paginatorPosition="bottom"
							  paginatorTemplate="{CurrentPageReport}  {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink}  {RowsPerPageDropdown}"
							  currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
							  rowsPerPageTemplate="10,25,50,100"
							  rowIndexVar="rowIndex"
							  rowKeyVar="rowKey"
							  scrollable="true"
							  styleClass="defaultTableHeader"
							  rowStyleClasses="oddRow, evenRow" >

					<p:column>
			 			<f:facet name="header"> Name </f:facet>
										#{report.name} 
						</p:column>
					<p:column>
						<f:facet name="header"> Department </f:facet>	
						<a href="#" onmouseup="setDepartmentValue(this.innerHTML);">#{report.departmentNmCostCenter}</a>
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
										<h:outputLabel value="Email Address " styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="emailText" maxlength="50"
											value="#{userSummaryBean.emailAddress}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="Phone " styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="phoneText" maxlength="50"
											value="#{userSummaryBean.phone}" 
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
							
								<tr>
									<td align="left">
										<h:outputLabel value="MF ID" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="satMFIdText" maxlength="50"
											value="#{userSummaryBean.satMFId}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="CF ID" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="satCFIdText" maxlength="50"
											value="#{userSummaryBean.satCFId}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="GF ID" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="satGFIdText" maxlength="50"
											value="#{userSummaryBean.satGFId}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="LCS ID" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="satLCSIdText" maxlength="50"
											value="#{userSummaryBean.satLCSId}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="FDMSId" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="satFDMSIdText" maxlength="50"
											value="#{userSummaryBean.satFDMSId}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="SiteMinderId" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="satSiteMinderIdText" maxlength="50"
											value="#{userSummaryBean.satSiteMinderId}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="Alternate Id1" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="satAltId1Text" maxlength="50"
											value="#{userSummaryBean.satAltId1}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="Alternate Id2" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="satAltId2Text" maxlength="50"
											value="#{userSummaryBean.satAltId2}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="Alternate Id3" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="satAltId3Text" maxlength="50"
											value="#{userSummaryBean.satAltId3}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="SAT Status" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="satStatusText" maxlength="20" tabindex="0"
											value="#{userSummaryBean.satStatus}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>

								<tr>
									<td align="left">
										<h:outputLabel value="SAT Comment" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="satCommentText" maxlength="50"
											value="#{userSummaryBean.satComment}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="SAT Job Role" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="satJobRoleText" maxlength="50"
											value="#{userSummaryBean.satJobRole}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left">
										<p:outputPanel id="saveCancelPanel" autoUpdate="true" >
										<h:commandButton id="saveButton" value="Save"
											action="#{userSummaryBean.doAddUser}">
											<f:ajax render="adduserModalAjaxPanel" />
										</h:commandButton>
										<h:commandButton id="cancelButton" value="Cancel" immediate="true"
											action="#{userSummaryBean.doCancelAddUser}">
											<f:ajax render="adduserModalAjaxPanel" />
										</h:commandButton>
										</p:outputPanel>
									</td>
								</tr>
							</tbody>
						</table>
					</h:form>
				</p:dialog>
			</p:outputPanel>

<!-- Update -->
			<p:outputPanel autoUpdate="true" id="updateUserModalAjaxPanel">

				<p:dialog id="updateModalPanel"
						  resizeable="true"
						  draggable="true"
						  height="500" width="750"
						  rendered="#{userSummaryBean.renderUpdateUserModalPanel}"
						  visible="true"
						  style="overflow:auto;">

					<f:facet name="header">
						<h:outputLabel value="Update User" />
					</f:facet>

					<!--   Messages                    -->
					<div>
						<h:messages id="updatemsgId"
									fatalClass="fatalMessage"
									errorClass="errorMessage"
									warnClass="warningMessage"
									infoClass="infoMessage"
									layout="table" />
					</div>

					<h:form id="updateUserForm">
						<table width="100%">
							<tbody>
								<tr>
									<td align="left">
										<h:outputLabel value="Key Id" styleClass="fieldLabel" />
									</td>
									<td align="left">
										#{userSummaryBean.keyId}
									</td>
									<td align="left">
										<h:outputLabel value="Last Name" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatelastNameText" maxlength="50"
											value="#{userSummaryBean.lastName}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="First Name" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatefirstNameText" maxlength="50"
											value="#{userSummaryBean.firstName}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="Middle Name" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatemiddleNameText" maxlength="50"
											value="#{userSummaryBean.middleName}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left">

										<h:outputLabel value="User Type" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="updateUserTypeListbox"
											value="#{userSummaryBean.userType}">
											<f:selectItems
												value="#{userSummaryBean.availableUserType}" />
										</h:selectOneMenu>
									</td>
									<td align="left">
										<h:outputLabel value="Status"
											styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="updateStatusNameText"
											value="#{userSummaryBean.status}" rendered="true">
											<f:selectItems
												value="#{userSummaryBean.availableStatus}" />
										</h:selectOneMenu>
									</td>
								</tr>
								
								<tr>
									<td align="left">
										<h:outputLabel value="Supervisor " styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="supervisorSelectOneMenu" 
											value="#{userSummaryBean.supervisorId}">
											<f:selectItems id="supervisorSelectItems"
												value="#{userSummaryBean.availableSupervisors}"/>
											<p:ajax event="change"
													listener="#{userSummaryBean.populateSupevisorData}"
													update="@form" />
										</h:selectOneMenu>
									</td>
									<td align="left">
										<h:outputLabel value="Department" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="updateDepartmentListbox"
											value="#{userSummaryBean.departmentId}">
											<f:selectItems
												value="#{userSummaryBean.supevisorDepartments}" />
										</h:selectOneMenu>
									</td>
								</tr>
								<tr>
									<td align="left">

										<h:outputLabel value="Division" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="updateDivisiontListbox"
											value="#{userSummaryBean.divisionId}">
											<f:selectItems value="#{userSummaryBean.supevisorDivisions}" />
										</h:selectOneMenu>
									</td>
									<td align="left">
										<h:outputLabel value="Cost Center " styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="updateCostCenterbox"
											value="#{userSummaryBean.costCenter}" rendered="true">
											<f:selectItems
												value="#{userSummaryBean.supevisorCostCenters}" />
										</h:selectOneMenu>
									</td>
								</tr>

								<tr>
									<td align="left">
										<h:outputLabel value="Job Title " styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="updatejobTitleListbox"
											value="#{userSummaryBean.jobTitle}" rendered="true">
											<f:selectItems value="#{userSummaryBean.supevisorJobTitles}" />
										</h:selectOneMenu>
									</td>
									<td align="left">
										<h:outputLabel value="Location " styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="updatelocationListbox"
											value="#{userSummaryBean.location}" rendered="true">
											<f:selectItems value="#{userSummaryBean.supevisorLocations}" />
										</h:selectOneMenu>
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="Business Segment "
											styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:selectOneMenu id="updatebsnssSgmntListbox"
											value="#{userSummaryBean.bsnssSgmnt}" rendered="true">
											<f:selectItems
												value="#{userSummaryBean.supevisorBusinessSegments}" />
										</h:selectOneMenu>
									</td>
									<td align="left">
									</td>
									<td align="left">
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="Email Address " styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updateemailText" maxlength="50"
											value="#{userSummaryBean.emailAddress}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="Phone " styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatephoneText" maxlength="50"
											value="#{userSummaryBean.phone}" />
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="MF ID" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatesatMFIdText" maxlength="50"
											value="#{userSummaryBean.satMFId}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="CF ID" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatesatCFIdText" maxlength="50"
											value="#{userSummaryBean.satCFId}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="GF ID" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatesatGFIdText" maxlength="50"
											value="#{userSummaryBean.satGFId}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="LCS ID" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatesatLCSIdText" maxlength="50"
											value="#{userSummaryBean.satLCSId}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="FDMSId" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatesatFDMSIdText" maxlength="50"
											value="#{userSummaryBean.satFDMSId}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="SiteMinderId" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatesatSiteMinderIdText" maxlength="50"
											value="#{userSummaryBean.satSiteMinderId}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="Alternate Id1" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatesatAltId1Text" maxlength="50"
											value="#{userSummaryBean.satAltId1}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="Alternate Id2" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatesatAltId2Text" maxlength="50"
											value="#{userSummaryBean.satAltId2}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="Alternate Id3" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatesatAltId3Text" maxlength="50"
											value="#{userSummaryBean.satAltId3}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="SAT Status" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatesatStatusText" maxlength="20" tabindex="0"
											value="#{userSummaryBean.satStatus}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="SAT Comment" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatesatCommentText" maxlength="50"
											value="#{userSummaryBean.satComment}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
									<td align="left">
										<h:outputLabel value="SAT Job Role" styleClass="fieldLabel" />
									</td>
									<td align="left">
										<h:inputText id="updatesatJobRoleText" maxlength="50"
											value="#{userSummaryBean.satJobRole}"
											onchange="this.value = this.value.toUpperCase();" />
									</td>
								</tr>
								<tr>
									<td align="left" colspan="4">
										<h:commandButton id="updatesaveButton" value="Save"
											action="#{userSummaryBean.doUpdate}" />
										<h:commandButton id="updatecancelButton" value="Cancel"
										 immediate="true" 
											action="#{userSummaryBean.doCancelUpdate}" />
									</td>
								</tr>
							</tbody>
						</table>
					</h:form>
				</p:dialog>
			</p:outputPanel>

<!-- Delete user modal panel -->

		<!--  Verify Delete Modal Panel                    -->
		<p:outputPanel autoUpdate="true" id="deletedUserModalAjaxPanel">
			<p:dialog id="deleteConfirmModalPanel"
					  resizeable="true"
					  draggable="true"
					  height="350" width="450"
					  rendered="#{userSummaryBean.renderDeleteUserModalPanel}"
					  visible="true"
					  styleClass="defaultTableHeader, oddRow, evenRow" >

				<f:facet name="header">
					<h:outputLabel value="Delete Row(s)" />
				</f:facet>
				<h:form id="deleteConfirmForm">
					<div class="verticalSpacer" />
					<div>
						<div class="sectionHeaderSmall">
							Delete User(s)?
						</div>
					</div>
					<h:dataTable id="selectDeletedRowsTable"
						value="#{userSummaryBean.selectedUserList}" var="user"
						headerClass="defaultTableHeader" rowClasses="oddRow, evenRow" >
						<p:column>
							<f:facet name="header">
								<h:outputText value="Key Id"></h:outputText>
							</f:facet>#{user.keyId}</p:column>


						<p:column>
							<f:facet name="header">
								<h:outputText value="User Id"></h:outputText>
							</f:facet>#{user.userId}</p:column>
						<p:column>
							<f:facet name="header">
								<h:outputText value="User Name"></h:outputText>
							</f:facet>#{user.name}</p:column>

					</h:dataTable>
					<div>
						<h:commandButton id="deleteButton" value="Delete"
							action="#{userSummaryBean.doDelete}" />

						<h:commandButton id="cancelDeleteButton" value="Cancel"
							action="#{userSummaryBean.doCancelDelete}" />
					</div>
				</h:form>
			</p:dialog>
		</p:outputPanel>

<!--  End Delete modal panel -->
			
<!--  Bulk update functionality -->
			<!--  Verify BulkUpdate Modal Panel                    -->
			<p:outputPanel autoUpdate="true" id="bulkUpdateModalAjaxPanel">
				<p:dialog id="bulkUpdateConfirmModalPanel"
						  resizeable="true"
						  draggable="true"
						  height="500" width="850"
						  rendered="#{userSummaryBean.renderBulkUpdatePanel}"
						  visible="true" >

					<f:facet name="header">
						<h:outputLabel value="Update Row(s)" />
					</f:facet>

					<h:form id="bulkUpdateConfirmForm">
						<div class="verticalSpacer" />
						<div>
							<div class="sectionHeaderSmall">
								Bulk Update user
							</div>
						</div>
			<!--  display in editable mode Panel                    -->

					<div class="verticalSpacer"></div>

						<!--  update fields -->
				<table width="800">
					<tbody>
						<tr>
							<td align="left">
								<h:outputLabel id="SupervisorSearchLabel">Supervisor:</h:outputLabel>
							</td>
							<td align="left">
								<h:selectOneMenu id="searchSupervisorSelectOneMenu"
									value="#{userSummaryBean.bulkSupervisorId}">
									<f:selectItems id="searchSupervisorSelectItems"
										value="#{userSummaryBean.availableSupervisors}"/>
								</h:selectOneMenu>
							</td>

							<td align="left">
								<h:outputLabel id="selectDepartmentLabel">Department:</h:outputLabel>
							</td>
							<td align="left">
								<h:selectOneMenu id="DepartmentListbox"
									value="#{userSummaryBean.bulkDepartmentId}">
									<f:selectItems value="#{userSummaryBean.availableDepartment}" />
								</h:selectOneMenu>
							</td>
						</tr>
						<tr>
							<td align="left">
								<h:outputLabel id="selectStatusLabel">Status: </h:outputLabel>
							</td>
							<td align="left">
								<h:selectOneMenu id="StatusListbox"
									value="#{userSummaryBean.bulkStatus}">
									<f:selectItems value="#{userSummaryBean.availableStatus}" />
								</h:selectOneMenu>
							</td>
							<td align="left">
								<h:outputLabel id="selectDivisionLabel">Division: </h:outputLabel>
							</td>
							<td align="left">
								<h:selectOneMenu id="DivisiontListbox"
									value="#{userSummaryBean.bulkDivisionId}">
									<f:selectItems value="#{userSummaryBean.availableDivision}" />
								</h:selectOneMenu>
							</td>
						</tr>
						<tr>
							<td align="left">

								<h:outputLabel id="userTypeLabel">User Type: </h:outputLabel>
							</td>
							<td align="left">
								<h:selectOneMenu id="UserTypeListbox"
									value="#{userSummaryBean.bulkUserType}">
									<f:selectItems value="#{userSummaryBean.availableUserType}" />
								</h:selectOneMenu>
							</td>
							<td align="left">
								<h:outputLabel id="businessSgmntLabel">Business Segment:  </h:outputLabel>
							</td>
							<td align="left">
								<h:selectOneMenu id="businessSgmnt"
									value="#{userSummaryBean.bulkBsnssSgmnt}">
									<f:selectItems value="#{userSummaryBean.availableBusinessSeg}" />
								</h:selectOneMenu>
							</td>
						</tr>
						<tr>
							<td colspan="3" align="right">
								<h:outputLabel id="costCenterLabel">Cost Center: </h:outputLabel>
							</td>
							<td align="left">
								<h:selectOneMenu id="costCenterText"
									value="#{userSummaryBean.bulkCostCenter}">
									<f:selectItems value="#{userSummaryBean.availableCostCenter}" />
								</h:selectOneMenu>
							</td>
						</tr>
						</tbody>
						</table>


<!-- data table to show selected items -->
							<p:dataTable id="bulkuserTable"
										 value="#{userSummaryBean.bulkUpdateList}"
										 var="user" rows="10"

										 styleClass="defaultTableHeader"
										 rowStyleClasses="oddRow, evenRow" >
								<p:column id="updateCheckBoxColumn"
								rendered="#{userSummaryBean.updateCheckbox}">
								<f:facet name="header">
								Select
								</f:facet>
								<h:selectBooleanCheckbox id="UserCheckBox"
								value="#{user.checked}"></h:selectBooleanCheckbox>
								</p:column>
								<p:column>
									<f:facet name="header">User Id
									</f:facet>
										#{user.userId}
								</p:column>
								<p:column>
									<f:facet name="header">Key Id
									</f:facet>
										#{user.keyId}
								</p:column>
								<p:column>
									<f:facet name="header">Name
									</f:facet>
										#{user.name}
								</p:column>
								<p:column>
									<f:facet name="header">User Type
									</f:facet>
										#{user.userType}
								</p:column>
								<p:column>
									<f:facet name="header">Status
									</f:facet>
										#{user.userStatusDescription}
								</p:column>
								<p:column>
									<f:facet name="header">Supervisor
									</f:facet>
										#{user.supervisorName}
								</p:column>
								<p:column>
									<f:facet name="header">Department/Division/Business Segment
									</f:facet>
										#{user.deptDivisionBusinessSeg}
								</p:column>
								<p:column>
									<f:facet name="header">CostCenter
									</f:facet>
										#{user.costCenter}
								</p:column>

						</p:dataTable>

<!-- end data table to show selected items -->


						<div>
							<h:commandButton id="bulkUpdateButton" value="Update"
								action="#{userSummaryBean.doBulkUpdate}" />

							<h:commandButton id="cancelBulkUpdateButton" value="Cancel"
								action="#{userSummaryBean.doCancelBulkUpdate}" />
						</div>

			<!--  display in editable mode Panel                    -->

					</h:form>
				</p:dialog>
			</p:outputPanel>

<!--  end  -->

			
<!--  View Panel -->

		<!-- **************************    View ModelPanel     ***********************************   -->


		<p:outputPanel autoUpdate="true" id="viewModalAjaxPanel">
			<p:dialog id="viewModalPanel"
					  resizeable="true"
					  draggable="true"
					  height="420" width="650"
					  rendered="#{userSummaryBean.renderViewPanel}"
					  visible="true">

				<f:facet name="header">
					<h:outputLabel value="View User" />
				</f:facet>
				<f:facet name="controller">
					<h:panelGroup>
						<p:commandLink id="hidelinkFour" style="float: left" value="close"
									   oncomplete="PF('viewModalAjaxPanel').hide();" />
					</h:panelGroup>
				</f:facet>

				<!--   Messages                    -->
				<div>
					<h:messages id="viewModalMsg"
								fatalClass="fatalMessage"
								errorClass="errorMessage"
								warnClass="warningMessage"
								infoClass="infoMessage"
								layout="table" />
<!--					<rich:messages id="viewModalMsg" fatalClass="fatalMessage"-->
<!--						errorClass="errorMessage" warnClass="warningMessage"-->
<!--						infoClass="infoMessage" layout="table" />-->
				</div>

				<h:form id="viewModalForm">
						<div align="right"  class="sectionHeaderSmall">
							<p:commandLink value="Close"
								action="#{userSummaryBean.closeViewUserPanel}" >
							</p:commandLink>
						</div>
					<table align="left">
						<tbody>
							<tr>
								<td align="left">
									<h:outputLabel value="User Id " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.userId}
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Key Id " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.keyId}
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="First Name " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.firstName}
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Middle Name" styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.middleName}
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Last Name " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.lastName}
								</td>
							</tr>

							<tr>
								<td align="left">
									<h:outputLabel value="User Type " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.userTypeString}
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Department " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.department}
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Cost Center " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.costCenter}
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Division " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.division}
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Supervisor " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.supervisorName}
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Status " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.userStatusString}
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Location " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.location}
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Business Segment " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.bsnssSgmnt}
								</td>
							</tr>

							<tr>
								<td align="left">
									<h:outputLabel value="JobTitle " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.jobTitle}
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Phone " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.phone}
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="EmailAddress " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.emailAddress}
								</td>
							</tr>


						</tbody>
					</table>
					<table align="left">
					<tbody>
						<tr>
							<td align="left" colspan="2">
								<h:outputLabel value="Existing Alternate Id(s) " styleClass="fieldLabel" />
							</td>
						</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="MF ID" styleClass="fieldLabel" />
									</td>
									<td align="left">
										#{userSummaryBean.satMFId}
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="CF ID" styleClass="fieldLabel" />
									</td>
									<td align="left">
										#{userSummaryBean.satCFId}
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="GF ID" styleClass="fieldLabel" />
									</td>
									<td align="left">
										#{userSummaryBean.satGFId}
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="LCS ID" styleClass="fieldLabel" />
									</td>
									<td align="left">
										#{userSummaryBean.satLCSId}
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="FDMSId" styleClass="fieldLabel" />
									</td>
									<td align="left">
										#{userSummaryBean.satFDMSId}
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="SiteMinderId" styleClass="fieldLabel" />
									</td>
									<td align="left">
										#{userSummaryBean.satSiteMinderId}
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="Alternate Id1" styleClass="fieldLabel" />
									</td>
									<td align="left">
										#{userSummaryBean.satAltId1}
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="Alternate Id2" styleClass="fieldLabel" />
									</td>
									<td align="left">
										#{userSummaryBean.satAltId2}
									</td>
								</tr>
								<tr>
									<td align="left">
										<h:outputLabel value="Alternate Id3" styleClass="fieldLabel" />
									</td>
									<td align="left">
										#{userSummaryBean.satAltId3}
									</td>
								</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Sat Status " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.satStatus}
								</td>
							</tr>
							<tr>
								<td align="left">
									<h:outputLabel value="Sat Comment " styleClass="fieldLabel" />
								</td>
								<td align="left">
									#{userSummaryBean.satComment}
								</td>
							</tr>
							</tbody>

					</table>

					
				</h:form>
			</p:dialog>
		</p:outputPanel>

<!--  end  -->

		</ui:define>
	</ui:composition>
</html>
