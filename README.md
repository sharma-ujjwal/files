```
<p:column id="selectAllLink"
						  styleClass="headerSortLink actionselectAllLinkCls"
						  sortable="true">
					<f:facet name="header">
						<h:commandLink value="All/None" styleClass="headerSortLink" >
							<f:ajax execute="@this" render="@form bodyForm:actionReqListTable"
									listener="#{userActionRequiredBean.selectAllNone(userActionRequiredBean.myActionRequiredUIList)}" />
						</h:commandLink>
					</f:facet>
					<p:selectBooleanCheckbox id="reviewUserAccessCheckBox"
											 value="#{myActionListTable.selected}">
						<p:ajax event="change" listener="#{userActionRequiredBean.onRowSelect(myActionListTable)}"
								update="@this" process="@this"/>
					</p:selectBooleanCheckbox>
</p:column>
