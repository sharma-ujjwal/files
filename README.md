```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html
  xmlns="http://www.w3.org/1999/xhtml"
  xmlns:ui="http://java.sun.com/jsf/facelets"
  xmlns:h="http://xmlns.jcp.org/jsf/html"
  xmlns:f="http://java.sun.com/jsf/core"
  xmlns:p="http://primefaces.org/ui">
<ui:composition template="../common/main.xhtml">
<ui:define name="body">
<script type="text/javascript">

function setRadios(parentElementId, radioValue) {
    // find the element that contains the subtree with all the radios to set
	var parentElement = document.getElementById(parentElementId);
	// find all the input elements in the subtree of the parent element
	var inputs = parentElement.getElementsByTagName('input');
	// loop over each input element
	for (var i = 0; i &lt; inputs.length; i++) {
		var input = inputs[i];
		// is it of type radio?
		if (input.type == 'radio') {
			// is it the right radio for the given value to set?
			if (input.value == radioValue) {
				// check it
				input.checked = true;
			}
		}
	}
}

</script>

	<h:outputLabel styleClass="sectionHeader" value="Report Details"/>
	
	<p:ajaxStatus>
		<f:facet name="start"><h:graphicImage  value="/images/loader.gif"/></f:facet>
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
				<p:commandLink id="accessListRejectLink"
					value="Reject Employee" 
					action="#{accessListBean.doPrepareRejectUserPanel}"
					styleClass="undecoratedRejectLink"
					rendered="#{accessListBean.editMode}" />
			</p:toolbarGroup>
			<p:toolbarGroup>
			    <h:commandLink id="printAccessCommandLink" 
					action="#{accessListBean.doPrintAccessList}" 
					value="Printable Version" 
					styleClass="print"
					onmousedown="document.forms['bodyForm'].target='_blank'" />
			</p:toolbarGroup>
			<p:toolbarGroup location="right">
				<h:commandLink styleClass="previousitem" id="previousEmployeeLink"
					action="#{accessListBean.doRetrievePreviousEmployee}"
					value="Previous Employee" />
				<h:outputLabel styleClass="headerTextValue" value="  "/>
				<h:commandLink styleClass="nextitem" id="nextEmployeeLink"
					action="#{accessListBean.doRetrieveNextEmployee}"
					value="Next Employee" />
			</p:toolbarGroup>
		</p:toolbar>

			<table style="margin: 10px 0px 10px 0px; width: 100%">
			<tbody>
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
			</tbody>
			</table>
		<p:outputPanel autoUpdate="true" id="accessListOutputPanel">
			<p:panel binding="#{accessListBean.panel}"/>
		</p:outputPanel>
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
					oncomplete="window.scrollTo(0, 0);" />
			</p:toolbarGroup>
			<p:toolbarGroup>
			    <h:commandLink id="bottomPrintAccessCommandLink" 
					action="#{accessListBean.doPrintAccessList}" 
					value="Printable Version" 
					styleClass="print"
					onmousedown="document.forms['bodyForm'].target='_blank'" />
			</p:toolbarGroup>
			<p:toolbarGroup location="right">
				<h:commandLink styleClass="previousitem" id="bottomPreviousEmployeeLink"
					action="#{accessListBean.doRetrievePreviousEmployee}"
					value="Previous Employee" />
				<h:outputLabel styleClass="headerTextValue" value="  "/>
				<h:commandLink styleClass="nextitem" id="bottomNextEmployeeLink"
					action="#{accessListBean.doRetrieveNextEmployee}"
					value="Next Employee" />
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


public Panel getPanel() {
		logger.debug("getPanel() --> being executed.");
		/*
		 * Create a new panel
		 */
		panel = new Panel();
		panel.setId("accessListMainPanel");
		panel.setStyle("padding:0");
		panel.setStyleClass("outpanelHeader");
		/*
		 * then, for each application...
		 */
		int id = 0;
		logger.debug("Number of Applications for user = " + getReviewUserApplicationAccessesDTOs().size());
		for (ReviewUserApplicationAccessesDTO applicationDTO : getReviewUserApplicationAccessesDTOs()) {
			id++;
			String applicationName = applicationDTO.getApplicationName();

			String applicationUserId = applicationDTO.getAccesses().get(0).getApplicationUserId();
			/*
			 * create a tool bar to be displayed above the data table representing the application "group" (e.i. Compass, Solar
			 * ..)
			 *
			 * ToolBar creates a holder for the bar, it must have toolBarGroups to display items in the tool bar.
			 */
			Toolbar toolBar = new Toolbar();
			toolBar.setId("Application" + id + "toolBar");
			if (this.review.isSODReview()) {
				toolBar.setStyleClass("defaultTableHeaderSOD");
			} else {
				toolBar.setStyleClass("apptitle");
			}
			/*
			 * add ToolBarGroup to the ToolBar
			 */
			toolBar.getChildren().add(buildToolBarGroup(id, applicationName, applicationUserId));
			/*
			 * add the ToolBar for the application to the panel
			 */
			panel.getChildren().add(toolBar);
			/*
			 * add the data table for the application that has the applications accesses for the user
			 */
			panel.getChildren().add(buildAccessTable(asUI(applicationDTO.getAccesses()), id, applicationDTO));

			if (isEditMode()) {
				HtmlOutputLabel label = new HtmlOutputLabel();
				label.setId("Application" + id + "commentLabel");
				label.setValue("Comments: ");

				panel.getChildren().add(label);

				HtmlInputText input = new HtmlInputText();
				input.setId("Application" + id + "commentText");
				input.setSize(80);
				input.setValue(applicationDTO.getComment());
				input.addValueChangeListener(new CommentListener(applicationDTO));

				panel.getChildren().add(input);
			} else if (isViewEdits()|| isPrintMode()) {
				HtmlOutputLabel label = new HtmlOutputLabel();
				label.setId("Application" + id + "commentLabel");
				label.setValue("Comments: ");

				panel.getChildren().add(label);

				HtmlOutputText output = new HtmlOutputText();
				output.setId("Application" + id + "commentText");
				output.setValue(applicationDTO.getComment());

				panel.getChildren().add(output);
			}

			HtmlGraphicImage spacer = new HtmlGraphicImage();
			spacer.setId("Application" + id + "spacer");
			spacer.setHeight("15px");
			spacer.setWidth("15px");
			spacer.setStyle("display: block"); // not inline

			panel.getChildren().add(spacer);
		}
		//set the mode back to the one before that was before the clicking the print.
		if(isPrintMode()){
			this.mode = this.modeBeforePrint;
		}
		return panel;
	}
