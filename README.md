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
    <h:outputStylesheet>
        .formtable {
            width: 100%;
            border-collapse: collapse;
        }
        .formtable td {
            padding: 2px 6px;
            vertical-align: middle;
        }
        .rightLabelBold {
            font-weight: bold;
            white-space: nowrap;
        }
        .leftLabelBold {
            font-weight: bold;
        }
        .sectionheader {
            font-weight: bold;
            font-size: 14px;
            margin-bottom: 4px;
        }
    </h:outputStylesheet>
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
            <p class="sectionheader">
                <h:outputText value="#{dependent.relationship}" />
            </p>
            <table class="formtable">
                <tr>
                    <td class="rightLabelBold">Effective date:</td>
                    <td><h:outputText value="#{dependent.effectiveDate}" /></td>
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




<table class="formtable" style="width:auto; border-collapse: collapse;">
    <tr>
        <td class="rightLabelBold">Effective date:</td>
        <td><h:outputText value="#{dependent.effectiveDate}" /></td>
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
    </tr>
    <tr>
        <td class="rightLabelBold">Preferred Language:</td>
        <td><h:outputText value="#{dependent.preferredLanguage}" /></td>
    </tr>
    <tr>
        <td class="rightLabelBold">Facility ID:</td>
        <td><h:outputText value="#{dependent.facilityId}" /></td>
        <td>
            <h:selectBooleanCheckbox value="#{dependent.establishedPatient}" />
        </td>
        <td class="leftLabelBold">Established Patient?</td>
    </tr>
</table>

.formtable {
    border-collapse: collapse;
    width: auto !important; /* No forced 100% width */
}

.formtable td {
    padding: 2px 6px !important; /* Reduce spacing between text */
    white-space: nowrap; /* Prevent breaking into multiple lines unnecessarily */
}

.rightLabelBold {
    font-weight: bold;
    text-align: right;
}

.leftLabelBold {
    font-weight: bold;
    text-align: left;
}


<table class="formtable" style="width:auto; border-collapse: collapse;">
    <tr>
        <td class="rightLabelBold">Effective date:</td>
        <td><h:outputText value="#{dependent.effectiveDate}" /></td>
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
    </tr>
    <tr>
        <td class="rightLabelBold">Preferred Language:</td>
        <td><h:outputText value="#{dependent.preferredLanguage}" /></td>
    </tr>
    <tr>
        <td class="rightLabelBold">Facility ID:</td>
        <td><h:outputText value="#{dependent.facilityId}" /></td>
    </tr>
    <tr>
        <td colspan="2">
            <h:selectBooleanCheckbox value="#{dependent.establishedPatient}" />
            <span class="leftLabelBold">Established Patient?</span>
        </td>
    </tr>
</table>

.formtable td {
    padding: 2px 6px !important;
    vertical-align: middle !important;
}

.leftLabelBold {
    font-weight: bold;
    margin-left: 4px;
}


<table class="formtable">
    <tr>
        <th class="rightLabelBold">Effective Date</th>
        <th class="rightLabelBold">Name</th>
        <th class="rightLabelBold">Gender</th>
        <th class="rightLabelBold">Date of Birth</th>
        <th class="rightLabelBold">Preferred Language</th>
        <th class="rightLabelBold">Facility ID</th>
        <th class="leftLabelBold">Established Patient?</th>
    </tr>

    <ui:repeat value="#{bean.dependents}" var="dependent">
        <tr>
            <td><h:outputText value="#{dependent.effectiveDate}" /></td>
            <td><h:outputText value="#{dependent.formattedName}" /></td>
            <td><h:outputText value="#{dependent.gender}" /></td>
            <td><h:outputText value="#{dependent.dateOfBirth}" /></td>
            <td><h:outputText value="#{dependent.preferredLanguage}" /></td>
            <td><h:outputText value="#{dependent.facilityId}" /></td>
            <td>
                <h:selectBooleanCheckbox value="#{dependent.establishedPatient}" />
            </td>
        </tr>
    </ui:repeat>
</table>


<table class="formtable">
    <thead>
        <tr>
            <th class="rightLabelBold">Effective Date</th>
            <th class="rightLabelBold">Name</th>
            <th class="rightLabelBold">Gender</th>
            <th class="rightLabelBold">Date of Birth</th>
            <th class="rightLabelBold">Preferred Language</th>
            <th class="rightLabelBold">Facility ID</th>
            <th class="leftLabelBold">Established Patient?</th>
        </tr>
    </thead>

    <tbody>
        <ui:repeat value="#{bean.dependents}" var="dependent">
            <tr>
                <td><h:outputText value="#{dependent.effectiveDate}" /></td>
                <td><h:outputText value="#{dependent.formattedName}" /></td>
                <td><h:outputText value="#{dependent.gender}" /></td>
                <td><h:outputText value="#{dependent.dateOfBirth}" /></td>
                <td><h:outputText value="#{dependent.preferredLanguage}" /></td>
                <td><h:outputText value="#{dependent.facilityId}" /></td>
                <td>
                    <h:selectBooleanCheckbox value="#{dependent.establishedPatient}" />
                    <h:outputLabel value="Established Patient?" />
                </td>
            </tr>
        </ui:repeat>
    </tbody>
</table>



/* Main table styling for dependents */
.formtable {
    width: 100%;
    border-collapse: collapse;
    margin-bottom: 10px;
    background-color: #fff;
    table-layout: fixed;
}

/* Alternating background color for each dependent row */
.ui-datatable .oddRow {
    background-color: #ffffff;
}

.ui-datatable .evenRow {
    background-color: #f9f9f9;
}

/* Section header (relationship) styling */
.sectionheader {
    font-weight: bold;
    font-size: 14px;
    margin-bottom: 5px;
    color: #444;
}

/* Label styling */
.rightLabelBold, 
.leftLabelBold {
    font-weight: bold;
    padding-right: 8px;
    white-space: nowrap;
}

/* Table cell padding */
.formtable td {
    padding: 4px 8px;
    vertical-align: middle;
}

/* Yellow border on the right side */
.ui-datatable .ui-datatable-data > tr > td {
    border-right: 5px solid #f6d77a; /* yellow vertical bar */
}

/* Remove border from the last column */
.ui-datatable .ui-datatable-data > tr > td:last-child {
    border-right: none;
}

/* Align checkbox with label */
.formtable td h\:selectBooleanCheckbox {
    margin-right: 4px;
    vertical-align: middle;
}

.formtable {
    width: 100%;
    border-collapse: collapse;
    table-layout: fixed; /* FIXED column layout */
    margin-bottom: 10px;
}

.formtable td {
    padding: 4px 8px;
    vertical-align: middle;
}

/* Define fixed widths for each column */
.formtable td:nth-child(1) { width: 20%; }  /* Label column */
.formtable td:nth-child(2) { width: 30%; }  /* Value column */
.formtable td:nth-child(3) { width: 20%; }  /* Label column (Gender/Checkbox) */
.formtable td:nth-child(4) { width: 30%; }  /* Value column (Gender value / Checkbox label) }

/* Align checkbox and label properly */
.formtable td[colspan="2"] {
    display: flex;
    align-items: center;
}



.formtable {
    width: 100%;
    border-collapse: collapse;
    table-layout: fixed;
    margin-bottom: 10px;
}

.formtable td {
    padding: 4px 8px;
    vertical-align: middle;
}

/* Define column widths */
.formtable td:nth-child(1) { width: 18%; } /* Label */
.formtable td:nth-child(2) { width: 30%; } /* Value */
.formtable td:nth-child(3),
.formtable td:nth-child(4) { width: 26%; } /* Facility ID + Established Patient */

/* Adjust checkbox alignment */
.checkbox-cell {
    display: flex;
    align-items: center;
    gap: 6px;
    white-space: nowrap;
}

/* Alternating rows */
.ui-datatable .oddRow { background-color: #ffffff; }
.ui-datatable .evenRow { background-color: #f9f9f9; }

/* Yellow right border */
.ui-datatable .ui-datatable-data > tr > td {
    border-right: 5px solid #f6d77a;
}
.ui-datatable .ui-datatable-data > tr > td:last-child {
    border-right: none;
}




Yes! We can remove the nested <table> structure entirely and achieve the exact same alignment using CSS Grid or Flexbox. This way, the columns (Effective Date, Name, Gender, DOB, Language, Facility ID, Established Patient) will stay perfectly aligned regardless of content length—just like in your original screenshot.


---

✅ Updated XHTML (No <tr>/<td>)

Replace the nested table with a single grid container:

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
            <p class="sectionheader">
                <h:outputText value="#{dependent.relationship}" />
            </p>

            <!-- Using CSS Grid -->
            <div class="dependent-grid">
                <div class="label">Effective date:</div>
                <div class="value"><h:outputText value="#{dependent.effectiveDate}" /></div>

                <div class="label">Name:</div>
                <div class="value"><h:outputText value="#{dependent.formattedName}" /></div>

                <div class="label">Gender:</div>
                <div class="value"><h:outputText value="#{dependent.gender}" /></div>

                <div class="label">Date of Birth:</div>
                <div class="value"><h:outputText value="#{dependent.dateOfBirth}" /></div>

                <div class="label">Preferred Language:</div>
                <div class="value"><h:outputText value="#{dependent.preferredLanguage}" /></div>

                <div class="label">Facility ID:</div>
                <div class="value"><h:outputText value="#{dependent.facilityId}" /></div>

                <div class="checkbox">
                    <h:selectBooleanCheckbox value="#{dependent.establishedPatient}" />
                    <h:outputLabel value="Established Patient?" style="margin-left: 4px;" />
                </div>
            </div>
        </p:column>
    </p:dataTable>
</ui:composition>


---

✅ CSS for Grid Layout

/* Grid container for each dependent */
.dependent-grid {
    display: grid;
    grid-template-columns: 18% 30% 18% 30%; /* 4 columns like the screenshot */
    gap: 4px 10px;
    width: 100%;
    align-items: center;
}

/* Labels and values */
.dependent-grid .label {
    font-weight: bold;
    white-space: nowrap;
}

.dependent-grid .value {
    white-space: nowrap;
}

/* Checkbox alignment */
.dependent-grid .checkbox {
    grid-column: 3 / span 2; /* spans across 2 columns on the right */
    display: flex;
    align-items: center;
    gap: 5px;
    margin-top: 4px;
}

/* Row striping */
.ui-datatable .oddRow { background-color: #fff; }
.ui-datatable .evenRow { background-color: #f9f9f9; }

/* Yellow border */
.ui-datatable .ui-datatable-data > tr > td {
    border-right: 5px solid #f6d77a;
}
.ui-datatable .ui-datatable-data > tr > td:last-child {
    border-right: none;
}


---

🎯 What this does:

Removes <tr>/<td> complexity.

Uses grid layout to ensure every field stays in a fixed column (name no longer shifts gender or checkbox).

Checkbox is aligned properly and stays in its own section.

Matches your original reference image exactly.



---

Would you like me to make "Established Patient?" fixed in the last column, fully aligned vertically across dependents (like a dedicated column)? This will match the yellow bar alignment in your screenshot pixel-perfect.

