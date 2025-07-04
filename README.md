```
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
                                             process="@form"
                                             ajax="true"
                                             update="addAlternateIdForm bodyForm:headerMessages bodyForm:tabPanel:rejectedUserTable"
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
