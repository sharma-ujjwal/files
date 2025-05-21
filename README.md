```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:ui="http://java.sun.com/jsf/facelets"
      xmlns:h="http://xmlns.jcp.org/jsf/html"
      xmlns:f="http://java.sun.com/jsf/core"
      xmlns:p="http://primefaces.org/ui">

<ui:composition template="../common/main.xhtml">
    <ui:define name="body">
        <!-- Add messages component for debugging -->
        <p:messages id="messages" autoUpdate="true" showDetail="true"/>
        
        <script type="text/javascript">
            function setRadios(parentElementId, radioValue) {
                let parentElement = document.getElementById(parentElementId);
                let inputs = parentElement.getElementsByTagName('input');
                for (let i = 0; i < inputs.length; i++) {
                    let input = inputs[i];
                    if (input.type === 'radio') {
                        if (input.value === radioValue) {
                            input.checked = true;
                        }
                    }
                }
            }

            function setRadioButtonValue(radioValue) {
                let inputs = document.getElementsByTagName('input');
                for (let i = 0; i < inputs.length; i++) {
                    let input = inputs[i];
                    if (input.type === 'radio') {
                        if (input.value === radioValue) {
                            input.checked = true;
                        }
                    }
                }
            }
            
            // Debug function to check if buttons are working
            function logAction(action) {
                console.log("Action triggered: " + action);
            }
        </script>

        <h:outputLabel styleClass="sectionHeader" value="Report Details"/>
        
        <p:ajaxStatus onstart="logAction('ajax-start')" onsuccess="logAction('ajax-success')" onerror="logAction('ajax-error')">
            <f:facet name="start"><h:graphicImage value="/images/loader.gif"/></f:facet>
        </p:ajaxStatus>
        
        <ui:include src="headerComponent.xhtml">
            <ui:param name="backingBeanName" value="#{accessListBean}"/>
        </ui:include> 
        
        <div class="verticalSpacer"/>
        
        <!-- Main form wrapping all interactive elements -->
        <h:form id="mainForm" prependId="false">
            <!-- Top Toolbar -->
            <p:toolbar id="topToolbar">
                <p:toolbarGroup location="left">
                    <p:commandButton id="returnToListBtn" value="Return to List"
                                   action="#{accessListBean.doReturnToEmployeeList}"
                                   update="@form"
                                   process="@this"
                                   onstart="logAction('return-to-list')"
                                   icon="pi pi-arrow-left"/>
                    
                    <p:commandButton id="rejectEmployeeBtn" value="Reject Employee"
                                   action="#{accessListBean.doPrepareRejectUserPanel}"
                                   styleClass="undecoratedRejectLink"
                                   update=":accessListModalAjaxPanel"
                                   process="@this"
                                   oncomplete="PF('rejectReviewUserModalPanel').show()"
                                   rendered="#{accessListBean.editMode}"
                                   icon="pi pi-times"/>
                </p:toolbarGroup>
                
                <p:toolbarGroup>
                    <p:commandButton id="printBtn" value="Printable Version"
                                   action="#{accessListBean.doPrintAccessList}"
                                   styleClass="print"
                                   target="_blank"
                                   process="@form"
                                   icon="pi pi-print"/>
                </p:toolbarGroup>
                
                <p:toolbarGroup location="right">
                    <p:commandButton id="prevEmployeeBtn" value="Previous Employee"
                                   action="#{accessListBean.doRetrievePreviousEmployee}"
                                   update="@form"
                                   process="@this"
                                   onstart="logAction('previous-employee')"
                                   icon="pi pi-chevron-left"/>
                    
                    <p:commandButton id="nextEmployeeBtn" value="Next Employee"
                                   action="#{accessListBean.doRetrieveNextEmployee}"
                                   update="@form"
                                   process="@this"
                                   onstart="logAction('next-employee')"
                                   icon="pi pi-chevron-right"/>
                </p:toolbarGroup>
            </p:toolbar>

            <!-- Employee Information -->
            <p:panel header="Employee Details" style="margin-bottom:20px;">
                <p:outputLabel value="#{accessListBean.numberOfRecords} - #{accessListBean.userName}" style="font-weight:bold; margin-right:20px;"/>
                <p:outputLabel value="Status: #{accessListBean.userStatus}" style="margin-right:20px;"/>
                <p:outputLabel value="Manager: #{accessListBean.employeeManager}" 
                             rendered="#{accessListBean.review.notManagerReview}" style="margin-right:20px;"/>
                <p:outputLabel value="Division: #{accessListBean.userDivision}" style="margin-right:20px;"/>
                <p:outputLabel value="Department: #{accessListBean.userDepartment}"/>
            </p:panel>

            <!-- Access List -->
            <ui:repeat value="#{accessListBean.reviewUserApplicationAccessesDTOs}" var="group" varStatus="loop">
                <p:panel id="appPanel_#{loop.index}" header="Application: #{group.applicationName} (User ID: #{group.applicationUserId})" 
                         styleClass="myPanelStyle" toggleable="true" collapsed="false">
                    <p:dataTable id="privTable_#{loop.index}" value="#{group.accesses}" var="priv">
                        <p:column headerText="Source Name">
                            <h:outputText value="#{priv.sourceName}"/>
                        </p:column>
                        <p:column headerText="Description">
                            <h:outputText value="#{priv.privilegeDescription}"/>
                        </p:column>
                        <p:column headerText="Value">
                            <h:outputText value="#{priv.privilegeValue}"/>
                        </p:column>
                        <p:column headerText="Comment">
                            <h:outputText value="#{priv.privilegeComment}"/>
                        </p:column>
                        <p:column headerText="Review Status" style="text-align:center">
                            <f:facet name="header">
                                <h:panelGrid columns="2" style="margin-bottom:5px;">
                                    <p:commandButton value="All" icon="pi pi-check"
                                                   action="#{accessListBean.setAllPrivilegeValues(group.accesses, 'KEEP')}"
                                                   update=":mainForm:privTable_#{loop.index}"
                                                   process="@this"/>
                                    <p:commandButton value="None" icon="pi pi-times"
                                                   action="#{accessListBean.setAllPrivilegeValues(group.accesses, 'REMOVE')}"
                                                   update=":mainForm:privTable_#{loop.index}"
                                                   process="@this"/>
                                </h:panelGrid>
                            </f:facet>
                            <p:selectOneRadio id="reviewRadio_#{loop.index}" value="#{priv.keepRemoveFlag}" layout="custom">
                                <f:selectItems value="#{accessListBean.keepRemoveOptions}" var="option"
                                             itemLabel="#{option.label}" itemValue="#{option.value}"/>
                                <p:ajax listener="#{accessListBean.statusChanged(priv)}" update="@this"/>
                            </p:selectOneRadio>
                        </p:column>
                    </p:dataTable>
                </p:panel>
            </ui:repeat>

            <!-- Bottom Toolbar (duplicate of top for convenience) -->
            <p:toolbar id="bottomToolbar" style="margin-top:20px;">
                <p:toolbarGroup location="left">
                    <p:commandButton id="bottomReturnBtn" value="Return to List"
                                    action="#{accessListBean.doReturnToEmployeeList}"
                                    update="@form"
                                    process="@this"
                                    icon="pi pi-arrow-left"/>
                    
                    <p:commandButton id="bottomRejectBtn" value="Reject Employee"
                                    action="#{accessListBean.doPrepareRejectUserPanel}"
                                    styleClass="undecoratedRejectLink"
                                    update=":accessListModalAjaxPanel"
                                    process="@this"
                                    oncomplete="PF('rejectReviewUserModalPanel').show(); window.scrollTo(0,0);"
                                    rendered="#{accessListBean.editMode}"
                                    icon="pi pi-times"/>
                </p:toolbarGroup>
                
                <p:toolbarGroup>
                    <p:commandButton id="bottomPrintBtn" value="Printable Version"
                                    action="#{accessListBean.doPrintAccessList}"
                                    styleClass="print"
                                    target="_blank"
                                    process="@form"
                                    icon="pi pi-print"/>
                </p:toolbarGroup>
                
                <p:toolbarGroup location="right">
                    <p:commandButton id="bottomPrevBtn" value="Previous Employee"
                                    action="#{accessListBean.doRetrievePreviousEmployee}"
                                    update="@form"
                                    process="@this"
                                    icon="pi pi-chevron-left"/>
                    
                    <p:commandButton id="bottomNextBtn" value="Next Employee"
                                    action="#{accessListBean.doRetrieveNextEmployee}"
                                    update="@form"
                                    process="@this"
                                    icon="pi pi-chevron-right"/>
                </p:toolbarGroup>
            </p:toolbar>
        </h:form>
    </ui:define>
    
    <ui:define name="modalPanels">
        <p:outputPanel autoUpdate="true" id="accessListModalAjaxPanel">
            <ui:include src="rejectReviewUsersModalPanelComponent.xhtml">
                <ui:param name="aListBean" value="#{accessListBean}" />
                <ui:param name="formId" value="rejectReviewUserCommentForm" />
                <ui:param name="panelId" value="rejectReviewUserModalPanel" />
                <ui:param name="msgId" value="rejectReviewUserModalPanelMessages" />
                <ui:param name="dataTableId" value="rejectReviewUserModalPanelTable" />
                <ui:param name="selectId" value="rejectUserModalPanelReason" />
                <ui:param name="inputId" value="rejectUserModalComments" />
                <ui:param name="submitId" value="rejectUserModalPanelSubmitButton" />
                <ui:param name="cancelId" value="rejectUserModalPanelCancelButton" />
            </ui:include>
        </p:outputPanel>
    </ui:define>
</ui:composition>
</html>
