```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:h="http://xmlns.jcp.org/jsf/html"
      xmlns:p="http://primefaces.org/ui"
      xmlns:f="http://xmlns.jcp.org/jsf/core">
<h:head>
    <title>Access List</title>
</h:head>
<h:body>
    <h:form id="accessListForm">
        <ui:repeat value="#{accessListBean.accessGroups}" var="group">
            <p:panel header="Application Name: #{group.applicationName}" toggleable="true" collapsed="true">
                <h:panelGrid columns="2" cellpadding="5">
                    <h:outputText value="Business Area:" />
                    <h:outputText value="#{group.businessArea}" />
                    
                    <h:outputText value="Evidence Description:" />
                    <h:outputText value="#{group.evidenceDescription}" />
                    
                    <!-- Add more summary details if required -->
                </h:panelGrid>
                
                <p:dataTable value="#{group.privileges}" var="priv">
                    <p:column headerText="Source Name">
                        <h:outputText value="#{priv.sourceName}" />
                    </p:column>
                    <p:column headerText="Evidence Description">
                        <h:outputText value="#{priv.evidenceDescription}" />
                    </p:column>
                    <p:column headerText="Privilege Value">
                        <h:outputText value="#{priv.privilegeValue}" />
                    </p:column>
                    <p:column headerText="Privilege Comment">
                        <h:inputText value="#{priv.privilegeComment}" />
                    </p:column>
                    <p:column headerText="Review Status">
                        <p:selectOneRadio value="#{priv.reviewStatus}">
                            <f:selectItem itemLabel="Keep" itemValue="Keep" />
                            <f:selectItem itemLabel="Remove" itemValue="Remove" />
                        </p:selectOneRadio>
                    </p:column>
                </p:dataTable>
            </p:panel>
        </ui:repeat>
    </h:form>
</h:body>
</html>
