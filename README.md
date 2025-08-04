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



Thanks for sharing your full XHTML. The issue where the "Gender" and "Established Patient?" checkbox alignment changes across rows is because:

1. Your nested <table> doesn't enforce fixed column widths.


2. Some rows have different <td> counts (like the "Name" row only has 2 <td>, while the "Gender" row has 4).


3. table-layout is not applied, so column widths are determined dynamically by the longest text in each cell.



We can fix this by ensuring a consistent column structure in every row and using CSS to lock column widths.


---

✅ Updated XHTML

We will make every row have 4 columns, even if some cells are empty.

<table class="formtable dependentClass">
    <tr>
        <td class="rightLabelBold">Effective date:</td>
        <td><h:outputText value="#{dependent.effectiveDate}" /></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td class="rightLabelBold">Name:</td>
        <td><h:outputText value="#{dependent.formattedName}" /></td>
        <td class="rightLabelBold">Gender:</td>
        <td><h:outputText value="#{dependent.gender}" /></td>
    </tr>
    <tr>
        <td class="rightLabelBold">Date of Birth:</td>
        <td><h:outputText value="#{dependent.dateOfBirth}" /></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td class="rightLabelBold">Preferred Language:</td>
        <td><h:outputText value="#{dependent.preferredLanguage}" /></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td class="rightLabelBold">Facility ID:</td>
        <td><h:outputText value="#{dependent.facilityId}" /></td>
        <td><h:selectBooleanCheckbox value="#{dependent.establishedPatient}" /></td>
        <td class="leftLabelBold">Established Patient?</td>
    </tr>
</table>


---

✅ CSS for Alignment

.formtable {
    width: 100%;
    border-collapse: collapse;
    table-layout: fixed; /* Fixed columns */
}

.formtable td {
    padding: 4px 8px;
    vertical-align: middle;
}

/* Define 4 equal-width columns */
.formtable td:nth-child(1) { width: 20%; }
.formtable td:nth-child(2) { width: 30%; }
.formtable td:nth-child(3) { width: 10%; }
.formtable td:nth-child(4) { width: 40%; }

/* Align checkbox and label horizontally */
.formtable td:nth-child(3),
.formtable td:nth-child(4) {
    white-space: nowrap;
}


---

✅ What This Fixes:

✔ "Gender" is always in the same column across all dependents.
✔ "Established Patient?" checkbox and label always align in the same column.
✔ No shifting due to text length.
✔ Layout looks like the original image you shared.


---

Would you like me to make "Established Patient?" column fixed to the far right of the table (independent of Facility ID width) exactly like your original screenshot? (this would make it pixel-perfect).

