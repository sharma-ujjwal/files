```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
	  xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
	  xmlns:h="http://xmlns.jcp.org/jsf/html"
	  xmlns:f="http://xmlns.jcp.org/jsf/core"
	  xmlns:p="http://primefaces.org/ui" xml:lang="en" lang="en">
	<h:head>
		<title>Engage Member Search Results</title>
	</h:head>
	<ui:composition template="/xhtml/templates/TwoColumnLayout.xhtml">
		<ui:define name="centerColumn">
			<style type="text/css">
				.ui-datatable tbody.ui-datatable-data > tr.ui-widget-content.ui-datatable-even:hover > td,
				.ui-datatable tbody.ui-datatable-data > tr.ui-widget-content.ui-datatable-odd:hover > td,
				.dataTableb .evenRow:hover > td,
				.dataTableb .oddRow:hover > td {
					background-color: rgb(152, 194, 234) !important; /* Solid hover color */
					color: #000 !important; /* Ensure text remains visible */
				}
			</style>
			<h:form id="memberSearchResultsForm">
				<div id="errorMessages">
					<p:outputPanel id="messageForRefresh" autoUpdate="true">
						<h:messages errorClass="errormessage"
							    	infoClass="infomessage" 
									warnClass="warningmessage"
									fatalClass="errormessage"
									layout="table"
									showDetail="true"
									showSummary="false">
						</h:messages>
					</p:outputPanel>
				</div>
				<script src="#{request.contextPath}/resources/js/ui-utils.js" type="text/javascript"/>
				<h:outputText id="initialDisplayMessage" value="To start your search, follow the instructions in the left pane." rendered="#{memberSearchRule.displayInitial}"/>
				<p:dataTable value="#{memberSearchData.memberSearchResultList}"
					var="member"
					rows="0" tableStyle="width:auto;"
					styleClass="dataTableb"
					style="cursor:pointer; overflow-x: hidden;"
					selectionMode="single"
					rendered="#{memberSearchRule.displayResults}"
					rowIndexVar="rowIndex"
					selection="#{memberSearchData.selectedMember}"
					rowKey="#{member.uniqueId}"
					rowKeyVar="rowKey">
					<p:ajax event="rowSelect"
							listener="#{memberSearchActionListener.selectMemberRow}"
							update="memberSearchResultsForm:messageForRefresh"
							onstart="showModalInfoWindow(true);" />
					<p:column headerClass="#{memberSearchRule.nameStyle}" styleClass="#{rowIndex%2 == 0 ? 'evenRow' : 'oddRow'}">
						<f:facet name="header"><h:commandLink id="memberNameHeader" value="Member Name" actionListener="#{memberSearchActionListener.sortMemberName}" styleClass="header-test_style"/></f:facet>
						<h:outputText id="memberName" value="#{member.memberNameColumn}" escape="false" styleClass="output-test_style"/>
					</p:column>
					<p:column headerClass="#{memberSearchRule.dobStyle}" styleClass="#{rowIndex%2 == 0 ? 'evenRow' : 'oddRow'}">
						<f:facet name="header"><h:commandLink id="memberDobHeader" value="Date of Birth" actionListener="#{memberSearchActionListener.sortMemberDob}" styleClass="header-test_style"/></f:facet>
						<h:outputText id="memberDateOfBirth" value="#{member.dobColumn}" escape="false" styleClass="output-test_style"/>
					</p:column>
					<p:column styleClass="#{rowIndex%2 == 0 ? 'evenRow' : 'oddRow'}">
						<f:facet name="header"><h:outputText value="Member ID" styleClass="header-test_style"/></f:facet>
						<h:outputText id="memberId" value="#{member.memberIdColumn}" escape="false" styleClass="output-test_style"/>
					</p:column>
					<p:column headerClass="#{memberSearchRule.ssnStyle}" styleClass="#{rowIndex%2 == 0 ? 'evenRow' : 'oddRow'}">
						<f:facet name="header"><h:commandLink id="memberSsnHeader" value="SSN" actionListener="#{memberSearchActionListener.sortMemberSsn}" styleClass="header-test_style"/></f:facet>
						<h:outputText style="text-transform: uppercase;" id="memberSsn" value="#{member.ssnColumn}" escape="false" styleClass="output-test_style"/>
					</p:column>
					<p:column styleClass="#{rowIndex%2 == 0 ? 'evenRow' : 'oddRow'}">
						<f:facet name="header"><h:outputText value="Policy" styleClass="header-test_style"/></f:facet>
						<h:outputText id="memberPolicy" value="#{member.policyColumn}" escape="false" styleClass="output-test_style"/>
					</p:column>
				</p:dataTable>
			</h:form>
		</ui:define>
	</ui:composition>
</html>
