```
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
                                <h:commandButton id="goSearchButton3"
                                                 value="Go"
                                                 action="#{rejectedUserSummaryBean.goSearch}"/>
                                <h:commandButton id="resetButton1"
                                                 value="Reset"
                                                 action="#{rejectedUserSummaryBean.resetSearch}"/>
                                <h:commandButton id="reconcileRejectsButton"
                                                 value="Reconcile Rejects"
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
                         styleClass="defaultTableHeader" tableStyle="width: auto;" style="overflow-x:hidden"
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
                                   oncomplete="PF('searchUserModalPanelWidget').show()"
                                   update="searchUserModalAjaxPanel"
                                   process="@this"
                                   rendered="#{rejectedUser.showSearchUser}">
                        <f:param name="column" value="#{rejectedUser.srcUserName}"/>
                        <f:param name="rejectedIdcolumn" value="#{rejectedUser.rejectedUserId }"/>
                        <f:param name="newAltIdcolumn" value="#{rejectedUser.srcUserId }"/>
                    </p:commandLink>
                    <p:commandLink value="Add User | "
                                   action="#{rejectedUserSummaryBean.showAddUserPanel}"
                                   styleClass="headerSortLink"
                                   rendered="#{rejectedUser.showAddUser}">
                        <f:param name="column" value="#{rejectedUser.rejectedUserId}"/>
                    </p:commandLink>
                    <p:commandLink value="Add Alternate Id"
                                   action="#{rejectedUserSummaryBean.showAssociatedUserSearchPanel}"
                                   styleClass="headerSortLink"
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
                /* <![CDATA[ */
                function setDepartmentValue(value) {
                    var selector = document.getElementById('addUserForm:departmentListbox');
                    setSelectorValue(selector, value);
                }

                function setDivisionValue(value) {
                    var selector = document.getElementById('addUserForm:divisionListbox');
                    setSelectorValue(selector, value);
                }

                function setBusinessSegValue(value) {
                    var selector = document.getElementById('addUserForm:bsnssSgmntListbox');
                    setSelectorValue(selector, value);
                }

                function setLocationValue(value) {
                    var selector = document.getElementById('addUserForm:locationListbox');
                    setSelectorValue(selector, value);
                }

                function setJobTitleValue(value) {
                    var selector = document.getElementById('addUserForm:jobTitleListbox');
                    setSelectorValue(selector, value);
                }

                function setSelectorValue(selector, value) {
                    if (selector == null) {
                        return;
                    }
                    value = value.replace('&amp;', '&');

                    for (i = 0; i < selector.options.length; i++) {
                        if (selector.options[i].text == value) {
                            selector.selectedIndex = i;
                            break;
                        }
                    }
                }

                /* ]]> */
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
                          rendered="#{rejectedUserSummaryBean.renderAddUserModalPanel}"
                          style="overflow:auto;">

                    <f:facet name="header">
                        <h:outputLabel value="Add User"/>
                    </f:facet>
                    <!--   Messages                    -->
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
                                                 paginator="true" tableStyle="width: auto;" style="overflow-x:hidden"
                                                 paginatorPosition="bottom"
                                                 paginatorTemplate="#{not empty rejectedUserSummaryBean.supervisorDirectReports  ? '{CurrentPageReport} {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink} {RowsPerPageDropdown}' : ''}"
                                                 currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
                                                 rowsPerPageTemplate="10,25,50,100"
                                                 rowIndexVar="rowIndex"
                                                 rowKeyVar="rowKey"
                                                 scrollable="true"
                                                 styleClass="defaultTableHeader"
                                                 rowStyleClass="oddRow, evenRow">

                                        <p:column>
                                            <f:facet name="header"> Name</f:facet>
                                            #{report.name}
                                        </p:column>
                                        <p:column>
                                            <f:facet name="header"> Department</f:facet>
                                            <a href="#" onmouseup="setDepartmentValue(this.innerHTML);">#{report.departmentNmCostCenter}</a>
                                        </p:column>
                                        <p:column>
                                            <f:facet name="header"> CostCenter</f:facet>
                                            #{report.costCenter}
                                        </p:column>
                                        <p:column>
                                            <f:facet name="header"> Division</f:facet>
                                            <a href="#"
                                               onmouseup="setDivisionValue(this.innerHTML);">#{report.division}</a>
                                        </p:column>
                                        <p:column>
                                            <f:facet name="header"> Business Segment</f:facet>
                                            <a href="#" onmouseup="setBusinessSegValue(this.innerHTML);">#{report.businessSeg}</a>
                                        </p:column>
                                        <p:column>
                                            <f:facet name="header"> Job Title</f:facet>
                                            <a href="#"
                                               onmouseup="setJobTitleValue(this.innerHTML);">#{report.jobTitle}</a>
                                        </p:column>
                                        <p:column>
                                            <f:facet name="header"> Location</f:facet>
                                            <a href="#"
                                               onmouseup="setLocationValue(this.innerHTML);">#{report.location}</a>
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
                                                 process="@form"
                                                 update="rejectedUserTable adduserModalAjaxPanel"
                                                 oncomplete="PF('addModalPanelWidget').hide()"
                                                 action="#{rejectedUserSummaryBean.doAddNewUser}"/>
                                <p:commandButton id="cancelButton" value="Cancel"
                                                 action="#{rejectedUserSummaryBean.doCancelAddUser}"
                                                 process="@this" immediate="true"
                                                 update="searchUserModalAjaxPanel"
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
                        <h:panelGroup>
                            <p:commandLink id="hidelink" style="float:left" value="close"
                                           oncomplete="PF('searchUserModalAjaxPanelVar').hide();"/>
                        </h:panelGroup>
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
                                     rowStyleClasses="oddRow, evenRow"
                                     style="width:100%">
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
                                                     immediate="true"
                                                     process="@this"
                                                     update="searchUserForm1"
                                                     oncomplete="PF('addUserModalAjaxPanel').show();"/>
                                    <p:commandButton id="addAltButton" value="Add AlternateId" styleClass="plain-button"
                                                     immediate="true"
                                                     oncomplete="PF('associatedUserSearchModalAjaxVar').show();"
                                                     process="@this"
                                                     update="searchUserForm1"
                                                     action="#{rejectedUserSummaryBean.showAltUserSearchPanel}"/>
                                    <p:commandButton id="cancelButton" value="Cancel" styleClass="plain-button"
                                                     action="#{rejectedUserSummaryBean.doCancelSearchUser}"
                                                     oncomplete="PF('searchUserModalPanelWidget').hide();"
                                                     immediate="true"
                                                     process="@this"
                                                     update="@form"/>
                                </td>
                            </tr>
                            </tbody>
                        </table>
                </p:dialog>
            </p:outputPanel>
        </h:form>


        <!-- **************************    Add Searched User ModelPanel     ***********************************   -->
        <h:form id="addSeachedUserForm">
            <p:outputPanel id="addSearchedUserModalAjaxPanel">
                <p:dialog id="addSeachedUserModalPanel"
                      widgetVar="addUserModalAjaxPanel"
                      modal="true"
                      resizeable="true"
                      draggable="true" height="550" width="500"
                      style="display: none;">

                <f:facet name="header">
                    <h:outputLabel value="Edit Table"/>
                </f:facet>
                <f:facet name="controller">
                    <h:panelGroup>
                        <p:commandLink id="hidelinkOne" style="float: left" value="close"
                                       oncomplete="PF('addSearchedUserModalAjaxPanel').hide();"/>
                    </h:panelGroup>
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
                                #{rejectedUserSummaryBean.keyId}
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
                                <h:commandButton id="saveButton"
                                                 value="Save"
                                                 action="#{rejectedUserSummaryBean.doAddUser}"/>
                            </td>
                            <td align="left">
                                <h:commandButton value="Cancel"
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
        <h:form id="addSeachedAssociatedUserForm">
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
                        <h:panelGroup>
                            <p:commandLink id="hidelinkTwo" style="float: left" value="close"
                                           oncomplete="PF('associatedUserSearchModalAjaxPanel').hide();"/>
                        </h:panelGroup>
                    </f:facet>

                    <!--   Messages                    -->
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
                                                 update="associatedUserSearchModalAjaxPanel"/>
                            </td>
                            <td>
                                <p:commandButton id="cancelDeleteButton"
                                                 value="Cancel"
                                                 styleClass="plain-button"
                                                 oncomplete="PF('associatedUserSearchModalPanel').hide();"
                                                 process="@this"
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
                          resizeable="true" draggable="true" height="500" width="800"
                          rendered="#{rejectedUserSummaryBean.renderSearchedAssociatedUserModalPanel}"
                          visible="true">
                <f:facet name="header">
                    <h:outputLabel value="Search User"/>
                </f:facet>
                <f:facet name="controller">
                    <h:panelGroup>
                        <p:commandLink id="hidelinkThree" style="float: left" value="close"
                                       oncomplete="PF('searchAssociatedUserModalAjaxPanel').hide();"/>
                    </h:panelGroup>
                </f:facet>
                    <p:dataTable id="searchAssociatedUserTable"
                                 value="#{rejectedUserSummaryBean.userList}" var="user"
                                 rows="#{rejectedUserSummaryBean.popupDisplayAmount}"
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
                          height="420" width="750"
                          style="display: none;">

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
                                #{rejectedUserSummaryBean.keyId}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="First Name " styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.firstName}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="Middle Name" styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.middleName}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="Last Name " styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.lastName}
                            </td>
                        </tr>

                        <tr>
                            <td align="left">
                                <h:outputLabel value="User Type " styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.userTypeDescription}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="Department " styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.departmentName}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="Division " styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.divisionName}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="Location " styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.location}
                            </td>
                        </tr>

                        <tr>
                            <td align="left">
                                <h:outputLabel value="JobTitle " styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.jobTitle}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="Supervisor " styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.supervisorName}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="Cost Center " styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.costCenter}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="Phone " styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.phone}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="Status " styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.userStatusDescription}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="Sat Status " styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.satStatus}
                            </td>
                        </tr>

                        <tr>
                            <td align="left">
                                <p:commandButton id="saveButton" value="Save"
                                                 styleClass="plain-button"
                                                 process="@this"
                                                 immediate="true"
                                                 update="@this"
                                                 oncomplete="PF('addAlternateIdModalVar').hide();"
                                                 action="#{rejectedUserSummaryBean.doAddAlternateId}">
                                </p:commandButton>
                            </td>
                            <td align="left">
                                <p:commandButton id="cancelButton" value="Cancel"
                                                 styleClass="plain-button"
                                                 process="@this"
                                                 immediate="true"
                                                 update="@this"
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
                                #{rejectedUserSummaryBean.newAltId}
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
                                #{rejectedUserSummaryBean.satMFId}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="CF ID" styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.satCFId}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="GF ID" styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.satGFId}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="LCS ID" styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.satLCSId}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="FDMSId" styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.satFDMSId}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="SiteMinderId" styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.satSiteMinderId}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="Alternate Id1" styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.satAltId1}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="Alternate Id2" styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.satAltId2}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="Alternate Id3" styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.satAltId3}
                            </td>
                        </tr>
                        <tr>
                            <td align="left">
                                <h:outputLabel value="Email Address" styleClass="fieldLabel"/>
                            </td>
                            <td align="left">
                                #{rejectedUserSummaryBean.emailAddress}
                            </td>
                        </tr>
                    </table>
                </p:dialog>
            </p:outputPanel>
        </h:form>
</f:view>
