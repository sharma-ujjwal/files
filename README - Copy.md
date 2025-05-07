<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:ui="http://java.sun.com/jsf/facelets"
      xmlns:h="http://xmlns.jcp.org/jsf/html"
      xmlns:f="http://java.sun.com/jsf/core"
      xmlns:p="http://primefaces.org/ui">

<ui:composition template="../common/adminConsoleTemplate.xhtml">
    <ui:define name="styles">
        <style type="text/css">
            select {
                font-size: 0.8em;
            }
        </style>
    </ui:define>
    <ui:define name="script">
        <script type="text/javascript">
            console.log("Setting up exception edit panel script");

            function setDepartmentValue(value) {
                var selector = document.getElementById('editExceptionForm:userExceptionsTable:0:missingDepartmentNameCostCenterSelectOne');
                setSelectorValue(selector, value);
            }

            function setDivisionValue(value) {
                var selector = document.getElementById('editExceptionForm:userExceptionsTable:0:missingDivisionSelectOne');
                setSelectorValue(selector, value);
            }

            function setUserStatusValue(value) {
                var selector = document.getElementById('editExceptionForm:userExceptionsTable:0:missingUserStatusSelectOne');
                setSelectorValue(selector, value);
            }

            function setUserTypeValue(value) {
                var selector = document.getElementById('editExceptionForm:userExceptionsTable:0:missingUserTypeSelectOne');
                setSelectorValue(selector, value);
            }

            function setSelectorValue(selector, value) {
                if (selector == null) {
                    return;
                }

                value = value.replace('&amp;', '&amp;');

                for (i = 0; i &lt; selector.options.length; i++) {
                    if (selector.options[i].text === value) {
                        selector.selectedIndex = i;
                        break;
                    }
                }
            }
        </script>
    </ui:define>
    <ui:define name="exceptionTab">
        <div>
            <h:outputLabel id="selectFilterLabel" value="Filter: "/>

            <!--  Select Filter ListBox -->
            <h:selectOneMenu id="filterListbox"
                             value="#{exceptionSummaryBean.activeFilter}">
                <p:ajax event="change" process="@this"
                        listener="#{exceptionSummaryBean.switchFilter}"/>
                <f:selectItems value="#{exceptionSummaryBean.availableFilters}"/>
            </h:selectOneMenu>
        </div>
        <div class="verticalSpacer"/>
        <p:outputPanel autoUpdate="true">
            <p:dataTable id="exceptionTable"
                         value="#{exceptionSummaryBean.exceptionList}" var="exception"
                         rows="#{exceptionSummaryBean.displayAmount}"
                         paginator="true"
                         paginatorPosition="bottom"
                         paginatorTemplate="{CurrentPageReport} {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink}"
                         currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
                         rowsPerPageTemplate="10,25,50,100"
                         rowIndexVar="rowIndex" rowKeyVar="rowKey"
                         scrollable="true"
                         styleClass="defaultTableHeader"
                         rowStyleClasses="oddRow, evenRow">

                <p:column id="keyIdColumn">
                    <f:facet name="header">
                        <p:commandLink value="Key ID"
                                       action="#{exceptionSummaryBean.doSort}"
                                       styleClass="headerSortLink">
                            <f:param name="column" value="keyId"/>
                        </p:commandLink>
                    </f:facet>
                    #{exception.keyId}
                </p:column>
                <p:column id="nameColumn">
                    <f:facet name="header">
                        <p:commandLink value="Name"
                                       action="#{exceptionSummaryBean.doSort}"
                                       styleClass="headerSortLink">
                            <f:param name="column" value="formattedFullName"/>
                        </p:commandLink>
                    </f:facet>
                    #{exception.formattedFullName}
                </p:column>
                <p:column id="divisionColumn">
                    <f:facet name="header">
                        <p:commandLink value="Division"
                                       action="#{exceptionSummaryBean.doSort}"
                                       styleClass="headerSortLink">
                            <f:param name="column" value="divisionName"/>
                        </p:commandLink>
                    </f:facet>
                    <p:outputPanel id="divisionDisplayPanel" autoUpdate="true">
                        <p:commandLink id="missingDivisionLink"
                                       action="#{exceptionSummaryBean.showExceptionEditPanel}"
                                       rendered="#{exception.missingDivision}" value="edit">
                            <f:param name="editId" value="#{exception.userId}"/>
                        </p:commandLink>
                        <h:outputText rendered="#{!exception.missingDivision}"
                                      value="#{exception.divisionName}"/>
                    </p:outputPanel>
                </p:column>
                <p:column id="departmentColumn">
                    <f:facet name="header">
                        <p:commandLink value="Department Name/Cost Center"
                                       action="#{exceptionSummaryBean.doSort}"
                                       styleClass="headerSortLink">
                            <f:param name="column" value="departmentNameCostCenter"/>
                        </p:commandLink>
                    </f:facet>
                    <p:outputPanel id="departmentDisplayPanel" autoUpdate="true">
                        <p:commandLink id="missingDepartmentLink"
                                       action="#{exceptionSummaryBean.showExceptionEditPanel}"
                                       rendered="#{exception.missingDepartment}" value="edit">
                            <f:param name="editId" value="#{exception.userId}"/>
                        </p:commandLink>
                        <h:outputText rendered="#{!exception.missingDepartment}"
                                      value="#{exception.departmentNameCostCenter}"/>
                    </p:outputPanel>
                </p:column>
                <p:column id="supervisorColumn">
                    <f:facet name="header">
                        <p:commandLink value="Supervisor"
                                       action="#{exceptionSummaryBean.doSort}"
                                       styleClass="headerSortLink">
                            <f:param name="column" value="supervisorName"/>
                        </p:commandLink>
                    </f:facet>
                    <p:outputPanel id="supervisorDisplayPanel" autoUpdate="true">
                        <p:commandLink id="missingSupervisor"
                                       action="#{exceptionSummaryBean.showExceptionEditPanel}"
                                       rendered="#{exception.missingSupervisor}" value="edit">
                            <f:param name="editId" value="#{exception.userId}"/>
                        </p:commandLink>
                        <h:outputText rendered="#{!exception.missingSupervisor}"
                                      value="#{exception.supervisorName}"/>
                    </p:outputPanel>
                </p:column>
                <p:column id="jobTitleColumn">
                    <f:facet name="header">
                        <p:commandLink value="Job Title"
                                       action="#{exceptionSummaryBean.doSort}"
                                       styleClass="headerSortLink">
                            <f:param name="column" value="jobTitle"/>
                        </p:commandLink>
                    </f:facet>
                    #{exception.jobTitle}
                </p:column>
                <p:column id="userTypeColumn">
                    <f:facet name="header">
                        <p:commandLink value="User Type"
                                       action="#{exceptionSummaryBean.doSort}"
                                       styleClass="headerSortLink">
                            <f:param name="column" value="userTypeName"/>
                        </p:commandLink>
                    </f:facet>
                    <p:outputPanel id="userTypeDisplayPanel" autoUpdate="true">
                        <p:commandLink id="missingUserType"
                                       action="#{exceptionSummaryBean.showExceptionEditPanel}"
                                       rendered="#{exception.missingUserType}" value="edit">
                            <f:param name="editId" value="#{exception.userId}"/>
                        </p:commandLink>
                        <h:outputText rendered="#{!exception.missingUserType}"
                                      value="#{exception.userTypeName}"/>
                    </p:outputPanel>
                </p:column>
                <p:column id="userStatusColumn">
                    <f:facet name="header">
                        <p:commandLink value="User Status"
                                       action="#{exceptionSummaryBean.doSort}"
                                       styleClass="headerSortLink">
                            <f:param name="column" value="userStatusName"/>
                        </p:commandLink>
                    </f:facet>
                    <p:outputPanel id="userStatusDisplayPanel" autoUpdate="true">
                        <p:commandLink id="missingUserStatus"
                                       action="#{exceptionSummaryBean.showExceptionEditPanel}"
                                       rendered="#{exception.missingUserStatus}" value="edit">
                            <f:param name="editId" value="#{exception.userId}"/>
                        </p:commandLink>
                        <h:outputText rendered="#{!exception.missingUserStatus}"
                                      value="#{exception.userStatusName}"/>
                    </p:outputPanel>
                </p:column>
                <p:column id="emailColumn">
                    <f:facet name="header">
                        <p:commandLink value="Email"
                                       action="#{exceptionSummaryBean.doSort}"
                                       styleClass="headerSortLink">
                            <f:param name="column" value="email"/>
                        </p:commandLink>
                    </f:facet>
                    <p:commandLink id="missingEmail"
                                   action="#{exceptionSummaryBean.showExceptionEditPanel}"
                                   rendered="#{exception.missingEmail}" value="edit">
                        <f:param name="editId" value="#{exception.userId}"/>
                    </p:commandLink>
                    <h:outputText rendered="#{!exception.missingEmail}"
                                  value="#{exception.email}"/>
                </p:column>
                <p:column id="locationColumn">
                    <f:facet name="header">
                        <p:commandLink value="Location"
                                       action="#{exceptionSummaryBean.doSort}"
                                       styleClass="headerSortLink">
                            <f:param name="column" value="location"/>
                        </p:commandLink>
                    </f:facet>
                    #{exception.location}
                </p:column>
                <p:column id="phoneColumn">
                    <f:facet name="header">
                        <p:commandLink value="Phone"
                                       action="#{exceptionSummaryBean.doSort}"
                                       styleClass="headerSortLink">
                            <f:param name="column" value="phone"/>
                        </p:commandLink>
                    </f:facet>
                    #{exception.phone}
                </p:column>
            </p:dataTable>
        </p:outputPanel>
    </ui:define>
    <ui:define name="modalPanels">
        <p:outputPanel autoUpdate="true" id="editExceptionA4jPanel">
            <p:dialog id="editException" resizeable="true"
                      draggable="true" width="1000" height="600"
                      rendered="#{exceptionSummaryBean.renderEditExceptionPanel}"
                      visible="true" style="overflow: auto;">

                <f:facet name="header">
                    <h:outputLabel value="Edit Exception"/>
                </f:facet>

                <div>
                    <h:messages id="msgId" fatalClass="fatalMessage"
                                errorClass="errorMessage" warnClass="warningMessage"
                                infoClass="infoMessage" layout="table"/>
                </div>

                <h:form id="editExceptionForm">
                    <p:dataTable id="userExceptionsTable"
                                 value="#{exceptionSummaryBean.exceptionList}" var="exception"
                                 rows="1"
                                 paginator="true" paginatorPosition="bottom"
                                 paginatorTemplate="{CurrentPageReport}  {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink}"
                                 currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
                                 rowsPerPageTemplate="10,25,50,100"
                                 rowIndexVar="rowIndex" rowKeyVar="rowKey"
                                 scrollable="true"
                                 headerClass="defaultTableHeader">

                        <p:column id="nameColumn">
                            <f:facet name="header">Name</f:facet>
                            #{exceptionSummaryBean.selectedException.formattedFullName}
                        </p:column>
                        <p:column id="divisionColumn">
                            <f:facet name="header">Division</f:facet>
                            <h:selectOneMenu id="missingDivisionSelectOne"
                                             rendered="#{exceptionSummaryBean.selectedException.missingDivision}"
                                             value="#{exceptionSummaryBean.selectedDivisionId}">
                                <f:selectItems value="#{exceptionSummaryBean.divisionSelectItems}"/>
                            </h:selectOneMenu>
                            <h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingDivision}"
                                          value="#{exceptionSummaryBean.selectedException.divisionName}"/>
                        </p:column>
                        <p:column id="departmentColumn">
                            <f:facet name="header">Department Name/Cost Center</f:facet>
                            <h:selectOneMenu id="missingDepartmentNameCostCenterSelectOne"
                                             rendered="#{exceptionSummaryBean.selectedException.missingDepartment}"
                                             value="#{exceptionSummaryBean.selectedDepartmentId}">
                                <f:selectItems value="#{exceptionSummaryBean.departmentNameCostCenterSelectItems}"/>
                            </h:selectOneMenu>
                            <h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingDepartment}"
                                          value="#{exceptionSummaryBean.selectedException.departmentNameCostCenter}"/>
                        </p:column>
                        <p:column id="userTypeColumn">
                            <f:facet name="header">User Type</f:facet>
                            <h:selectOneMenu id="missingUserTypeSelectOne"
                                             rendered="#{exceptionSummaryBean.selectedException.missingUserType}"
                                             value="#{exceptionSummaryBean.selectedUserTypeId}">
                                <f:selectItems value="#{exceptionSummaryBean.userTypeSelectItems}"/>
                            </h:selectOneMenu>
                            <h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingUserType}"
                                          value="#{exceptionSummaryBean.selectedException.userTypeName}"/>
                        </p:column>
                        <p:column id="userStatusColumn">
                            <f:facet name="header">User Status</f:facet>
                            <h:selectOneMenu id="missingUserStatusSelectOne"
                                             rendered="#{exceptionSummaryBean.selectedException.missingUserStatus}"
                                             value="#{exceptionSummaryBean.selectedUserStatusId}">
                                <f:selectItems value="#{exceptionSummaryBean.userStatusSelectItems}"/>
                            </h:selectOneMenu>
                            <h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingUserStatus}"
                                          value="#{exceptionSummaryBean.selectedException.userStatusName}"/>
                        </p:column>
                        <p:column id="emailColumn">
                            <f:facet name="header">Email</f:facet>
                            <h:inputText id="exceptionEmailInputText"
                                         value="#{exceptionSummaryBean.selectedException.email}"
                                         rendered="#{exceptionSummaryBean.selectedException.missingEmail}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                            <h:outputText id="exceptionEmailOutputText"
                                          value="#{exceptionSummaryBean.selectedException.email}"
                                          rendered="#{!exceptionSummaryBean.selectedException.missingEmail}"/>
                        </p:column>
                        <p:column id="jobTitleColumn">
                            <f:facet name="header">Job Title</f:facet>
                            #{exceptionSummaryBean.selectedException.jobTitle}
                        </p:column>
                        <p:column id="locationColumn">
                            <f:facet name="header">Location</f:facet>
                            #{exceptionSummaryBean.selectedException.location}
                        </p:column>
                    </p:dataTable>
                    <div class="verticalSpacer"/>
                    <div style="width: 100%;" id="supervisorPanel">
                        <p:outputPanel ajautoUpdate="true">
                            Supervisor:
                            <h:outputText rendered="#{!exceptionSummaryBean.selectedException.missingSupervisor}"
                                          value="#{exceptionSummaryBean.selectedException.supervisorName}"/>
                            <p:commandLink
                                    rendered="#{exceptionSummaryBean.selectedException.missingSupervisor or exceptionSummaryBean.supervisorChanged}"
                                    action="#{exceptionSummaryBean.doShowSearchSupervisorPanel}"
                                    value="Select supervisor"/>
                        </p:outputPanel>
                    </div>
                    <div class="verticalSpacer"/>
                    <p:outputPanel autoUpdate="true">
                        <p:dataTable id="directReportsTable"
                                     value="#{exceptionSummaryBean.directReportsList}"
                                     var="directReport"
                                     rows="#{exceptionSummaryBean.displayAmount}"
                                     paginator="true" paginatorPosition="bottom"
                                     paginatorTemplate="{CurrentPageReport}  {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink}"
                                     currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
                                     rowsPerPageTemplate="10,25,50,100"
                                     rowIndexVar="rowIndex" rowKeyVar="rowKey"
                                     scrollable="true"
                                     headerClass="defaultTableHeader"
                                     rowClasses="oddRow, evenRow">

                            <p:column id="directReportKeyIdColumn">
                                <f:facet name="header">
                                    <p:commandLink value="Key ID"
                                                   action="#{exceptionSummaryBean.doSortDirectReports}"
                                                   styleClass="headerSortLink" ajaxSingle="true">
                                        <f:param name="directReportColumn" value="keyId"/>
                                    </p:commandLink>
                                </f:facet>
                                #{directReport.keyId}
                            </p:column>
                            <p:column id="directReportNameColumn">
                                <f:facet name="header">
                                    <p:commandLink value="Name"
                                                   action="#{exceptionSummaryBean.doSortDirectReports}"
                                                   styleClass="headerSortLink" ajaxSingle="true">
                                        <f:param name="directReportColumn" value="formattedFullName"/>
                                    </p:commandLink>
                                </f:facet>
                                #{directReport.formattedFullName}
                            </p:column>
                            <p:column id="directReportDivisionColumn">
                                <f:facet name="header">
                                    <p:commandLink value="Division"
                                                   action="#{exceptionSummaryBean.doSortDirectReports}"
                                                   styleClass="headerSortLink" ajaxSingle="true">
                                        <f:param name="directReportColumn" value="divisionName"/>
                                    </p:commandLink>
                                </f:facet>
                                <h:outputText
                                        rendered="#{!directReport.missingDivision and !exceptionSummaryBean.selectedException.missingDivision}">
                                    #{directReport.divisionName}
                                </h:outputText>
                                <h:outputText
                                        rendered="#{!directReport.missingDivision and exceptionSummaryBean.selectedException.missingDivision}">
                                    <a href="#" onmouseup="setDivisionValue(this.innerHTML);">#{directReport.divisionName}</a>
                                </h:outputText>
                            </p:column>
                            <p:column id="directReportDepartmentColumn">
                                <f:facet name="header">
                                    <p:commandLink value="Department"
                                                   action="#{exceptionSummaryBean.doSortDirectReports}"
                                                   styleClass="headerSortLink" ajaxSingle="true">
                                        <f:param name="directReportColumn" value="departmentNameCostCenter"/>
                                    </p:commandLink>
                                </f:facet>
                                <h:outputText
                                        rendered="#{!directReport.missingDepartment and !exceptionSummaryBean.selectedException.missingDepartment}">
                                    #{directReport.departmentNameCostCenter}
                                </h:outputText>
                                <h:outputText
                                        rendered="#{!directReport.missingDepartment and exceptionSummaryBean.selectedException.missingDepartment}">
                                    <a href="#" onmouseup="setDepartmentValue(this.innerHTML);">#{directReport.departmentNameCostCenter}</a>
                                </h:outputText>
                            </p:column>
                            <p:column id="directReportUserTypeColumn">
                                <f:facet name="header">
                                    <p:commandLink value="User Type"
                                                   action="#{exceptionSummaryBean.doSortDirectReports}"
                                                   styleClass="headerSortLink" ajaxSingle="true">
                                        <f:param name="directReportColumn" value="userTypeName"/>
                                    </p:commandLink>
                                </f:facet>
                                <h:outputText
                                        rendered="#{!directReport.missingUserType and !exceptionSummaryBean.selectedException.missingUserType}">
                                    #{directReport.userTypeName}
                                </h:outputText>
                                <h:outputText
                                        rendered="#{!directReport.missingUserType and exceptionSummaryBean.selectedException.missingUserType}">
                                    <a href="#" onmouseup="setUserTypeValue(this.innerHTML);">#{directReport.userTypeName}</a>
                                </h:outputText>
                            </p:column>
                            <p:column id="directReportUserStatusColumn">
                                <f:facet name="header">
                                    <p:commandLink value="User Status"
                                                   action="#{exceptionSummaryBean.doSortDirectReports}"
                                                   styleClass="headerSortLink" ajaxSingle="true">
                                        <f:param name="directReportColumn" value="userStatusName"/>
                                    </p:commandLink>
                                </f:facet>
                                <h:outputText
                                        rendered="#{!directReport.missingUserStatus and !exceptionSummaryBean.selectedException.missingUserStatus}">
                                    #{directReport.userStatusName}
                                </h:outputText>
                                <h:outputText
                                        rendered="#{!directReport.missingUserStatus and exceptionSummaryBean.selectedException.missingUserStatus}">
                                    <a href="#" onmouseup="setUserStatusValue(this.innerHTML);">#{directReport.userStatusName}</a>
                                </h:outputText>
                            </p:column>
                            <p:column id="directReportJobTitleColumn">
                                <f:facet name="header">
                                    <p:commandLink value="Job Title"
                                                   action="#{exceptionSummaryBean.doSortDirectReports}"
                                                   styleClass="headerSortLink" ajaxSingle="true">
                                        <f:param name="directReportColumn" value="jobTitle"/>
                                    </p:commandLink>
                                </f:facet>
                                #{directReport.jobTitle}
                            </p:column>
                            <p:column id="directReportLocationColumn">
                                <f:facet name="header">
                                    <p:commandLink value="Location"
                                                   action="#{exceptionSummaryBean.doSortDirectReports}"
                                                   styleClass="headerSortLink" ajaxSingle="true">
                                        <f:param name="directReportColumn" value="location"/>
                                    </p:commandLink>
                                </f:facet>
                                #{directReport.location}
                            </p:column>
                        </p:dataTable>
                    </p:outputPanel>
                    <div class="verticalSpacer"/>
                    <div>
                        <h:commandButton id="saveButton" value="Save"
                                         action="#{exceptionSummaryBean.saveException}"/>

                        <h:commandButton id="cancelButton" value="Cancel"
                                         action="#{exceptionSummaryBean.cancelEditException}"
                                         immediate="true"/>
                    </div>
                </h:form>
            </p:dialog>
            <p:dialog id="searchSupervisorModalPanel"
                      resizeable="true"
                      draggable="true" height="500" width="750"
                      rendered="#{exceptionSummaryBean.renderSearchSupervisorModalPanel}"
                      visible="true"
                      widgetVar="searchSupervisorDialog">

                <f:facet name="header">
                    <h:outputLabel value="Search Supervisor"/>
                </f:facet>
                <f:facet name="controller">
                    <h:panelGroup>
                        <a id="hidelink" style="float: left" onclick="PF('searchSupervisorDialog').hide()">close</a>
                    </h:panelGroup>
                </f:facet>
                <h:form id="searchUserForm">
                    <div>
                        <div class="sectionHeaderSmall">
                            Search Supervisor
                        </div>
                    </div>
                    <h:panelGrid columns="6">
                        <h:outputLabel value="Key ID:"/>
                        <h:inputText id="supervisorKeyIdSearchText"
                                     value="#{exceptionSummaryBean.supervisorSearchKeyId}"
                                     onchange="this.value = this.value.toUpperCase();"/>
                        <h:outputLabel value="Last Name:"/>
                        <h:inputText id="supervisorLastNameSearchText"
                                     value="#{exceptionSummaryBean.supervisorSearchLastName}"
                                     onchange="this.value = this.value.toUpperCase();"/>
                        <h:outputLabel value="First Name:"/>
                        <h:inputText id="supervisorFirstNameSearchText"
                                     value="#{exceptionSummaryBean.supervisorSearchFirstName}"
                                     onchange="this.value = this.value.toUpperCase();"/>
                    </h:panelGrid>
                    <div>
                        <h:commandButton id="searchButton" value="Go"
                                         action="#{exceptionSummaryBean.doSupervisorSearch}"/>
                        <h:commandButton id="resetButton" value="Reset"
                                         action="#{exceptionSummaryBean.resetSupervisorSearch}"/>
                    </div>
                    <p:outputPanel autoUpdate="true">
                        <p:dataTable id="searchUserTable"
                                     value="#{exceptionSummaryBean.supervisorSearchList}"
                                     var="user" rows="10" headerClass="defaultTableHeader"
                                     paginator="true" paginatorPosition="bottom"
                                     paginatorTemplate="{CurrentPageReport}  {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink}"
                                     currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
                                     rowsPerPageTemplate="10,25,50,100"
                                     rowIndexVar="rowIndex" rowKeyVar="rowKey"
                                     scrollable="true"
                                     rowStyleClasses="oddRow, evenRow" style="width:100%">
                            <p:column>
                                <f:facet name="header">
                                    <p:commandLink value="Last Name" action="#{exceptionSummaryBean.doSupervisorSort}"
                                                   styleClass="headerSortLink">
                                        <f:param name="supervisorColumn" value="lastName"/>
                                    </p:commandLink>
                                </f:facet>
                                #{user.lastName}
                            </p:column>
                            <p:column>
                                <f:facet name="header">
                                    <p:commandLink value="First Name" action="#{exceptionSummaryBean.doSupervisorSort}"
                                                   styleClass="headerSortLink">
                                        <f:param name="supervisorColumn" value="firstName"/>
                                    </p:commandLink>
                                </f:facet>
                                #{user.firstName}
                            </p:column>
                            <p:column>
                                <f:facet name="header">
                                    <p:commandLink value="Key Id"
                                                   action="#{exceptionSummaryBean.doSupervisorSort}"
                                                   styleClass="headerSortLink">
                                        <f:param name="supervisorColumn" value="keyId"/>
                                    </p:commandLink>
                                </f:facet>
                                <p:commandLink value="#{user.keyId}"
                                               action="#{exceptionSummaryBean.doAssignSelectedSupervisor}"
                                               styleClass="headerSortLink">
                                    <f:param name="selectedSupervisorId" value="#{user.userId}"/>
                                </p:commandLink>
                            </p:column>
                            <p:column>
                                <f:facet name="header">
                                    <p:commandLink value="Division" action="#{exceptionSummaryBean.doSupervisorSort}"
                                                   styleClass="headerSortLink">
                                        <f:param name="supervisorColumn" value="divisionName"/>
                                    </p:commandLink>
                                </f:facet>
                                #{user.divisionName}
                            </p:column>
                            <p:column>
                                <f:facet name="header">
                                    <p:commandLink value="Location" action="#{exceptionSummaryBean.doSupervisorSort}"
                                                   styleClass="headerSortLink">
                                        <f:param name="supervisorColumn" value="location"/>
                                    </p:commandLink>
                                </f:facet>
                                #{user.location}
                            </p:column>
                            <p:column>
                                <f:facet name="header">
                                    <p:commandLink value="Status" action="#{exceptionSummaryBean.doSupervisorSort}"
                                                   styleClass="headerSortLink">
                                        <f:param name="supervisorColumn" value="userStatusDescription"/>
                                    </p:commandLink>
                                </f:facet>
                                #{user.userStatusDescription}
                            </p:column>
                            <p:column>
                                <f:facet name="header">
                                    <p:commandLink value="Type" action="#{exceptionSummaryBean.doSupervisorSort}"
                                                   styleClass="headerSortLink">
                                        <f:param name="supervisorColumn" value="userTypeDescription"/>
                                    </p:commandLink>
                                </f:facet>
                                #{user.userTypeDescription}
                            </p:column>
                        </p:dataTable>
                    </p:outputPanel>
                </h:form>
            </p:dialog>
        </p:outputPanel>
    </ui:define>
</ui:composition>
</html>
