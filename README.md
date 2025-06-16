```
<p:toolbar id="approvedReviewersTableToolBar" styleClass="custom-toolbar">
    <p:toolbarGroup location="left">
        <h:commandLink styleClass="listitem" id="approvedReviewersDistributeLink" value="Return to List"
            action="#{accessListBean.doReturnToEmployeeList}"/>
        <h:outputLabel styleClass="headerTextValue" value="  "/>
        <h:commandLink id="accessListRejectLink"
            value="Reject Employee" 
            action="#{accessListBean.doPrepareRejectUserPanel}"
            styleClass="undecoratedRejectLink"
            rendered="#{accessListBean.editMode}" />
    </p:toolbarGroup>

    <p:toolbarGroup styleClass="middle-group">
        <h:commandLink id="printAccessCommandLink" 
            action="#{accessListBean.doPrintAccessList}" 
            value="Printable Version" 
            styleClass="print"
            onmousedown="document.forms['bodyForm'].target='_blank'" />
    </p:toolbarGroup>

    <p:toolbarGroup location="right">
        <h:commandLink styleClass="previousitem" id="previousEmployeeLink"
            action="#{accessListBean.doRetrievePreviousEmployee}"
            value="Previous Employee" />
        <h:outputLabel styleClass="headerTextValue" value="  "/>
        <h:commandLink styleClass="nextitem" id="nextEmployeeLink"
            action="#{accessListBean.doRetrieveNextEmployee}"
            value="Next Employee" />
    </p:toolbarGroup>
</p:toolbar>


.custom-toolbar .ui-toolbar-group {
    display: flex;
    align-items: center;
}

/* Force the middle group to push content to the right */
.custom-toolbar .middle-group {
    margin-left: auto;
    margin-right: auto;
}

/* Optional: On small screens, stack toolbar groups */
@media (max-width: 768px) {
    .custom-toolbar .ui-toolbar-group {
        flex-direction: column;
        align-items: flex-start;
    }
}
