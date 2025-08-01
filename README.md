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