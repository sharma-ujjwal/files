```
method in rejectedUserSummaryBean.java
public void populateDepartmentData(String departmentName) {
		String js = String.format("populateDepartmentData('%s');", departmentName.replace("'", "\\'"));
		PrimeFaces.current().executeScript(js);
}

<f:view xmlns="http://www.w3.org/1999/xhtml"
        xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
        xmlns:h="http://xmlns.jcp.org/jsf/html"
        xmlns:f="http://java.sun.com/jsf/core"
        xmlns:p="http://primefaces.org/ui">

    <h:form id="bodyRejectForm">
        <div class="adminAppDivCls">
            <!--  Search -->
            <p:outputPanel id="filterPanel">

                <table width="100%">
                    <tbody>
                    <tr>
                        <td align="left">
                            <h:outputLabel id="keyIdSearchLabel">Key ID:
                            </h:outputLabel>
                        </td>
                        <td align="left">

                            <h:inputText id="keyIdSearchText2" maxlength="100"
                                         value="#{rejectedUserSummaryBean.searchSrcUserId}"
                                         onchange="this.value = this.value.toUpperCase();">
                            </h:inputText>
                        </td>
                        <td align="right">
                            <h:outputLabel id="selectDateLabel"> Extracted Date:</h:outputLabel>
                            <h:selectOneMenu id="availablecreatedDateListbox"
                                             value="#{rejectedUserSummaryBean.selectedDateCriteria}">
                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.availablecreatedDateCriteria}"/>
                            </h:selectOneMenu>
                        </td>
                        <td class="right rightSpaced calenderAlign">
                            <p:calendar id="targetDateCalendar"
                                        readonlyInput="true"
                                        showOn="button" navigator="true" widgetVar="calendarWidget"
                                        value="#{rejectedUserSummaryBean.createdDateCriteria}"
                                        pattern="MM/dd/yyyy"/>
                        </td>
                        <td class="rightSpaced">
                            &#160;
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel id="selectStatusLabel">Extract System:
                            </h:outputLabel>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="extrctSysListbox"
                                             value="#{rejectedUserSummaryBean.searchExtrctSysId}">
                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.availableExtractSystem}"/>
                            </h:selectOneMenu>
                        </td>
                    </tr>

                    <tr>
                        <td colspan="5" align="left">
                            <!-- Go Button -->
                            <p:commandButton id="goSearchButton3"
                                             styleClass="plain-button"
                                             value="Go"
                                             process="@form"
                                             update="rejectedUserTable"
                                             ajax="true"
                                             action="#{rejectedUserSummaryBean.goSearch}"/>
                            <p:commandButton id="resetButton1"
                                             styleClass="plain-button"
                                             value="Reset"
                                             ajax="true"
                                             immediate="true"
                                             process="@this"
                                             update="filterPanel rejectedUserTable"
                                             action="#{rejectedUserSummaryBean.resetSearch}"/>
                            <p:commandButton id="reconcileRejectsButton"
                                             styleClass="plain-button"
                                             value="Reconcile Rejects"
                                             ajax="true"
                                             process="@this"
                                             update="bodyForm:tabPanel:rejectedUserTable"
                                             action="#{rejectedUserSummaryBean.doReconcile}"/>
                        </td>
                    </tr>
                    </tbody>
                </table>
            </p:outputPanel>
        </div>
    </h:form>
    <!-- **************************     Reject  summary table     ***********************************   -->
    <p:outputPanel styleClass="adminConfBlockDivCls">
        <p:dataTable id="rejectedUserTable"
                     value="#{rejectedUserSummaryBean.rejectedUserList}"
                     var="rejectedUser"
                     paginator="true"
                     paginatorPosition="bottom"
                     paginatorTemplate="#{not empty rejectedUserSummaryBean.rejectedUserList ? '{CurrentPageReport} {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink} {RowsPerPageDropdown}' : ''}"
                     currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
                     rowsPerPageTemplate="10,25,50,100"
                     rowIndexVar="rowIndex"
                     rowKeyVar="rowKey"
                     rows="10"
                     styleClass="defaultTableHeader" style="overflow-x:hidden"
                     rowStyleClass="oddRow, evenRow">
            <p:column>
                <f:facet name="header">
                    <p:commandLink value="Rejected User Id"
                                   action="#{rejectedUserSummaryBean.doSort}"
                                   styleClass="headerSortLink"
                                   update="rejectedUserTable"
                                   process="@this">
                        <f:param name="column" value="srcUserId"/>
                    </p:commandLink>
                </f:facet>
                #{rejectedUser.srcUserId}
            </p:column>
            <p:column>
                <f:facet name="header">
                    <p:commandLink value="User Name"
                                   action="#{rejectedUserSummaryBean.doSort}"
                                   styleClass="headerSortLink"
                                   update="rejectedUserTable"
                                   process="@this">
                        <f:param name="column" value="srcUserName"/>
                    </p:commandLink>
                </f:facet>
                #{rejectedUser.srcUserName}
            </p:column>
            <p:column>
                <f:facet name="header">
                    <p:commandLink value="Sys Environment"
                                   action="#{rejectedUserSummaryBean.doSort}"
                                   styleClass="headerSortLink"
                                   update="rejectedUserTable"
                                   process="@this">
                        <f:param name="column" value="srcApplicationName"/>
                    </p:commandLink>
                </f:facet>
                #{rejectedUser.srcApplicationName}
            </p:column>
            <p:column>
                <f:facet name="header">
                    <p:commandLink value="Additional Values"
                                   action="#{rejectedUserSummaryBean.doSort}"
                                   styleClass="headerSortLink"
                                   update="rejectedUserTable"
                                   process="@this">
                        <f:param name="column" value="additionalValues"/>
                    </p:commandLink>
                </f:facet>
                #{rejectedUser.additionalValues}
            </p:column>
            <p:column>
                <f:facet name="header">
                    <h:outputText value="Actions">
                    </h:outputText>
                </f:facet>
                <p:commandLink value="Search User"
                               action="#{rejectedUserSummaryBean.showSearchUser}"
                               styleClass="headerSortLink"
                               oncomplete="PF('searchUserModalPanelWidget').show();"
                               update="@this"
                               process="@this"
                               rendered="#{rejectedUser.showSearchUser}">
                    <f:param name="column" value="#{rejectedUser.srcUserName}"/>
                    <f:param name="rejectedIdcolumn" value="#{rejectedUser.rejectedUserId }"/>
                    <f:param name="newAltIdcolumn" value="#{rejectedUser.srcUserId }"/>
                </p:commandLink>
                <p:commandLink value="Add User | "
                               action="#{rejectedUserSummaryBean.showAddUserPanel}"
                               oncomplete="PF('addModalPanelWidget').show();"
                               styleClass="headerSortLink"
                               update="@this bodyForm:tabPanel:addUserForm"
                               process="@this"
                               rendered="#{rejectedUser.showAddUser}">
                    <f:param name="column" value="#{rejectedUser.rejectedUserId}"/>
                </p:commandLink>
                <p:commandLink value="Add Alternate Id"
                               action="#{rejectedUserSummaryBean.showAssociatedUserSearchPanel}"
                               styleClass="headerSortLink"
                               update="@this"
                               process="@this"
                               oncomplete="PF('addAlternateIdModalVar').show();"
                               rendered="#{rejectedUser.showAddUser}">
                    <f:param name="rejectedIdcolumn" value="#{rejectedUser.rejectedUserId }"/>
                    <f:param name="newAltIdcolumn" value="#{rejectedUser.srcUserId }"/>
                </p:commandLink>
            </p:column>
            <p:column>
                <f:facet name="header">
                    <p:commandLink value="Extracted Date"
                                   action="#{rejectedUserSummaryBean.doSort}"
                                   styleClass="headerSortLink"
                                   update="rejectedUserTable"
                                   process="@this">
                        <f:param name="column" value="createdDate"/>
                    </p:commandLink>
                </f:facet>
                <h:outputText value="#{rejectedUser.createdDate}">
                    <f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}"/>
                </h:outputText>
            </p:column>


        </p:dataTable>
    </p:outputPanel>

    <!-- **************************    Add NEW User ModelPanel     ***********************************   -->
    <ui:define name="styles">
        <style type="text/css">
            select {
                font-size: 1.0em;
            }

            input {
                font-size: 1.0em;
            }
        </style>
    </ui:define>
    <ui:define name="script">
        <script type="text/javascript">
            function setDepartmentValue(deptName) {
                console.log("EXECUTING DEPARTMENT VALUE...................")
                const dropdown = document.getElementById('bodyForm:tabPanel:addUserForm:departmentListbox');
                for (let i = 0; i &lt; dropdown.options.length; i++) {
                    if (dropdown.options[i].text.trim() === deptName.trim()) {
                        dropdown.selectedIndex = i;
                        dropdown.dispatchEvent(new Event('change'));
                        break;
                    }
                }
            }

            function setDivisionValue(divisionName) {
                console.log("EXECUTING DIVISION VALUE..........")
                const dropdown = document.getElementById('bodyForm:tabPanel:addUserForm:divisionListbox');
                for (let i = 0; i &lt; dropdown.options.length; i++) {
                    if (dropdown.options[i].text.trim() === divisionName.trim()) {
                        dropdown.selectedIndex = i;
                        dropdown.dispatchEvent(new Event('change'));
                        break;
                    }
                }
            }

            function setBusinessSegValue(businessSeg) {
                const dropdown = document.getElementById('bodyForm:tabPanel:addUserForm:bsnssSgmntListbox');
                for (let i = 0; i &lt; dropdown.options.length; i++) {
                    if (dropdown.options[i].text.trim() === businessSeg.trim()) {
                        dropdown.selectedIndex = i;
                        dropdown.dispatchEvent(new Event('change'));
                        break;
                    }
                }
            }

            function setJobTitleValue(jobTitle) {
                const dropdown = document.getElementById('bodyForm:tabPanel:addUserForm:jobTitleListbox');
                for (let i = 0; i &lt; dropdown.options.length; i++) {
                    if (dropdown.options[i].text.trim() === jobTitle.trim()) {
                        dropdown.selectedIndex = i;
                        dropdown.dispatchEvent(new Event('change'));
                        break;
                    }
                }
            }

            function setLocationValue(location) {
                const dropdown = document.getElementById('bodyForm:tabPanel:addUserForm:locationListbox');
                for (let i = 0; i &lt; dropdown.options.length; i++) {
                    if (dropdown.options[i].text.trim() === location.trim()) {
                        dropdown.selectedIndex = i;
                        dropdown.dispatchEvent(new Event('change'));
                        break;
                    }
                }
            }
        </script>
    </ui:define>
    <h:form id="addUserForm">
        <p:outputPanel id="adduserModalAjaxPanel">
            <p:dialog id="addModalPanel"
                      resizeable="true"
                      widgetVar="addModalPanelWidget"
                      draggable="true"
                      modal="true"
                      height="600" width="800"
                      style="overflow:auto;">

                <f:facet name="header">
                    <h:outputLabel value="Add User"/>
                </f:facet>
                <div>
                    <h:messages id="addUserMsgId" fatalClass="fatalMessage"
                                errorClass="errorMessage" warnClass="warningMessage"
                                infoClass="infoMessage" layout="table"/>
                </div>
                <table style="width:100%">
                    <tbody>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Key Id" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            #{rejectedUserSummaryBean.keyId}
                        </td>
                        <td align="left">
                            <h:outputLabel value="Last Name" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText id="addlastNameText" maxlength="50"
                                         value="#{rejectedUserSummaryBean.lastName}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="First Name" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText id="addfirstNameText" maxlength="50"
                                         value="#{rejectedUserSummaryBean.firstName}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                        <td align="left">
                            <h:outputLabel value="Middle Name" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText id="addmiddleNameText" maxlength="50"
                                         value="#{rejectedUserSummaryBean.middleName}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">

                            <h:outputLabel value="User Type" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="UserTypeListbox"
                                             value="#{rejectedUserSummaryBean.userType}">
                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.availableUserType}"/>
                            </h:selectOneMenu>
                        </td>
                        <td align="left">
                            <h:outputLabel value="Status"
                                           styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="StatusNameText"
                                             value="#{rejectedUserSummaryBean.status}" rendered="true">
                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.availableStatus}"/>
                            </h:selectOneMenu>
                        </td>
                    </tr>


                    <tr>
                        <td align="left">
                            <h:outputLabel value="Supervisor " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="supervisorListbox"
                                             value="#{rejectedUserSummaryBean.supervisorId}">
                                <f:selectItems id="searchSupervisorSelectItems"
                                               value="#{rejectedUserSummaryBean.availableSupervisors}"/>
                                <p:ajax event="change"
                                        listener="#{rejectedUserSummaryBean.populateSupevisorData}"
                                        oncomplete="PF('addModalPanelWidget').show();"
                                        process="@this"
                                        update="adduserModalAjaxPanel"/>
                            </h:selectOneMenu>

                        </td>
                        <td align="left">
                            <h:outputLabel value="Department CostCenter" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="departmentListbox"
                                             value="#{rejectedUserSummaryBean.departmentId}">

                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.supevisorDepartments}"/>
                            </h:selectOneMenu>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">

                            <h:outputLabel value="Division" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="divisionListbox"
                                             value="#{rejectedUserSummaryBean.divisionId}">
                                <f:selectItems value="#{rejectedUserSummaryBean.supevisorDivisions}"/>
                            </h:selectOneMenu>
                        </td>
                        <td align="left">
                            <h:outputLabel value="Business Segment "
                                           styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="bsnssSgmntListbox"
                                             value="#{rejectedUserSummaryBean.bsnssSgmnt}" rendered="true">
                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.supevisorBusinessSegments}"/>
                            </h:selectOneMenu>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Location " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="locationListbox"
                                             value="#{rejectedUserSummaryBean.location}" rendered="true">
                                <f:selectItems value="#{rejectedUserSummaryBean.supevisorLocations}"/>
                            </h:selectOneMenu>
                        </td>
                        <td align="left">
                            <h:outputLabel value="Job Title " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="jobTitleListbox"
                                             value="#{rejectedUserSummaryBean.jobTitle}" rendered="true">
                                <f:selectItems value="#{rejectedUserSummaryBean.supevisorJobTitles}"/>
                            </h:selectOneMenu>
                        </td>
                    </tr>

                    <tr>
                        <td align="center" colspan="4">
                            <p:outputPanel id="directReportsPanel">
                                <p:dataTable id="directReportsTable"
                                             value="#{rejectedUserSummaryBean.supervisorDirectReports}"
                                             var="report" height="100" width="700" rows="3"
                                             paginator="true" style="overflow-x:hidden; width: 100%;"
                                             paginatorPosition="bottom"
                                             paginatorTemplate="#{not empty rejectedUserSummaryBean.supervisorDirectReports  ? '{CurrentPageReport} {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink} {RowsPerPageDropdown}' : ''}"
                                             rowIndexVar="rowIndex"
                                             rowKeyVar="rowKey"
                                             styleClass="defaultTableHeader"
                                             rowStyleClass="oddRow, evenRow">

                                    <p:column>
                                        <f:facet name="header"> Name</f:facet>
                                        #{report.name}
                                    </p:column>
                                    <p:column>
                                        <f:facet name="header"> Department</f:facet>
                                        <p:commandLink value="#{report.departmentNmCostCenter}"
                                                       actionListener="#{rejectedUserSummaryBean.populateDepartmentData(report.departmentNmCostCenter)}"
                                                       process="@this"
                                                       update="@none"
                                                       oncomplete="PF('addModalPanelWidget').show();" />
                                    </p:column>
                                    <p:column>
                                        <f:facet name="header"> CostCenter</f:facet>
                                        #{report.costCenter}
                                    </p:column>
                                    <p:column>
                                        <f:facet name="header"> Division</f:facet>
                                        <a href="#"
                                           onmouseup="setDivisionValue(this.innerHTML);">
                                            #{report.division}
                                        </a>
                                    </p:column>
                                    <p:column>
                                        <f:facet name="header"> Business Segment</f:facet>
                                        <a href="#" onmouseup="setBusinessSegValue(this.innerHTML);">
                                            #{report.businessSeg}
                                        </a>
                                    </p:column>
                                    <p:column>
                                        <f:facet name="header"> Job Title</f:facet>
                                        <a href="#"
                                           onmouseup="setJobTitleValue(this.innerHTML);">
                                            #{report.jobTitle}
                                        </a>
                                    </p:column>
                                    <p:column>
                                        <f:facet name="header"> Location</f:facet>
                                        <a href="#"
                                           onmouseup="setLocationValue(this.innerHTML);">
                                            #{report.location}
                                        </a>
                                    </p:column>
                                </p:dataTable>
                            </p:outputPanel>


                        </td>
                    </tr>

                    <tr>
                        <td align="left">
                            <h:outputLabel value="Email Address " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText id="addEmailAddressText" maxlength="50"
                                         value="#{rejectedUserSummaryBean.emailAddress}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                        <td align="left">
                            <h:outputLabel value="Phone " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText id="phoneText" maxlength="50"
                                         value="#{rejectedUserSummaryBean.phone}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="SAT Comment" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText id="satCommentText" maxlength="50"
                                         value="#{rejectedUserSummaryBean.satComment}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                        <td align="left">
                            <h:outputLabel value="SAT Job Role" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText id="satJobRoleText" maxlength="50"
                                         value="#{rejectedUserSummaryBean.satJobRole}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                    </tr>

                    <tr>
                        <td align="left">
                            <h:outputLabel value="SAT Status" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText id="satStatusText" maxlength="20" tabindex="0"
                                         value="#{rejectedUserSummaryBean.satStatus}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                        <td align="left">
                            <h:outputLabel value="Extract System" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputLabel value="#{rejectedUserSummaryBean.extrctSysName}"/>
                        </td>
                    </tr>

                    <tr>
                        <td align="left" colspan="4">
                            <p:commandButton id="saveButton" value="Save"
                                             styleClass="plain-button"
                                             process="@form"
                                             update="rejectedUserTable adduserModalAjaxPanel"
                                             oncomplete="PF('addModalPanelWidget').hide()"
                                             action="#{rejectedUserSummaryBean.doAddNewUser}"/>
                            <p:commandButton id="cancelButton" value="Cancel"
                                             styleClass="plain-button"
                                             action="#{rejectedUserSummaryBean.doCancelAddUser}"
                                             process="@this" immediate="true"
                                             update="adduserModalAjaxPanel"
                                             oncomplete="PF('addModalPanelWidget').hide()" />
                        </td>
                    </tr>
                    </tbody>
                </table>
            </p:dialog>
        </p:outputPanel>
    </h:form>


    <!-- **************************    Search User ModelPanel     ***********************************   -->
    <h:form id="searchUserForm1">
        <p:outputPanel id="searchUserModalAjaxPanel">
            <p:dialog id="searchUserModalPanel" resizeable="true"
                      widgetVar="searchUserModalPanelWidget"
                      modal="true"
                      draggable="true"
                      height="500" width="750">

                <f:facet name="header">
                    <h:outputLabel value="Edit Table"/>
                </f:facet>
                <f:facet name="controller">
                    <p:outputPanel>
                        <p:commandLink id="hidelink" style="float:left" value="close"
                                       oncomplete="PF('searchUserModalPanelWidget').hide();"/>
                    </p:outputPanel>
                </f:facet>
                    <div>
                        <div class="sectionHeaderSmall">
                            Search User
                        </div>
                    </div>
                    <p:dataTable id="searchUserTable"
                                 value="#{rejectedUserSummaryBean.userList}" var="user"
                                 rows="#{rejectedUserSummaryBean.popupDisplayAmount}"
                                 styleClass="defaultTableHeader"
                                 style="overflow-x:hidden; height: inherit;"
                                 rowStyleClasses="oddRow, evenRow"
                                 paginator="true"
                                 paginatorPosition="bottom"
                                 paginatorTemplate="#{not empty rejectedUserSummaryBean.userList ? '{CurrentPageReport} {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink} {RowsPerPageDropdown}' : ''}"
                                 currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
                                 rowsPerPageTemplate="10,25,50,100"
                                 rowIndexVar="rowIndex"
                                 rowKeyVar="rowKey">
                        <p:column>
                            <f:facet name="header">
                                <p:commandLink value="Last Name"
                                               action="#{rejectedUserSummaryBean.doUserSort}"
                                               styleClass="headerSortLink"
                                               update="searchUserTable"
                                               process="@this">
                                    <f:param name="column" value="lastName"/>
                                </p:commandLink>
                            </f:facet>
                            #{user.lastName}
                        </p:column>
                        <p:column>
                            <f:facet name="header">
                                <p:commandLink value="First Name"
                                               action="#{rejectedUserSummaryBean.doUserSort}"
                                               styleClass="headerSortLink"
                                               update="searchUserTable"
                                               process="@this">
                                    <f:param name="column" value="firstName"/>
                                </p:commandLink>
                            </f:facet>
                            #{user.firstName}
                        </p:column>
                        <p:column>
                            <f:facet name="header">
                                <p:commandLink value="Key Id"
                                               action="#{rejectedUserSummaryBean.doUserSort}"
                                               styleClass="headerSortLink"
                                               update="searchUserTable"
                                               process="@this">
                                    <f:param name="column" value="keyId"/>
                                </p:commandLink>
                            </f:facet>
                            <p:commandLink value="#{user.keyId}"
                                           action="#{rejectedUserSummaryBean.showAddSearchedUserPanel}"
                                           styleClass="headerSortLink">
                                <f:param name="column" value="#{user.keyId}"/>
                            </p:commandLink>
                        </p:column>
                        <p:column>
                            <f:facet name="header">
                                <p:commandLink value="Division"
                                               action="#{rejectedUserSummaryBean.doUserSort}"
                                               styleClass="headerSortLink"
                                               update="searchUserTable"
                                               process="@this">
                                    <f:param name="column" value="division"/>
                                </p:commandLink>
                            </f:facet>
                            #{user.division}
                        </p:column>
                        <p:column>
                            <f:facet name="header">
                                <p:commandLink value="Location"
                                               action="#{rejectedUserSummaryBean.doUserSort}"
                                               styleClass="headerSortLink"
                                               update="searchUserTable"
                                               process="@this">
                                    <f:param name="column" value="location"/>
                                </p:commandLink>
                            </f:facet>
                            #{user.location}
                        </p:column>
                        <p:column>
                            <f:facet name="header">
                                <p:commandLink value="Status"
                                               action="#{rejectedUserSummaryBean.doUserSort}"
                                               styleClass="headerSortLink"
                                               update="searchUserTable"
                                               process="@this">
                                    <f:param name="column" value="status"/>
                                </p:commandLink>
                            </f:facet>
                            #{user.status}
                        </p:column>
                        <p:column>
                            <f:facet name="header">
                                <p:commandLink value="Type"
                                               action="#{rejectedUserSummaryBean.doUserSort}"
                                               styleClass="headerSortLink"
                                               update="searchUserTable"
                                               process="@this">
                                    <f:param name="column" value="type"/>
                                </p:commandLink>
                            </f:facet>
                            #{user.type}
                        </p:column>
                    </p:dataTable>
                    <table width="100%">
                        <tbody>
                        <tr>
                            <td align="left">
                                <p:commandButton id="addButton" value="Add User" styleClass="plain-button"
                                                 action="#{rejectedUserSummaryBean.showAddUserPanelFromSearch}"
                                                 oncomplete="PF('addSearchedUserModalAjaxPanelVar').show();"
                                                 ajax="true"
                                                 immediate="true"
                                                 process="@this"
                                                 update="bodyForm:tabPanel:addSearchedUserForm:addSearchedUserModalAjaxPanel" />
                                <p:commandButton id="addAltButton" value="Add AlternateId" styleClass="plain-button"
                                                 immediate="true"
                                                 ajax="true"
                                                 oncomplete="PF('associatedUserSearchModalAjaxVar').show();"
                                                 process="@this"
                                                 update="bodyForm:tabPanel:addSearchedAssociatedUserForm:associatedUserSearchModalAjaxPanel"
                                                 action="#{rejectedUserSummaryBean.showAltUserSearchPanel}"/>
                                <p:commandButton id="cancelButton" value="Cancel" styleClass="plain-button"
                                                 action="#{rejectedUserSummaryBean.doCancelSearchUser}"
                                                 oncomplete="PF('searchUserModalPanelWidget').hide();"
                                                 immediate="true"
                                                 process="@this"
                                                 update="searchUserTable"/>
                            </td>
                        </tr>
                        </tbody>
                    </table>
            </p:dialog>
        </p:outputPanel>
    </h:form>


    <!-- **************************    Add Searched User ModelPanel     ***********************************   -->
    <h:form id="addSearchedUserForm">
        <p:outputPanel id="addSearchedUserModalAjaxPanel">
            <p:dialog id="addSearchedUserModalPanel"
                  widgetVar="addSearchedUserModalAjaxPanelVar"
                  modal="true"
                  resizeable="true"
                  draggable="true" height="550" width="500">

            <f:facet name="header">
                <h:outputLabel value="Edit Table"/>
            </f:facet>
            <f:facet name="controller">
                <p:outputPanel>
                    <p:commandLink id="hidelinkOne" style="float: left" value="close"
                                   oncomplete="PF('addSearchedUserModalAjaxPanelVar').hide();"/>
                </p:outputPanel>
            </f:facet>

            <!--   Messages                    -->
            <div>
                <h:messages id="AddSerchedUserMsgId"
                            fatalClass="fatalMessage"
                            errorClass="errorMessage"
                            warnClass="warningMessage"
                            infoClass="infoMessage"
                            layout="table"/>
            </div>
                <div>
                    <div class="sectionHeaderSmall">
                        Add User
                    </div>
                </div>
                <table width="100%">
                    <tbody>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="KeyId " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputLabel value="#{rejectedUserSummaryBean.keyId}"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="First Name " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText maxlength="30"
                                         value="#{rejectedUserSummaryBean.firstName}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Middle Name  " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText maxlength="30"
                                         value="#{rejectedUserSummaryBean.middleName}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Last Name " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText maxlength="30"
                                         value="#{rejectedUserSummaryBean.lastName}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Email Address " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText maxlength="30"
                                         value="#{rejectedUserSummaryBean.emailAddress}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">

                            <h:outputLabel value="User Type" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="UserTypeListbox"
                                             value="#{rejectedUserSummaryBean.userType}">
                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.availableUserType}"/>
                            </h:selectOneMenu>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Supervisor " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="supervisorListbox"
                                             value="#{rejectedUserSummaryBean.supervisorId}">
                                <f:selectItems id="searchSupervisorSelectItems"
                                               value="#{rejectedUserSummaryBean.availableSupervisors}"/>
                                <p:ajax event="change"
                                        listener="#{rejectedUserSummaryBean.populateSupevisorData}"
                                        update="@form"/>
                            </h:selectOneMenu>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Department-CostCenter" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="DepartmentListbox"
                                             value="#{rejectedUserSummaryBean.departmentId}">
                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.supevisorDepartments}"/>
                            </h:selectOneMenu>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">

                            <h:outputLabel value="Division" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="DivisiontListbox"
                                             value="#{rejectedUserSummaryBean.divisionId}">


                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.supevisorDivisions}"/>
                            </h:selectOneMenu>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Job Title " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="jobTitleListbox"
                                             value="#{rejectedUserSummaryBean.jobTitle}" rendered="true">
                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.supevisorJobTitles}"/>
                            </h:selectOneMenu>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Location " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="locationListbox"
                                             value="#{rejectedUserSummaryBean.location}" rendered="true">
                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.supevisorLocations}"/>
                            </h:selectOneMenu>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Business Segment "
                                           styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="bsnssSgmntListbox"
                                             value="#{rejectedUserSummaryBean.bsnssSgmnt}"
                                             rendered="true">
                                <f:selectItems value="#{rejectedUserSummaryBean.supevisorBusinessSegments}"/>
                            </h:selectOneMenu>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Phone " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText maxlength="30"
                                         value="#{rejectedUserSummaryBean.phone}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel styleClass="fieldLabel" id="selectStatusLabel">Status:
                            </h:outputLabel>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="StatusListbox"
                                             value="#{rejectedUserSummaryBean.status}">
                                <f:selectItems value="#{rejectedUserSummaryBean.availableStatus}"/>
                            </h:selectOneMenu>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Sat Status " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText maxlength="20"
                                         value="#{rejectedUserSummaryBean.satStatus}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="SAT Comment" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText id="satCommentText"
                                         maxlength="50"
                                         value="#{userSummaryBean.satComment}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="SAT Job Role" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:inputText id="satJobRoleText" maxlength="50"
                                         value="#{userSummaryBean.satJobRole}"
                                         onchange="this.value = this.value.toUpperCase();"/>
                        </td>
                    </tr>

                    <tr>
                        <td align="left">
                            <p:commandButton id="saveButton"
                                             value="Save"
                                             styleClass="plain-button"
                                             process="@this"
                                             update="rejectedUserTable addSearchedUserModalAjaxPanel"
                                             oncomplete="PF('addSearchedUserModalAjaxPanelVar').hide()"
                                             action="#{rejectedUserSummaryBean.doAddUser}"/>
                        </td>
                        <td align="left">
                            <p:commandButton value="Cancel"
                                             styleClass="plain-button"
                                             immediate="true"
                                             process="@this"
                                             update="addSearchedUserModalAjaxPanel"
                                             oncomplete="PF('addSearchedUserModalAjaxPanelVar').hide()"
                                             action="#{rejectedUserSummaryBean.doCancelAddSearhedUser}"/>
                        </td>
                    </tr>
                    </tbody>
                </table>
            </p:dialog>
        </p:outputPanel>
    </h:form>


    <!-- **************************   April 8     ***********************************   -->


    <!-- **************************   Search associated User ModelPanel     ***********************************   -->
    <h:form id="addSearchedAssociatedUserForm">
        <p:outputPanel id="associatedUserSearchModalAjaxPanel">
            <p:dialog id="associatedUserSearchModalPanel"
                      widgetVar="associatedUserSearchModalAjaxVar"
                      resizeable="true"
                      modal="true"
                      draggable="true"
                      height="450" width="800">

                <f:facet name="header">
                    <h:outputLabel value="Search User"/>
                </f:facet>
                <f:facet name="controller">
                    <p:outputPanel>
                        <p:commandLink id="hidelinkTwo" style="float: left" value="close"
                                       oncomplete="PF('associatedUserSearchModalAjaxVar').hide();"/>
                    </p:outputPanel>
                </f:facet>

                <div>
                    <h:messages id="SerchedAssociatedUserMsgId"
                                fatalClass="fatalMessage"
                                errorClass="errorMessage"
                                warnClass="warningMessage"
                                infoClass="infoMessage"
                                layout="table"/>
                </div>

                <div>
                    <div class="sectionHeaderSmall">
                        Filter By name or User Id
                    </div>
                </div>
                <table width="100%">
                    <tbody>
                    <tr>
                        <td align="left">
                            <h:outputLabel id="firstNameLabel">First Name:
                            </h:outputLabel>
                        </td>
                        <td align="left">
                            <h:inputText id="firstNameText" maxlength="100"
                                         value="#{rejectedUserSummaryBean.firstName}"
                                         onchange="this.value = this.value.toUpperCase();">
                            </h:inputText>
                        </td>
                        <td align="left">
                            <h:outputLabel id="LastNameLabel">Last Name:
                            </h:outputLabel>
                        </td>
                        <td align="left">
                            <h:inputText id="lastNameText" maxlength="100"
                                         value="#{rejectedUserSummaryBean.lastName}"
                                         onchange="this.value = this.value.toUpperCase();">
                            </h:inputText>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel id="keyIdLabel">Key Id:
                            </h:outputLabel>
                        </td>
                        <td align="left">
                            <h:inputText id="keyIdText" maxlength="100"
                                         value="#{rejectedUserSummaryBean.keyId}"
                                         onchange="this.value = this.value.toUpperCase();">
                            </h:inputText>
                        </td>
                        <td align="left">
                            <h:outputLabel id="SupervisorLabel">Supervisor:
                            </h:outputLabel>
                        </td>
                        <td align="left">
                            <h:selectOneMenu id="supervisorListbox"
                                             value="#{rejectedUserSummaryBean.supervisorId}">
                                <f:selectItems id="searchSupervisorSelectItems"
                                               value="#{rejectedUserSummaryBean.availableSupervisors}"/>
                            </h:selectOneMenu>
                        </td>
                    </tr>

                    <tr>
                        <td colspan="100%">
                            <hr width="100%"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left" class="sectionHeaderSmall" colspan="100%">
                            Filter User By
                        </td>
                    </tr>
                    <tr>
                        <td colspan="100%">
                            If nothing is selected, all values are included. User
                            ctrl+click to select more than one value.
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel id="StatusLabel">Status</h:outputLabel>
                        </td>
                        <td>
                            <h:selectManyListbox
                                    value="#{rejectedUserSummaryBean.searchStatuses}"
                                    converter="javax.faces.Long">
                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.multiSelectUserStatusList}"/>
                            </h:selectManyListbox>
                        </td>
                        <td align="left">
                            <h:outputLabel id="selectDepartmentLabel">Department:
                            </h:outputLabel>
                        </td>
                        <td align="left">
                            <h:selectManyListbox id="DepartmentListbox"
                                                 value="#{rejectedUserSummaryBean.searchDepartments}" size="3"
                                                 converter="javax.faces.Long">
                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.multiSelectDepartmentList}"/>
                            </h:selectManyListbox>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">

                            <h:outputLabel id="userTypeLabel">User Type:
                            </h:outputLabel>
                        </td>
                        <td align="left">
                            <h:selectManyListbox
                                    value="#{rejectedUserSummaryBean.searchUserTypes}" size="3"
                                    converter="javax.faces.Long">
                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.multiSelectUserTypeList}"/>
                            </h:selectManyListbox>
                        </td>
                        <td align="left">
                            <h:outputLabel id="selectDivisionLabel">Division:
                            </h:outputLabel>
                        </td>
                        <td align="left">
                            <h:selectManyListbox id="DivisiontListbox"
                                                 value="#{rejectedUserSummaryBean.searchDivisions}" size="3"
                                                 converter="javax.faces.Long">
                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.multiSelectDivisionList}"/>
                            </h:selectManyListbox>
                        </td>
                        <td align="left"></td>
                    </tr>
                    <tr>
                        <td colspan="100%">
                            <hr width="100%"/>
                        </td>
                    </tr>
                    <tr>
                        <td>
                            <p:commandButton id="deleteButton"
                                             styleClass="plain-button"
                                             value="Continue"
                                             action="#{rejectedUserSummaryBean.doSearchAssociatedUser}"
                                             process="@form"
                                             oncomplete="PF('searchAssociatedUserModalVar').show();"
                                             update="associatedUserSearchModalAjaxPanel bodyForm:tabPanel:associatedUserForm:searchAssociatedUserTable"/>
                        </td>
                        <td>
                            <p:commandButton id="cancelDeleteButton"
                                             value="Cancel"
                                             styleClass="plain-button"
                                             oncomplete="PF('associatedUserSearchModalAjaxVar').hide();"
                                             process="@this"
                                             update="associatedUserSearchModalAjaxPanel"
                                             ajax="true"
                                             immediate="true"
                                             action="#{rejectedUserSummaryBean.doCancelSearchAssociatedUser}"/>
                        </td>
                    </tr>
                    </tbody>
                </table>
            </p:dialog>
        </p:outputPanel>
    </h:form>

    <!-- **************************    Searched Associated User ModelPanel     ***********************************   -->
    <h:form id="associatedUserForm">
        <p:outputPanel id="searchAssociatedUserModalAjaxPanel">
            <p:dialog id="searchAssociatedUserModalPanel"
                      widgetVar="searchAssociatedUserModalVar"
                      modal="true"
                      resizeable="true" draggable="true" height="500" width="800">
            <f:facet name="header">
                <h:outputLabel value="Search User"/>
            </f:facet>
            <f:facet name="controller">
                <p:outputPanel>
                    <p:commandLink id="hidelinkThree" style="float: left" value="close"
                                   oncomplete="PF('searchAssociatedUserModalVar').hide();"/>
                </p:outputPanel>
            </f:facet>
                <p:dataTable id="searchAssociatedUserTable"
                             value="#{rejectedUserSummaryBean.userList}" var="user"
                             paginator="true"
                             paginatorTemplate="{CurrentPageReport}  {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink} {RowsPerPageDropdown}"
                             currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
                             rowsPerPageTemplate="10,25,50,100"
                             paginatorPosition="bottom"
                             rowIndexVar="rowIndex"
                             rowKey="#{user.keyId}"
                             rows="10"
                             styleClass="defaultTableHeader"
                             rowStyleClasses="oddRow, evenRow"
                             style="width=100%">
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="Last Name"
                                           action="#{rejectedUserSummaryBean.doUserSort}"
                                           styleClass="headerSortLink"
                                           update="searchAssociatedUserTable"
                                           process="@this">
                                <f:param name="column" value="lastName"/>
                            </p:commandLink>
                        </f:facet>
                        #{user.lastName}
                    </p:column>
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="First Name"
                                           action="#{rejectedUserSummaryBean.doUserSort}"
                                           styleClass="headerSortLink"
                                           update="searchAssociatedUserTable"
                                           process="@this">
                                <f:param name="column" value="firstName"/>
                            </p:commandLink>
                        </f:facet>
                        #{user.firstName}
                    </p:column>
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="Key Id"
                                           action="#{rejectedUserSummaryBean.doUserSort}"
                                           styleClass="headerSortLink"
                                           update="searchAssociatedUserTable"
                                           process="@this">
                                <f:param name="column" value="keyId"/>
                            </p:commandLink>
                        </f:facet>
                        <p:commandLink value="#{user.keyId}"
                                       action="#{rejectedUserSummaryBean.showAddAlternateIdPanel}"
                                       process="@this"
                                       oncomplete="PF('searchAssociatedUserModalVar').hide(); PF('addAlternateIdModalVar').show();"
                                       update="bodyForm:tabPanel:addAlternateIdForm:addAddAlternateIdModalAjaxPanel"
                                       styleClass="headerSortLink">
                            <f:param name="column" value="#{user.keyId}"/>
                        </p:commandLink>
                    </p:column>


                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="Division"
                                           action="#{rejectedUserSummaryBean.doUserSort}"
                                           styleClass="headerSortLink"
                                           update="searchAssociatedUserTable"
                                           process="@this">
                                <f:param name="column" value="division"/>
                            </p:commandLink>
                        </f:facet>
                        #{user.division}
                    </p:column>
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="Location"
                                           action="#{rejectedUserBean.doUserSort}"
                                           styleClass="headerSortLink"
                                           update="searchAssociatedUserTable"
                                           process="@this">
                                <f:param name="column" value="location"/>
                            </p:commandLink>
                        </f:facet>
                        #{user.location}
                    </p:column>
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="Status"
                                           action="#{rejectedUserSummaryBean.doUserSort}"
                                           styleClass="headerSortLink"
                                           update="searchAssociatedUserTable"
                                           process="@this">
                                <f:param name="column" value="status"/>
                            </p:commandLink>
                        </f:facet>
                        #{user.status}
                    </p:column>
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="Type"
                                           action="#{rejectedUserSummaryBean.doUserSort}"
                                           styleClass="headerSortLink"
                                           update="searchAssociatedUserTable"
                                           process="@this">
                                <f:param name="column" value="type"/>
                            </p:commandLink>
                        </f:facet>
                        #{user.type}
                    </p:column>
                </p:dataTable>

                <table style="width:100%">
                    <tbody>
                    <tr>

                        <td align="left">
                            <p:commandButton id="cancelButton"
                                             value="Cancel"
                                             styleClass="plain-button"
                                             update="@this"
                                             process="@this"
                                             oncomplete="PF('searchAssociatedUserModalVar').hide();"
                                             immediate="true"
                                             action="#{rejectedUserSummaryBean.doCancelAssociatedUserSearch}"/>
                        </td>
                    </tr>
                    </tbody>
                </table>
            </p:dialog>
        </p:outputPanel>
    </h:form>

    <!-- **************************    Add Alternate Id ModelPanel     ***********************************   -->

    <h:form id="addAlternateIdForm">
        <p:outputPanel id="addAddAlternateIdModalAjaxPanel">
            <p:dialog id="AddAlternateIdPanel" resizeable="true"
                      widgetVar="addAlternateIdModalVar"
                      modal="true"
                      draggable="true"
                      height="420" width="750">

                <f:facet name="header">
                    <h:outputLabel value="Add Alternate Id"/>
                </f:facet>

                <!--   Messages                    -->
                <div>
                    <h:messages id="addAlternateIdMsg"
                                fatalClass="fatalMessage"
                                errorClass="errorMessage"
                                warnClass="warningMessage"
                                infoClass="infoMessage"
                                layout="table"/>
                </div>
                <table align="left">
                    <tbody>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Key Id " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.keyId}" />
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="First Name " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.firstName}" />
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Middle Name" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.middleName}" />
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Last Name " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.lastName}" />
                        </td>
                    </tr>

                    <tr>
                        <td align="left">
                            <h:outputLabel value="User Type " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.userTypeDescription}" />
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Department " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.departmentName}" />
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Division " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.divisionName}" />
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Location " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.location}" />
                        </td>
                    </tr>

                    <tr>
                        <td align="left">
                            <h:outputLabel value="JobTitle " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.jobTitle}" />
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Supervisor " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.supervisorName}" />
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Cost Center " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.costCenter}" />
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Phone " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.phone}" />
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Status " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.userStatusDescription}" />
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Sat Status " styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.satStatus}" />
                        </td>
                    </tr>

                    <tr>
                        <td align="left">
                            <p:commandButton id="saveButton" value="Save"
                                             styleClass="plain-button"
                                             process="@this"
                                             ajax="true"
                                             update="addAlternateIdForm bodyForm:tabPanel:rejectsTab bodyForm:headerMessages"
                                             oncomplete="PF('addAlternateIdModalVar').hide();"
                                             action="#{rejectedUserSummaryBean.doAddAlternateId}">
                            </p:commandButton>
                        </td>
                        <td align="left">
                            <p:commandButton id="cancelButton" value="Cancel"
                                             styleClass="plain-button"
                                             ajax="true"
                                             process="@this"
                                             immediate="true"
                                             update="addAlternateIdForm"
                                             oncomplete="PF('addAlternateIdModalVar').hide();"
                                             action="#{rejectedUserSummaryBean.doCancelAddAlternateId}"/>
                        </td>
                    </tr>
                    </tbody>
                </table>

                <table align="left">
                    <tr>
                        <td align="left" colspan="2">
                            <h:outputLabel value="New Alternate Id "
                                           styleClass="fieldLabel"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:selectOneMenu id="alternateIdListbox"
                                             value="#{rejectedUserSummaryBean.internalIdentifierTypeCD}">
                                <f:selectItems
                                        value="#{rejectedUserSummaryBean.availableInternalIdentifierType}"/>

                            </h:selectOneMenu>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.newAltId}"/>
                        </td>

                    </tr>
                    <tr>
                        <td align="left" colspan="2">
                            <h:outputLabel value="Existing Alternate Id(s) "
                                           styleClass="fieldLabel"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="MF ID" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.satMFId}"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="CF ID" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.satCFId}"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="GF ID" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.satGFId}"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="LCS ID" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.satLCSId}"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="FDMSId" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.satFDMSId}"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="SiteMinderId" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.satSiteMinderId}"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Alternate Id1" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.satAltId1}"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Alternate Id2" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.satAltId2}"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Alternate Id3" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.satAltId3}"/>
                        </td>
                    </tr>
                    <tr>
                        <td align="left">
                            <h:outputLabel value="Email Address" styleClass="fieldLabel"/>
                        </td>
                        <td align="left">
                            <h:outputText value="#{rejectedUserSummaryBean.emailAddress}"/>
                        </td>
                    </tr>
                </table>
            </p:dialog>
        </p:outputPanel>
    </h:form>
</f:view>
