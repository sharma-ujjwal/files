```
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
										update="bodyForm:tabPanel:addUserForm:directReportsTable bodyForm:headerMessages"/>
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
											 update="adduserModalAjaxPanel bodyForm:headerMessages"
											 oncomplete="PF('addModalPanelWidget').hide()" />
						</td>
					</tr>
					</tbody>
				</table>
			</p:dialog>
		</p:outputPanel>
	</h:form>
