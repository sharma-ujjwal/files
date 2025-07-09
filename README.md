```
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
											 oncomplete="PF('searchAssociatedUserModalVar').show();"
											 update="associatedUserSearchModalAjaxPanel bodyForm:tabPanel:associatedUserForm:searchAssociatedUserTable"/>
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
