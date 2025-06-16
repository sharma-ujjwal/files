```
<p:toolbar id="bottomApprovedReviewersTableToolBar" styleClass="custom-toolbar">
    <p:toolbarGroup styleClass="accessListRejectLinkToolBarLeft" align="left">
        <h:commandLink styleClass="listitem" id="bottomApprovedReviewersDistributeLink"
                       value="Return to List"
                       action="#{accessListBean.doReturnToEmployeeList}"/>
        <h:outputLabel styleClass="headerTextValue" value="  "/>
        <p:commandLink id="bottomAccessListRejectLink"
                       value="Reject Employee"
                       action="#{accessListBean.doPrepareRejectUserPanel}"
                       update="bodyForm:headerMessages, @form, accessListModalAjaxPanel, panelId"
                       styleClass="undecoratedRejectLink"
                       rendered="#{accessListBean.editMode}"
                       oncomplete="window.scrollTo(0, 0);"/>
    </p:toolbarGroup>

    <p:toolbarGroup styleClass="middle-group" align="left">
        <h:commandLink id="bottomPrintAccessCommandLink"
                       action="#{accessListBean.doPrintAccessList}"
                       value="Printable Version"
                       styleClass="print"
                       onmousedown="document.forms['bodyForm'].target='_blank'"/>
    </p:toolbarGroup>

    <p:toolbarGroup styleClass="prevNextEmployeeLinkAlignRight" align="right">
        <h:commandLink styleClass="previousitem" id="bottomPreviousEmployeeLink"
                       action="#{accessListBean.doRetrievePreviousEmployee}"
                       value="Previous Employee"/>
        <h:outputLabel styleClass="headerTextValue" value="  "/>
        <h:commandLink styleClass="nextitem" id="bottomNextEmployeeLink"
                       action="#{accessListBean.doRetrieveNextEmployee}"
                       value="Next Employee"/>
    </p:toolbarGroup>
</p:toolbar>
