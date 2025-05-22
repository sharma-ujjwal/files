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
        
        <h:outputLabel styleClass="sectionHeader" value="Report Details"/>
        
        <p:ajaxStatus>
            <f:facet name="start"><h:graphicImage value="/images/loader.gif"/></f:facet>
        </p:ajaxStatus>
        
        <ui:include src="headerComponent.xhtml">
            <ui:param name="backingBeanName" value="#{accessListBean}"/>
        </ui:include> 
        
        <div class="verticalSpacer"/>
        
        <!-- Main form wrapping all interactive elements -->
        <h:form id="mainForm">
            <!-- Top Toolbar -->
            <p:toolbar id="topToolbar">
                <!-- Keep your existing toolbar code -->
            </p:toolbar>

            <!-- Employee Information -->
            <!-- Keep your existing employee info code -->

            <!-- Access List - FIXED SECTION -->
            <ui:repeat value="#{accessListBean.reviewUserApplicationAccessesDTOs}" var="group" varStatus="loop">
                <p:panel id="appPanel" header="Application: #{group.applicationName} (User ID: #{group.applicationUserId})" 
                         styleClass="myPanelStyle" toggleable="true" collapsed="false">
                    <p:dataTable id="privTable" value="#{group.accesses}" var="priv">
                        <!-- Your existing columns -->
                        <p:column headerText="Review Status" style="text-align:center">
                            <f:facet name="header">
                                <h:panelGrid columns="2" style="margin-bottom:5px;">
                                    <p:commandButton value="All" icon="pi pi-check"
                                                   action="#{accessListBean.setAllPrivilegeValues(group.accesses, 'KEEP')}"
                                                   update="@parent"
                                                   process="@this"/>
                                    <p:commandButton value="None" icon="pi pi-times"
                                                   action="#{accessListBean.setAllPrivilegeValues(group.accesses, 'REMOVE')}"
                                                   update="@parent"
                                                   process="@this"/>
                                </h:panelGrid>
                            </f:facet>
                            <p:selectOneRadio id="reviewRadio" value="#{priv.keepRemoveFlag}" layout="custom">
                                <f:selectItems value="#{accessListBean.keepRemoveOptions}" var="option"
                                             itemLabel="#{option.label}" itemValue="#{option.value}"/>
                                <p:ajax listener="#{accessListBean.statusChanged(priv)}" update="@this"/>
                            </p:selectOneRadio>
                        </p:column>
                    </p:dataTable>
                </p:panel>
            </ui:repeat>

            <!-- Bottom Toolbar -->
            <!-- Keep your existing bottom toolbar code -->
        </h:form>
    </ui:define>
    
    <ui:define name="modalPanels">
        <!-- Keep your existing modal panel code -->
    </ui:define>
</ui:composition>
</html>
