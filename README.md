```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
	  xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
	  xmlns:h="http://xmlns.jcp.org/jsf/html"
	  xmlns:p="http://primefaces.org/ui" xml:lang="en" lang="en">
	<h:head>
		<title>memberDependentDetail.xhtml</title>
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
				<table class="formtable dependentClass" style="width:auto; border-collapse: collapse;">
					<tr>
						<td class="rightLabelBold">Effective date:</td>
						<td><h:outputText value="#{dependent.effectiveDate}" /></td>
					</tr>
					<tr>
						<td class="rightLabelBold">Name:</td>
						<td><h:outputText value="#{dependent.formattedName}" /></td>
					</tr>
					<tr>
						<td class="rightLabelBold">Date of Birth:</td>
						<td><h:outputText value="#{dependent.dateOfBirth}" /></td>
						<td class="rightLabelBold">Gender:</td>
						<td><h:outputText value="#{dependent.gender}" /></td>
					</tr>
					<tr>
						<td class="rightLabelBold">Preferred Language:</td>
						<td><h:outputText value="#{dependent.preferredLanguage}" /></td>
					</tr>
					<tr>
						<td class="rightLabelBold">Facility ID:</td>
						<td><h:outputText value="#{dependent.facilityId}" /></td>
						<td><h:selectBooleanCheckbox value="#{dependent.establishedPatient}" /></td>
						<td class="leftLabelBold">Established Patient?</td>
					</tr>
				</table>
			</p:column>
		</p:dataTable>
	</ui:composition>
</html>
