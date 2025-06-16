```
<p:toolbar id="approvedReviewersTableToolBar" styleClass="custom-toolbar">
    <p:toolbarGroup styleClass="accessListRejectLinkToolBarLeft" align="left">
        <h:commandLink styleClass="listitem" id="approvedReviewersDistributeLink"
                       value="Return to List"
                       action="#{accessListBean.doReturnToEmployeeList}"/>
        <h:outputLabel styleClass="headerTextValue" value="  "/>
        <p:commandLink id="accessListRejectLink"
                       value="Reject Employee"
                       action="#{accessListBean.doPrepareRejectUserPanel}"
                       update="bodyForm:headerMessages, @form, accessListModalAjaxPanel, panelId"
                       styleClass="undecoratedRejectLink"
                       rendered="#{accessListBean.editMode}"
                       oncomplete="window.scrollTo(0, 0);" />
    </p:toolbarGroup>

    <p:toolbarGroup styleClass="middle-group" align="left">
        <h:commandLink id="printAccessCommandLink"
                       action="#{accessListBean.doPrintAccessList}"
                       value="Printable Version"
                       styleClass="print"
                       onmousedown="document.forms['bodyForm'].target='_blank'" />
    </p:toolbarGroup>

    <p:toolbarGroup styleClass="prevNextEmployeeLinkAlignRight" align="right">
        <h:commandLink styleClass="previousitem" id="previousEmployeeLink"
                       action="#{accessListBean.doRetrievePreviousEmployee}"
                       value="Previous Employee"/>
        <h:outputLabel styleClass="headerTextValue" value="  "/>
        <h:commandLink styleClass="nextitem" id="nextEmployeeLink"
                       action="#{accessListBean.doRetrieveNextEmployee}"
                       value="Next Employee"/>
    </p:toolbarGroup>
</p:toolbar>

/* Make the toolbar behave as a responsive flex container */
.custom-toolbar .ui-toolbar-group {
    display: flex;
    align-items: center;
}

/* Space out the groups properly */
.custom-toolbar .accessListRejectLinkToolBarLeft {
    margin-right: auto;
}

.custom-toolbar .middle-group {
    margin: 0 auto;
}

.custom-toolbar .prevNextEmployeeLinkAlignRight {
    margin-left: auto;
}

/* Optional responsive tweaks */
@media (max-width: 768px) {
    .custom-toolbar .ui-toolbar-group {
        flex-direction: column;
        align-items: flex-start;
        width: 100%;
    }

    .custom-toolbar .middle-group,
    .custom-toolbar .accessListRejectLinkToolBarLeft,
    .custom-toolbar .prevNextEmployeeLinkAlignRight {
        margin: 5px 0;
    }
}
