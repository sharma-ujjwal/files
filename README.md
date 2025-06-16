```
<p:toolbar id="approvedReviewersTableToolBar">
			<p:toolbarGroup styleClass="accessListRejectLinkToolBarLeft" align="left">
				<h:commandLink styleClass="listitem" id="approvedReviewersDistributeLink" value="Return to List"
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
			<p:toolbarGroup styleClass="printAccessCommandLinkAlignLeft" align="left">
				<h:commandLink id="printAccessCommandLink"
							   action="#{accessListBean.doPrintAccessList}"
							   value="Printable Version"
							   styleClass="print"
							   style="margin-right: #{accessListBean.editMode ? '1170px' : '1300px'} !important;"
							   onmousedown="document.forms['bodyForm'].target='_blank'"/>
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
