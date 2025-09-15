```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html>

<html xmlns="http://www.w3.org/1999/xhtml"
	  xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
	  xmlns:h="http://xmlns.jcp.org/jsf/html"
	  xmlns:f="http://xmlns.jcp.org/jsf/core"
	  xmlns:p="http://primefaces.org/ui" xml:lang="en" lang="en">

	<!-- aPolicySearchData PolicySearchData -->
	<!-- aPolicySearchActionListener PolicySearchActionListener -->
	<!-- aPolicySearchRule PolicySearchRule -->
	<!-- onsubmit javascript to execute on submit of row select-->
	<!-- oncomplete javascript to execute on completion of row select-->
	<ui:composition>
		<p:outputPanel id="policySearchResultPanel" ajaxRendered="true">
			<script src="#{request.contextPath}/resources/js/ui-wait-modal.js" type="text/javascript"/>
			<style type="text/css">
				.ui-datatable tbody.ui-datatable-data > tr.ui-widget-content.ui-datatable-even:hover > td,
				.ui-datatable tbody.ui-datatable-data > tr.ui-widget-content.ui-datatable-odd:hover > td,
				.dataTableb .evenRow:hover > td,
				.dataTableb .oddRow:hover > td {
					background-color: rgb(152, 194, 234) !important; /* Solid hover color */
					color: #000 !important; /* Ensure text remains visible */
				}
			</style>
			<p:dataTable
				value="#{aPolicySearchData.policySearchResultList}"
				id="policySearchDataTable"
				var="policy"
				rows="0" tableStyle="width:auto;"
				styleClass="dataTableb"
				rowClasses="oddRow evenRow"
				style="cursor:pointer;  width:auto; font-size: 13px;"
				rendered="#{aPolicySearchRule.displayResults}"
				rowKey="#{policy.uniqueId}"
				selectionMode="single"
			    selection="#{policyDetailData.selectedPolicy}">
				<p:ajax
					event="rowSelect"
					listener="#{aPolicySearchActionListener.selectPolicyRow}" onsubmit="#{onsubmit}"
					onstart="showModalInfoWindow();" oncomplete="hideModalInfoWindow();"/>
				<p:column headerClass="#{aPolicySearchRule.policyStyle}" >
					<f:facet name="header">
						<h:commandLink id="policyHeader" value="Policy"
							actionListener="#{aPolicySearchActionListener.sortPolicy}"
							onmouseup="showModalInfoWindow(true);" styleClass="header-test_style"/>
					</f:facet>
					<h:outputText id="polPartAcct" value="#{policy.polPartAcct}" escape="false" styleClass="output-test_style"/>
					<br />
					<h:outputText id="accountName" value="#{policy.accountName}" escape="false" styleClass="output-test_style"/>
					<br />
					<h:outputText id="status" value="#{policy.status}" styleClass="output-test_style"/>
					<br />
					<h:outputText id="adminType" value="#{policy.adminType}" escape="false" styleClass="output-test_style"/>
				</p:column>
				<p:column headerClass="#{aPolicySearchRule.groupStyle}">
					<f:facet name="header">
						<h:commandLink id="groupHeader" value="Group"
							actionListener="#{aPolicySearchActionListener.sortGroup}" 
							onmouseup="showModalInfoWindow(true);" styleClass="header-test_style"/>
					</f:facet>
					<h:outputText id="group" value="#{policy.group}" escape="false" styleClass="output-test_style"/>
				</p:column>
				<p:column headerClass="#{aPolicySearchRule.groupOfficeStyle}">
					<f:facet name="header">
						<h:commandLink id="groupOfficeHeader" value="Group Office"
							actionListener="#{aPolicySearchActionListener.sortGroupOffice}"
							onmouseup="showModalInfoWindow(true);" styleClass="header-test_style"/>
					</f:facet>
					<h:outputText styleClass="groupOfficeCls" id="groupOffice" value="#{policy.groupOffice}"/>
				</p:column>
				<p:column>
					<f:facet name="header">
						<h:outputText id="brokerHeader" value="Broker" styleClass="header-test_style"/>
					</f:facet>
					<h:outputText id="broker" value="#{policy.broker}" escape="false" styleClass="output-test_style"/>
				</p:column>
				<p:column headerClass="#{aPolicySearchRule.systemStyle}">
					<f:facet name="header">
						<h:commandLink id="systemHeader" value="System"
							actionListener="#{aPolicySearchActionListener.sortSystem}"
							onmouseup="showModalInfoWindow(true);" styleClass="header-test_style"/>
					</f:facet>
					<h:outputText id="system" value="#{policy.system}" styleClass="output-test_style"/>
				</p:column>
			</p:dataTable>
			<table>
				<tr>
					<td>
						<h:commandButton 
							id="firstPage" 
							actionListener="#{aPolicySearchActionListener.findFirstListener}" 
							image="#{sessionData.baseUrl}/assets/images/icons/#{aPolicySearchData.findFirstButtonImage}"
							rendered="#{!aPolicySearchData.pagingNeeded}" 
							disabled="#{aPolicySearchData.backDisabled}"
							onclick="showModalInfoWindow(true);"/>
						<h:commandButton 
							id="prevPage" 
							actionListener="#{aPolicySearchActionListener.findPrevListener}" 
							image="#{sessionData.baseUrl}/assets/images/icons/#{aPolicySearchData.findPrevButtonImage}"
							rendered="#{!aPolicySearchData.pagingNeeded}" 
							disabled="#{aPolicySearchData.backDisabled}"
							onclick="showModalInfoWindow(true);"/>
					</td>
					<td>
						<h:selectOneMenu id="selectedPageId" value="#{aPolicySearchData.selectedPage}" rendered="#{!aPolicySearchData.pagingNeeded}">
							<f:selectItems value="#{aPolicySearchData.pageList}"/>
							<p:ajax event="change"
								listener="#{aPolicySearchActionListener.selectPageListener}"
								onstart="showModalInfoWindow(true);"
								oncomplete="hideModalInfoWindow();"/>
						</h:selectOneMenu>
					</td>
					<td>
						<h:commandButton id="nextPage"
							actionListener="#{aPolicySearchActionListener.findNextListener}" 
							image="#{sessionData.baseUrl}/assets/images/icons/#{aPolicySearchData.findNextButtonImage}"
							rendered="#{!aPolicySearchData.pagingNeeded}" 
							disabled="#{aPolicySearchData.forwardDisabled}"
							onclick="showModalInfoWindow(true);"/>
						<h:commandButton id="lastPage"
							actionListener="#{aPolicySearchActionListener.findLastListener}" 
							image="#{sessionData.baseUrl}/assets/images/icons/#{aPolicySearchData.findLastButtonImage}"
							rendered="#{!aPolicySearchData.pagingNeeded}" 
							disabled="#{aPolicySearchData.forwardDisabled}"
							onclick="showModalInfoWindow(true);"/>
					</td>
				</tr>
			</table>
			<table id="policySearchDataTableInfo">
				<tr>
					<td>
						<h:outputText id="typeLabelId" value="#{aPolicySearchData.typeLabel} #{aPolicySearchData.displayedPageStartPosition} to #{aPolicySearchData.displayedPageEndPosition} of #{aPolicySearchData.totalCount} displayed." rendered="#{!aPolicySearchData.listEmpty}"/>
					</td>
				</tr>
				<tr>
					<td>
						<h:outputText value="Display " rendered="#{!aPolicySearchData.listEmpty}"/>
						<h:selectOneMenu id="aPolicySelectId" value="#{aPolicySearchData.selectedPageSize}" rendered="#{!aPolicySearchData.listEmpty}">
							<f:selectItem itemLabel="10" itemValue="10"/>
							<f:selectItem itemLabel="25" itemValue="25"/>
							<f:selectItem itemLabel="50" itemValue="50"/>
							<f:selectItem itemLabel="100" itemValue="100"/>
							<p:ajax event="change"
									update="policySearchDataTable typeLabelId firstPage prevPage nextPage lastPage selectedPageId"
									listener="#{aPolicySearchActionListener.changePageSizeListener}"
									onstart="showModalInfoWindow(true);"
									oncomplete="resetScrollPosition(); hideModalInfoWindow();"/>
						</h:selectOneMenu>
						<h:outputText value=" #{aPolicySearchData.typeLabel} per page." rendered="#{!aPolicySearchData.listEmpty}"/>
					</td>
				</tr>
			</table>
		</p:outputPanel>
	</ui:composition>
</html>

method is

public boolean isPagingNeeded() {
		System.out.println("Total Count:>>>>> " + totalCount + " Page Size:>>>>> " + pageSize);
		return (totalCount <= pageSize)
}

