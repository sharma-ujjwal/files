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
		<h:outputLabel styleClass="sectionHeader" value="Review Details"/>
		<p:ajaxStatus id="ajaxStatusLoaderReviewDetails" style="display: ruby">
			<f:facet name="start"><h:graphicImage  value="/images/loader.gif"/></f:facet>
		</p:ajaxStatus>
		<table id="reviewDetailsHeaderTable">
			<tbody>
			<tr>
				<td class="sideSpaced" width="120px"><h:outputLabel value="Review Name:" styleClass="fieldLabel" /></td>
				<td class="rightSpaced">
					<h:outputLabel value="#{reviewDetailsBean.review.name}" styleClass="fieldValue"/>
				</td>
				<td class="sideSpaced"><h:outputLabel value="Date Created:" styleClass="fieldLabel" /></td>
				<td class="rightSpaced">
					<h:outputLabel value="#{reviewDetailsBean.review.createdDate}" styleClass="fieldValue">
						<f:convertDateTime pattern="MM/dd/yyyy HH:mm:ss" dateStyle="short" timeZone="#{applicationBean.timeZone}"/>
					</h:outputLabel>
				</td>
				<td class="sideSpaced" rowspan="1" valign="top">
					<h:commandButton id="reviewDetailsEditCommentsCommand" value="Edit Comments"
									 action="#{reviewDetailsBean.doPrepareEditReviewCommentPanel}" />
				</td>
			</tr>
			<tr>
				<td class="sideSpaced"><h:outputLabel value="Review Type:" styleClass="fieldLabel" /></td>
				<td class="rightSpaced"><h:outputLabel value="#{reviewDetailsBean.review.typeCode}" styleClass="fieldValue"/></td>
				<td class="sideSpaced"><h:outputLabel value="Created By:" styleClass="fieldLabel" /></td>
				<td class="rightSpaced"><h:outputLabel value="#{reviewDetailsBean.review.createdBy}" styleClass="fieldValue"/></td>
				<td class="sideSpaced"><h:outputLabel value="Filter Criteria:" styleClass="fieldLabel"/></td>
				<td class="rightSpaced" rowspan="3" width="400px">
					<div style="overflow: auto; height: 50px;" >
						<h:outputLabel value="#{reviewDetailsBean.filterCriteriaText}" styleClass="fieldValue" escape="false"/>
					</div>
				</td>
			</tr>
			<tr>
				<td class="sideSpaced"><h:outputLabel value="Number of Reports:" styleClass="fieldLabel" /></td>
				<td class="rightSpaced"><h:outputLabel value="#{reviewDetailsBean.totalNumberOfReports}" styleClass="fieldValue"/></td>
				<td class="sideSpaced"><h:outputLabel value="Ad Hoc" styleClass="fieldLabel" /></td>
				<td class="rightSpaced"><h:outputLabel value="#{reviewDetailsBean.review.adHoc}" styleClass="fieldValue"/></td>
			</tr>
			<tr>
				<td class="sideSpaced"><h:outputLabel value="Review Status:" styleClass="fieldLabel" /></td>
				<td class="rightSpaced"><h:outputLabel value="#{reviewDetailsBean.review.statusCode}" styleClass="fieldValue"/></td>
			</tr>
			</tbody>
		</table>
		<table>
			<tbody>
			<tr>
				<td class="sideSpaced" width="120px"><h:outputLabel value="Comment:" styleClass="fieldLabel" /></td>
				<td class="rightSpaced">
					<h:outputLabel id="reviewCommentsOutput" value="#{reviewDetailsBean.review.comments}" styleClass="fieldValue"/>
				</td>
			</tr>
			</tbody>
		</table>
		<div class="verticalSpacer"/>
		<p:tabView id="tabView" styleClass="tabStyle"
				   activeIndex="#{reviewDetailsBean.selectedTabId}"
				   dynamic="true" cache="false">
			<p:ajax event="tabChange"
					listener="#{reviewDetailsBean.onTabChange}"
					update="bodyForm:headerMessages" />
			<p:tab id="pendingTab" title="Pending Reports">
				<ui:include src="pendingReportsComponent.xhtml"/>
			</p:tab>

			<p:tab id="approvedTab" title="Approved Reports">
				<ui:include src="approvedReportsComponent.xhtml"/>
			</p:tab>

			<p:tab id="distributedTab" title="Distributed Reports">
				<ui:include src="distributedReportsComponent.xhtml"/>
			</p:tab>

			<p:tab id="rejectedTab" title="Rejected Reports">
				<ui:include src="rejectedReportsComponent.xhtml"/>
			</p:tab>
		</p:tabView>
		<div class="verticalSpacer"/>
		<h:commandButton id="createBundleButton" value="Create Bundle" action="#{reviewDetailsBean.doCreateBundle}"/>
		<h:commandButton id="completeTaskButton" value="Complete Task"
						 action="#{reviewDetailsBean.doCompleteReviewDetailsTask}"
						 rendered="#{reviewDetailsBean.renderCompleteTaskButton}" >
		</h:commandButton>
	</ui:define>

	<ui:define name="modalPanels" >
		<p:outputPanel autoUpdate="true" id="reviewDetailsModalAjaxPanel">
			<ui:include src="editReviewCommentsModalPanelComponent.xhtml"/>
			<ui:include src="reassignReviewerModalPanelComponent.xhtml"/>
			<ui:include src="reassignSODReviewerModalPanelComponent.xhtml"/>
			<ui:include src="rejectReviewersModalPanelComponent.xhtml"/>
			<ui:include src="distributeReviewersModalPanelComponent.xhtml"/>
		</p:outputPanel>
	</ui:define>
</ui:composition>
</html>


<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
	xmlns:h="http://xmlns.jcp.org/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	xmlns:p="http://primefaces.org/ui">
 
	<ui:composition>
		<p:toolbar id="distributedReviewersTableToolBar" itemSeparator="line"/>
		<p:outputPanel autoUpdate="true" id="reviewDistributedReviewersTablePanel">
			<p:dataTable  id="distributedReviewersList"
						  value="#{reviewDetailsBean.distributedTableList}"
						  var="distributedTable" rows="10"
						  paginator="true" paginatorPosition="bottom"
						  rowIndexVar="rowIndex" rowKeyVar="rowKey"
						  paginatorTemplate="{CurrentPageReport} {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink} {RowsPerPageDropdown}"
						  currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
						  rowsPerPageTemplate="10,25,50,100">

				<p:column headerText="Bundle">
					<h:outputText value="#{distributedTable.bundleName}" />
				</p:column>

				<p:column rendered="#{reviewDetailsBean.columnType eq 'SGDU'}" headerText="SOD Owner">
					<h:outputText value="#{distributedTable.ownerName}" />
				</p:column>

				<p:column rendered="#{reviewDetailsBean.columnType eq 'SGDU'}" headerText="SOD Conflict Type">
					<h:outputText value="#{distributedTable.conflictTypeDisplayName}" />
				</p:column>

				<p:column headerText="Reviewer">
					<h:commandLink id="#{idPrefix}TaskLink"
								   value="#{distributedTable.reviewer.reviewerName}"
								   action="#{reviewDetailsBean.doViewReviewerDetails()}">
						<f:param name="actionReviewerId" value="#{distributedTable.reviewerId}" />
						<f:param name="actionStatusCodeValue" value="RELE" />
					</h:commandLink>
				</p:column>

				<p:column rendered="#{reviewDetailsBean.columnType eq 'MNGR'}" headerText="Escalation Manager">
					<h:outputText value="#{distributedTable.escalationMgrName}" />
				</p:column>

				<p:column headerText="Division">
					<h:outputText value="#{distributedTable.division}" />
				</p:column>

				<p:column headerText="Department" >
					<h:outputText value="#{distributedTable.reviewer.reviewer.department.name}" />
				</p:column>

				<p:column rendered="#{reviewDetailsBean.columnType ne 'MNGR'}" headerText="# of Departments" >
					<h:outputText value="#{distributedTable.numberOfDepartments}" />
				</p:column>

				<p:column headerText="# Direct Reports" >
					<h:outputText value="#{distributedTable.numberOfReviewedUsers}" />
				</p:column>

				<p:column headerText="Date Created">
					<h:outputText value="#{distributedTable.createdDate}">
						<f:convertDateTime pattern="MM/dd/yyyy" />
					</h:outputText>
				</p:column>

				<p:column headerText="Data Sent" >
					<h:outputText value="#{distributedTable.sentDate}" >
						<f:convertDateTime pattern="MM/dd/yyyy" />
					</h:outputText>
				</p:column>

				<p:column headerText="Target DueDate" >
					<h:outputText value="#{distributedTable.distributionTargetCompleteDate}" >
						<f:convertDateTime pattern="MM/dd/yyyy" />
					</h:outputText>
				</p:column>
			</p:dataTable>
		</p:outputPanel>
		<script>
    	$(document).ready(function() {
		$('#bodyForm\\:tabView\\:distributedReviewersList .ui-paginator-rpp-label').text('items per page').addClass('distributed-table-class');
      });
	</script>
	</ui:composition>
</html>
