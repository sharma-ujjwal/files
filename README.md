```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
	  xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
	  xmlns:h="http://xmlns.jcp.org/jsf/html"
	  xmlns:p="http://primefaces.org/ui" xml:lang="en" lang="en">
	<h:head>
		<title>memberDependentDetail.xhtml</title>
		<meta http-equiv="keywords" content="enter,your,keywords,here" />
		<meta http-equiv="description" content="A short description of this page." />
		<meta http-equiv="content-type" content="text/html; charset=ISO-8859-1" />
	</h:head>
	<ui:composition>			
		<p:dataTable id="memberDependentDataTableId"
					 value="#{memberDetailData.dependentList}"
					 var="dependent"
		             rows="0"
					 styleClass="formtable"
					 rowStyleClass="oddRow evenRow"
					 headerClass="defaultTableHeader"
					 style="cursor:pointer; width:100%;"
					 rendered="true">
			<p:column id="dependentColumn">
				<p class="sectionheader"><h:outputText value="#{dependent.relationship}"/></p>
				<table border="0" cellspacing="0" cellpadding="0" class="formtable">
					<tr>
						<td class="rightLabelBold"><h:outputText value="Effective date:"/></td>
						<td colspan="9"><h:outputText value="#{dependent.effectiveDate}"/></td>
					</tr>
					<tr>
						<td class="rightLabelBold"><h:outputText value="Name:"/></td>
						<td colspan="9"><h:outputText value="#{dependent.formattedName}"/></td>
					</tr>
					<tr>
						<td class="rightLabelBold"><h:outputText value="Date of Birth:"/></td>
						<td><h:outputText value="#{dependent.dateOfBirth}"/></td>
						<td class="rightLabelBold"><h:outputText value="Gender:"/></td>
						<td><h:outputText value="#{dependent.gender}"/></td>
						<!-- <td class="rightLabelBold"><h:outputText value="SSN:"/></td>
						<td><h:outputText value="&#160;" escape="false"/></td>
						<td><h:outputText value="#{dependent.ssn}"/></td> -->
					</tr>
					<tr>
						<td class="rightLabelBold"><h:outputText value="Preferred Language:"/></td>
						<td colspan="9"><h:outputText value="#{dependent.preferredLanguage}"/></td>
					</tr>
					<tr>
						<td class="rightLabelBold"><h:outputText value="Facility ID:"/></td>
						<td><h:outputText value="#{dependent.facilityId}"/></td>
						<td class="rightLabelBold"><h:selectBooleanCheckbox value="#{dependent.establishedPatient}"/></td>
						<td colspan="5" class="leftLabelBold"><h:outputText value="Established Patient?"/></td>
					</tr>
				</table>
			</p:column>
		</p:dataTable>
	</ui:composition>
</html>
