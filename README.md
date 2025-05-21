```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
	  xmlns:ui="http://java.sun.com/jsf/facelets"
	  xmlns:h="http://xmlns.jcp.org/jsf/html"
	  xmlns:f="http://java.sun.com/jsf/core"
	  xmlns:p="http://primefaces.org/ui">

<ui:composition template="../common/main.xhtml">
	<ui:define name="body">
		<script type="text/javascript">

			function setRadios(parentElementId, radioValue) {
				// find the element that contains the subtree with all the radios to set
				let parentElement = document.getElementById(parentElementId);
				// find all the input elements in the subtree of the parent element
				let inputs = parentElement.getElementsByTagName('input');
				// loop over each input element
				for (let i = 0; i &lt; inputs.length; i++) {
					let input = inputs[i];
					// is it of type radio?
					if (input.type === 'radio') {
						// is it the right radio for the given value to set?
						if (input.value === radioValue) {
							// check it
							input.checked = true;
						}
					}
				}
			}
		</script>

		<h:outputLabel styleClass="sectionHeader" value="Report Details"/>

		<p:ajaxStatus>
			<f:facet name="start">
				<h:graphicImage value="/images/loader.gif"/>
			</f:facet>
		</p:ajaxStatus>

		<ui:include src="headerComponent.xhtml">
			<ui:param name="backingBeanName" value="#{accessListBean}"/>
		</ui:include>

		<div class="verticalSpacer"/>

		<p:toolbar id="approvedReviewersTableToolBar">
			<p:toolbarGroup location="left">
				<h:commandLink styleClass="listitem" id="approvedReviewersDistributeLink" value="Return to List"
							   action="#{accessListBean.doReturnToEmployeeList}"/>
				<h:outputLabel styleClass="headerTextValue" value="  "/>
				<h:commandLink id="accessListRejectLink"
							   value="Reject Employee"
							   action="#{accessListBean.doPrepareRejectUserPanel}"
							   styleClass="undecoratedRejectLink"
							   rendered="#{accessListBean.editMode}"/>
			</p:toolbarGroup>
			<p:toolbarGroup>
				<h:commandLink id="printAccessCommandLink"
							   action="#{accessListBean.doPrintAccessList}"
							   value="Printable Version"
							   styleClass="print"
							   onmousedown="document.forms['bodyForm'].target='_blank'"/>
			</p:toolbarGroup>
			<p:toolbarGroup location="right">
				<h:commandLink styleClass="previousitem" id="previousEmployeeLink"
							   action="#{accessListBean.doRetrievePreviousEmployee}"
							   value="Previous Employee"/>
				<h:outputLabel styleClass="headerTextValue" value="  "/>
				<h:commandLink styleClass="nextitem" id="nextEmployeeLink"
							   action="#{accessListBean.doRetrieveNextEmployee}"
							   value="Next Employee"/>
			</p:toolbarGroup>
		</p:toolbar>

		<table style="margin: 10px 0px 10px 0px; width: 100%">
			<tr>
				<td align="left">
					<h:outputLabel styleClass="headerTextValue" value="#{accessListBean.numberOfRecords}"/>
					<h:outputLabel styleClass="headerTextValue" value=" - "/>
					<h:outputLabel styleClass="headerTextValue" value="#{accessListBean.userName}"/>
				</td>
				<td align="center">
					<h:outputLabel styleClass="headerTextValue" value="Employee Status:"/>
					<h:outputLabel styleClass="headerTextValue" value="#{accessListBean.userStatus}"/>
				</td>
				<td align="center">
					<h:outputLabel styleClass="headerTextValue" value="Employee Manager:" rendered="#{accessListBean.review.notManagerReview}"/>
					<h:outputLabel styleClass="headerTextValue" value="#{accessListBean.employeeManager}" rendered="#{accessListBean.review.notManagerReview}"/>
				</td>
				<td align="right">
					<h:outputLabel styleClass="headerTextValue" value="Division:"/>
					<h:outputLabel styleClass="headerTextValue" value="#{accessListBean.userDivision}"/>
				</td>
				<td align="right">
					<h:outputLabel styleClass="headerTextValue" value="Department:"/>
					<h:outputLabel styleClass="headerTextValue" value="#{accessListBean.userDepartment}"/>
				</td>
			</tr>
		</table>

		<h:form id="accessListForm">
			<ui:repeat value="#{accessListBean.reviewUserApplicationAccessesDTOs}" var="group">
				<p:panel styleClass="myPanelStyle">
					<f:facet name="header">
						<h:panelGrid columns="2" style="width:100%">
							<h:outputText value="Application Name: #{group.applicationName}"/>
							<h:outputText value="User ID: #{group.applicationUserId}" style="float: right; font-weight: inherit;"/>
						</h:panelGrid>
					</f:facet>
					<p:dataTable id="privilegeTable" value="#{group.accesses}" var="priv">
						<p:column headerText="Source Name">
							<h:outputText value="#{priv.sourceName}"/>
						</p:column>
						<p:column headerText="Evidence Description">
							<h:outputText value="#{priv.privilegeDescription}"/>
						</p:column>
						<p:column headerText="Privilege Value">
							<h:outputText value="#{priv.privilegeValue}"/>
						</p:column>
						<p:column headerText="Privilege Comment">
							<h:outputText value="#{priv.privilegeComment}"/>
						</p:column>
						<p:column style="text-align:center;" headerText="Review Status">
							<f:facet name="header">
								<h:panelGroup  layout="block">
									<h:outputText value="Review Status" style="font-weight: bold; display: block; margin-bottom: 5px;"/>
									<p:panelGrid columns="2" cellpadding="2">
										<p:commandButton value="All"
														 action="#{accessListBean.setAllPrivilegeValues(group.accesses, 'K')}"
														 update="@form"
														 process="@this" />
										<p:commandButton value="None"
													 action="#{accessListBean.setAllPrivilegeValues(group.accesses, 'R')}"
													 update="@form"
													 process="@this" />
									</p:panelGrid>
								</h:panelGroup>
							</f:facet>
							<h:selectOneRadio value="#{priv.keepRemoveFlag}">
								<f:selectItems value="#{accessListBean.keepRemoveOptions}" var="option"
											   itemLabel="#{option.label}" itemValue="#{option.value}" />
								<p:ajax event="change"
										listener="#{accessListBean.statusChanged(priv)}"
										update="@this" />
							</h:selectOneRadio>
						</p:column>
					</p:dataTable>
				</p:panel>
			</ui:repeat>
		</h:form>

	<p:toolbar id="bottomApprovedReviewersTableToolBar">
		<p:toolbarGroup location="left">
			<h:commandLink styleClass="listitem" id="bottomApprovedReviewersDistributeLink" value="Return to List"
						   action="#{accessListBean.doReturnToEmployeeList}"/>
			<h:outputLabel styleClass="headerTextValue" value="  "/>
			<p:commandLink id="bottomAccessListRejectLink"
						   value="Reject Employee"
						   action="#{accessListBean.doPrepareRejectUserPanel}"
						   styleClass="undecoratedRejectLink"
						   rendered="#{accessListBean.editMode}"
						   oncomplete="window.scrollTo(0, 0);"/>
		</p:toolbarGroup>
		<p:toolbarGroup>
			<h:commandLink id="bottomPrintAccessCommandLink"
						   action="#{accessListBean.doPrintAccessList}"
						   value="Printable Version"
						   styleClass="print"
						   onmousedown="document.forms['bodyForm'].target='_blank'"/>
		</p:toolbarGroup>
		<p:toolbarGroup location="right">
			<h:commandLink styleClass="previousitem" id="bottomPreviousEmployeeLink"
						   action="#{accessListBean.doRetrievePreviousEmployee}"
						   value="Previous Employee"/>
			<h:outputLabel styleClass="headerTextValue" value="  "/>
			<h:commandLink styleClass="nextitem" id="bottomNextEmployeeLink"
						   action="#{accessListBean.doRetrieveNextEmployee}"
						   value="Next Employee"/>
		</p:toolbarGroup>
	</p:toolbar>
</ui:define>
<ui:define name="modalPanels" >
			<p:outputPanel autoUpdate="true" id="accessListModalAjaxPanel">
				<ui:include src="rejectReviewUsersModalPanelComponent.xhtml">
					<ui:param name="aListBean" value="#{accessListBean}" />
					<ui:param name="formId" value="rejectReviewUserCommentForm" />
                    <ui:param name="panelId" value="rejectReviewUserModalPanel" />
                    <ui:param name="msgId" value="rejectReviewUserModalPanelMessages" />
                    <ui:param name="dataTableId" value="rejectReviewUserModalPanelTable" />
                    <ui:param name="selectId" value="rejectUserModalPanelReason" />
                    <ui:param name="inputId" value="rejectUserModalComments" />
                    <ui:param name="submitId" value="rejectUserModalPanelSubmitButton" />
                    <ui:param name="cancelId" value="rejectUserModalPanelCancelButton" />
				</ui:include>
			</p:outputPanel>
</ui:define>
</ui:composition>

</html>
