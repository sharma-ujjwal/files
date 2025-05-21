```
<!-- All / None buttons moved above the table -->
<h:panelGroup layout="block" style="margin: 10px 0;">
    <h:outputText value="Set All Privileges for: #{group.applicationName}" styleClass="sectionHeader"/>
    <p:commandButton value="All"
                     action="#{accessListBean.setAllPrivilegeValues(group.accesses, 'K')}"
                     update="@form"
                     process="@this"
                     style="margin-left:10px;" />
    <p:commandButton value="None"
                     action="#{accessListBean.setAllPrivilegeValues(group.accesses, 'R')}"
                     update="@form"
                     process="@this"
                     style="margin-left:5px;" />
</h:panelGroup>

<!-- DataTable -->
<p:dataTable id="privilegeTable" value="#{group.accesses}" var="priv" style="margin-top:10px;">
    <p:column headerText="Source Name">
        <h:outputText value="#{priv.sourceName}"/>
    </p:column>
    <p:column headerText="Evidence Description">
        <h:outputText value="#{priv.privilegeDescription}"/>
    </p:column>
    <p:column headerText="Privilege Value">
        <h:outputText value="#{priv.privilegeValue}"/>
    </p:column>
    <p:column headerText="Privilege Comment">
        <h:outputText value="#{priv.privilegeComment}"/>
    </p:column>
    <p:column headerText="Review Status" style="text-align:center;">
        <h:selectOneRadio value="#{priv.keepRemoveFlag}">
            <f:selectItems value="#{accessListBean.keepRemoveOptions}" var="option"
                           itemLabel="#{option.label}" itemValue="#{option.value}" />
            <p:ajax event="change"
                    listener="#{accessListBean.statusChanged(priv)}"
                    update="@this" />
        </h:selectOneRadio>
    </p:column>
</p:dataTable>
