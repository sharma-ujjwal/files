```
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

                <h:form id="addSeachedAssociatedUserForm">
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
                                <h:commandButton id="deleteButton"
                                                 value="Continue"
                                                 action="#{rejectedUserSummaryBean.doSearchAssociatedUser}"/>
                            </td>
                            <td>
                                <h:commandButton id="cancelDeleteButton"
                                                 value="Cancel"
                                                 immediate="true"
                                                 action="#{rejectedUserSummaryBean.doCancelSearchAssociatedUser}"/>
                            </td>
                        </tr>
                        </tbody>
                    </table>
                </h:form>
            </p:dialog>
        </p:outputPanel>
