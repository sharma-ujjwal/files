```
this is pendingReportsComponent.xhtml
<html xmlns="http://www.w3.org/1999/xhtml"
	  xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
	  xmlns:h="http://xmlns.jcp.org/jsf/html"
	  xmlns:f="http://java.sun.com/jsf/core"
	  xmlns:p="http://primefaces.org/ui">

<ui:composition>
	<p:toolbar id="pendingReviewersTableToolBar" itemSeparator="line">
		<p:toolbarGroup>
			<p:commandLink id="pendingReviewersApproveLink" value="Approve"
						   action="#{reviewDetailsBean.doApproveReviewers}"
						   update="bodyForm:headerMessages pendingReviewersList"
						   styleClass="undecoratedApproveLink"/>
		</p:toolbarGroup>
		<p:toolbarGroup>
			<p:commandLink id="pendingReviewersReassignLink" value="Reassign"
						   action="#{reviewDetailsBean.doPrepareReassignReviewersPanel}" styleClass="undecoratedReassignLink">
				<f:param name="#{reviewDetailsBean.actionSourceParamName}" value="#{reviewDetailsBean.pendingParam}"/>
			</p:commandLink>
		</p:toolbarGroup>
		<p:toolbarGroup>
			<p:commandLink id="pendingReviewersRejectLink"
						   value="Reject"
						   update="bodyForm:headerMessages rejectReviewerModalPanelAjax rejectReviewersModalPanelTable"
						   action="#{reviewDetailsBean.doPrepareRejectReviewersPanel}"
						   styleClass="undecoratedRejectLink" >
				<f:param name="#{reviewDetailsBean.actionSourceParamName}" value="#{reviewDetailsBean.pendingParam}"/>
				<f:param name="source" value="PEND" />
			</p:commandLink>
		</p:toolbarGroup>
	</p:toolbar>
	<p:outputPanel id="reviewPendingReviewersTablePanel">
		<p:dataTable id="pendingReviewersList"  value="#{reviewDetailsBean.myPendingTaskListBeanResults}"
					 var="myAllPendingTask" widgetVar="pendingReviewersListWidget"
					 rowIndexVar="rowIndex" rowKeyVar="rowKey">
			<p:column style="width: 80px !important;">
				<f:facet name="header">
					<h:panelGrid columns="1" style="width:80px; height: 12px;">
						<p:commandLink id="selectAllLinkPending" value="All/None"
									   style="color: #000 !important; margin-left: 5px; font-weight: 800; font-size: 11px;"
									   action="#{reviewDetailsBean.doSelectAllToggle}"
									   update="pendingReviewersList" >
							<f:param name="actionStatusCodeValue" value="PEND" />
						</p:commandLink>
					</h:panelGrid>
				</f:facet>
				<h:selectBooleanCheckbox value="#{myAllPendingTask.selected}">
					<p:ajax event="change" listener="#{reviewDetailsBean.onRowSelect}" />
				</h:selectBooleanCheckbox>
			</p:column>
			<p:column headerText="Bundle" sortable="true" field="bundleName">
				<h:outputText value="#{myAllPendingTask.bundleName}" />
			</p:column>
			<p:column headerText="" style="width: 56px">
				<h:outputLink>
					<h:graphicImage value="/images/ico_notice.gif" title="#{myAllPendingTask.reviewerStatus}"/>
				</h:outputLink>
			</p:column>
			<p:column rendered="#{reviewDetailsBean.columnType ne 'MNGR' and reviewDetailsBean.columnType ne 'SGDU'}"
					  sortable="true" field="ownerName" headerText="Data Owner">
				<h:outputText value="#{myAllPendingTask.ownerName}" />
			</p:column>
			<p:column headerText="Application" sortable="true" field="application">
				<h:outputText value="#{myAllPendingTask.application}" />
			</p:column>
			<p:column headerText="Reviewer" sortable="true" field="reviewerName">
				<h:commandLink id="commandLinkReviewerName"
							   value="#{myAllPendingTask.reviewer.reviewerName}"
							   action="#{reviewDetailsBean.doViewReviewerDetails}">
					<f:param name="actionReviewerId" value="#{myAllPendingTask.reviewerId}" />
					<f:param name="actionStatusCodeValue" value="PEND" />
				</h:commandLink>
			</p:column>
			<p:column rendered="#{reviewDetailsBean.columnType eq 'MNGR'}" headerText="Escalation Manager" sortable="true"
					  field="escalationMgrName">
				<h:outputText value="#{myAllPendingTask.escalationMgrName}" />
			</p:column>
			<p:column headerText="Division" sortable="true" field="reviewer.division">
				<h:outputText value="#{myAllPendingTask.reviewer.division}" />
			</p:column>
			<p:column rendered="#{reviewDetailsBean.columnType eq 'MNGR'}" headerText="# of Departments" sortable="true" field="department">
				<h:outputText value="#{myAllPendingTask.department}" />
			</p:column>
			<p:column headerText="# Direct Reports" sortable="true" field="reviewer.numberOfReviewedUsers">
				<h:outputText value="#{myAllPendingTask.reviewer.numberOfReviewedUsers}" />
			</p:column>
			<p:column rendered="#{reviewDetailsBean.columnType ne 'MNGR'}" headerText="# users in report" sortable="true" field="reviewer.numberOfReviewedUsers">
				<h:outputText value="#{myAllPendingTask.reviewer.numberOfReviewedUsers}" />
			</p:column>
			<p:column headerText="Date Created" sortable="true" field="createdDate">
				<h:outputText value="#{myAllPendingTask.createdDate}">
					<f:convertDateTime pattern="MM/dd/yyyy" />
				</h:outputText>
			</p:column>
			<p:column rendered="#{reviewDetailsBean.columnType eq 'SGDU'}"
					  sortable="true" field="reviewerDTO.ownerName" headerText="SOD Owner" >
				<h:outputText value="#{myAllPendingTask.reviewerDTO.ownerName}" />
			</p:column>
		</p:dataTable>
		<p:toolbar id="bottomPendingReviewersTableToolBar" itemSeparator="line">
			<p:toolbarGroup>
				<p:commandLink id="bottomPendingReviewersApproveLink" value="Approve"
							   update="bodyForm:headerMessages pendingReviewersList"
							   action="#{reviewDetailsBean.doApproveReviewers}" styleClass="undecoratedApproveLink"
							   oncomplete="window.scrollTo(0, 0);"/>
			</p:toolbarGroup>
			<p:toolbarGroup>
				<p:commandLink id="bottomPendingReviewersReassignLink" value="Reassign"
							   action="#{reviewDetailsBean.doPrepareReassignReviewersPanel}"
							   styleClass="undecoratedReassignLink"
							   oncomplete="window.scrollTo(0, 0);" >
					<f:param name="#{reviewDetailsBean.actionSourceParamName}" value="#{reviewDetailsBean.pendingParam}"/>
				</p:commandLink>
			</p:toolbarGroup>
			<p:toolbarGroup>
				<p:commandLink id="bottomPendingReviewersRejectLink" value="Reject"
							   action="#{reviewDetailsBean.doPrepareRejectReviewersPanel}"
							   styleClass="undecoratedRejectLink"
							   update="bodyForm:headerMessages rejectReviewerModalPanelAjax pendingReviewersList">
					<f:param name="#{reviewDetailsBean.actionSourceParamName}" value="#{reviewDetailsBean.pendingParam}"/>
					<f:param name="source" value="PEND" />
				</p:commandLink>
			</p:toolbarGroup>
		</p:toolbar>
	</p:outputPanel>
	<ui:define name="modalPanels">
		<p:outputPanel autoUpdate="true" id="rejectReviewerModalPanelAjax">
			<ui:include src="rejectReviewersModalPanelComponent.xhtml">
				<ui:param name="tab" value="PEND" />
			</ui:include>
		</p:outputPanel>
	</ui:define>
</ui:composition>
</html>

this is rejectReviewersModalPanelComponent.xhtml
<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://java.sun.com/jsf/facelets"
	xmlns:h="http://xmlns.jcp.org/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	xmlns:p="http://primefaces.org/ui">
 
	<ui:composition>
		<p:dialog id="rejectReviewersModalPanel" resizeable="true"  moveable="true" height="525" width="450"
			widgetVar="rejectReviewersModalPanelWidget" visible="#{reviewDetailsBean.renderRejectReviewerModalPanel}" modal="true">
			<f:facet name="header">
				<h:outputLabel value="Reject Reviewers" />
			</f:facet>
			<div>
				<h:messages id="rejectReviewersModalPanelMessages" fatalClass="fatalMessage" errorClass="errorMessage" 
					warnClass="warningMessage" infoClass="infoMessage" layout="table"/>
			</div>
			<div class="verticalSpacer"/>
			<div>
				<h:outputLabel styleClass="sectionHeaderSmall" 
					value="Please provide a reason and comment for the following rejection."/>
			</div>
			<div class="verticalSpacer"/>
			<div><h:outputLabel value="Reviewer(s):" styleClass="fieldLabel" /></div>
			<div style="overflow-y: scroll; height: 175px">
				<h:dataTable id="rejectReviewersModalPanelTable" value="#{reviewDetailsBean.selectedReviewers}"
					var="reviewer">
					<h:column>
						<h:outputText value="#{reviewer.currentReviewerName}"/>
					</h:column>
				</h:dataTable>
			</div>
			<div class="verticalSpacer"/>
			<h:form id="rejectReviewersCommentForm">
				<div>
					<h:outputLabel value="Reason:" styleClass="fieldLabel"/>
				</div>
				<div>
					<h:selectOneMenu id="rejectReviewerModalPanelReason" value="#{reviewDetailsBean.selectReasonInput}">
						<f:selectItems value="#{applicationBean.availableReviewerRejectReasons}"/>
					</h:selectOneMenu>
				</div>
				<div class="verticalSpacer"/>
				<div>
					<h:outputLabel value="Comments:" styleClass="fieldLabel" />
				</div>
				<div>
					<h:inputTextarea id="rejectReportsModalComments" value="#{reviewDetailsBean.commentsInput}" 
						cols="50" rows="5"/>
				</div>
				<div>
					<h:commandButton id="rejectReportCommentModalPanelSubmitButtonInvisible" value="Submit"
									 action="#{reviewDetailsBean.doSaveRejectReviewersPanel}" />

					<h:commandButton id="rejectReportCommentModalPanelCancelButton" value="Cancel" 
						action="#{reviewDetailsBean.doCancelRejectReviewersPanel}" />
				</div>
			</h:form>
		</p:dialog>
	</ui:composition>
</html>


public String getActionSourceValue() {
			String source = FacesContext.getCurrentInstance()
					.getExternalContext()
					.getRequestParameterMap()
					.get("source");
		if (source.equalsIgnoreCase("PEND")) {
			return "PENDING";
		} else if (source.contains("APPR")) {
			return "APPROVED";
		}
		return null; // Default or error case
	}

I get null in source value. why this is happening. the flow is first I see the datatable present in pendingReportsComponent, then when I click on Reject button, a popup comes, then after submit button inside doSaveRejectReviewersPanel() method I am calling getActionSourceValue() where I am geeting null as source. how to fix this
