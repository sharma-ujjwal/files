```
		<p:outputPanel id="accessListOutputPanel">
			<ui:repeat id="repeatUIId" value="#{accessListBean.reviewUserApplicationAccessesDTOs}" var="group">
				<p:panel id="accessListPanel" styleClass="accessListPanel" style="padding: 0px !important;">
					<f:facet name="header">
						<h:panelGrid id="panelGridId" columns="2" styleClass="#{accessListBean.review.SODReview ? 'defaultTableHeaderSOD' : 'reviewRepGridCls'}">
							<h:outputText id="applicationNameId" value="Application Name: #{group.applicationName}"/>
							<h:outputText id="userIdPanelId" value="User ID: #{group.applicationUserId}" style="float: right; font-weight: inherit;"/>
						</h:panelGrid>
					</f:facet>
					<p:dataTable id="privilegeTable"
								 value="#{group.accesses}"
								 rowIndexVar="rowStatus"
								 var="priv"
								 rowStyleClass="#{accessListBean.review.SODReview ? (rowStatus % 2 == 0 ? 'oddRowSOD' : 'evenRowSOD') : ''}"
								 style="width: 100% !important; margin-left: 0px !important; margin-top: 0px !important;" >
						<p:column id="sourceNameColumn" headerText="Source Name" style="width: 10%; background-color: #{rowStatus % 2 == 0 ? '#ffffff' : '#FFE5E6'}!important;">
							<h:outputText value="#{priv.sourceName}"/>
						</p:column>
						<p:column id="privilegeDescriptionColumn" headerText="Privilege Description" style="width: 20%; background-color: #{rowStatus % 2 == 0 ? '#ffffff' : '#FFE5E6'}!important;">
							<h:outputText value="#{priv.privilegeDescription}"/>
						</p:column>
						<p:column id="privilegeValueColumn" headerText="Privilege Value" style="width: 20%; background-color: #{rowStatus % 2 == 0 ? '#ffffff' : '#FFE5E6'}!important;">
							<h:outputText value="#{priv.privilegeValue}"/>
						</p:column>
						<p:column id="privilegeIdColumn" headerText="Privilege ID" style="width: 60%; background-color: #{rowStatus % 2 == 0 ? '#ffffff' : '#FFE5E6'}!important;">
							<h:outputText value="#{priv.privilegeComment}"/>
						</p:column>
						<p:column id="soxConcernColumn" headerText="SOX Concern" rendered="#{accessListBean.review.notManagerReview}" style="width: 5%; background-color: #{rowStatus % 2 == 0 ? '#ffffff' : '#FFE5E6'}!important;">
							<h:outputText value="#{priv.soxConcern.soxConcernCode}" style="text-align:center;"/>
						</p:column>
						<p:column style="text-align:center; width: 10%; background-color: #{rowStatus % 2 == 0 ? '#ffffff' : '#FFE5E6'}!important;"
								  headerText="Review Status" id="reviewStatusColumn"
								  rendered="#{accessListBean.editMode or accessListBean.viewEdits or accessListBean.printMode}" >
							<f:facet name="header">
								<h:panelGroup  layout="block" styleClass="reviewStatusHeader">
									<h:outputText value="Review Status" style="font-weight: 600; display: block; margin-bottom: 5px;"
												rendered="#{accessListBean.viewEdits or accessListBean.editMode}"/>
									<p:panelGrid columns="2" cellpadding="2" rendered="#{accessListBean.editMode}">
										<h:commandButton value="All"
														 action="#{accessListBean.setAllPrivilegeValues(group.accesses, 'KEEP')}" >
											<f:ajax execute="@this" render="@form privilegeTable" />
										</h:commandButton>
										<h:commandButton value="None"
														 action="#{accessListBean.setAllPrivilegeValues(group.accesses, 'REMOVE')}" >
											<f:ajax execute="@this" render="@form privilegeTable" />
										</h:commandButton>
									</p:panelGrid>
								</h:panelGroup>
							</f:facet>
							<h:panelGroup id="reviewRadioPanel" rendered="#{accessListBean.editMode}">
								<h:selectOneRadio id="reviewRadioButton" value="#{priv.keepRemoveFlag}">
									<f:selectItems value="#{accessListBean.keepRemoveOptions}" var="option"
												   itemLabel="#{option.label}" itemValue="#{option.value}" />
									<p:ajax event="change"
											listener="#{accessListBean.statusChanged(priv)}"
											update="@this" />
								</h:selectOneRadio>
							</h:panelGroup>
							<p:panelGrid id="keepRemovePanelGrid"
										 rendered="#{accessListBean.viewEdits or accessListBean.printMode}"
										 styleClass="keepRemovePanelGridClass">
								<h:outputText id="keepRemoveId"
											  value="#{priv.keepRemoveDisplay}"
											  styleClass="#{priv.keepRemoveClass} testClassId reviewRepKeepCls" />
							</p:panelGrid>
						</p:column>
					</p:dataTable>
					<h:panelGroup>
						<h:outputLabel id="ApplicationCommentLabel" value="Comments: " rendered="#{accessListBean.editMode or accessListBean.viewEdits or accessListBean.printMode}" />
						<h:outputText id="applicationCommentText" value="#{group.comment}" rendered="#{accessListBean.viewEdits or accessListBean.printMode}" />
						<h:inputText id="ApplicationCommentText" value="#{group.comment}" rendered="#{accessListBean.editMode}">
							<p:ajax event="change" listener="#{accessListBean.updateComment(group)}" update="ApplicationCommentText" />
						</h:inputText>
					</h:panelGroup>
					<h:panelGroup>
						<h:graphicImage id="ApplicationSpacer"
										height="15px"
										width="15px"
										style="display: none;" />
					</h:panelGroup>
				</p:panel>
			</ui:repeat>
		</p:outputPanel>
