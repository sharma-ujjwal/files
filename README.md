```
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
										update="supervisorListbox departmentListbox divisionListbox bsnssSgmntListbox
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
