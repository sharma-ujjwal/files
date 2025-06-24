```
<p:column id="selectAllLink"
						  styleClass="headerSortLink actionselectAllLinkCls"
						  sortable="true">
					<f:facet name="header">
						<h:commandLink value="All/None" styleClass="headerSortLink" >
							<p:ajax execute="@this" render="@form actionReqListTable"
									listener="#{userActionRequiredBean.selectAllNone(userActionRequiredBean.myActionRequiredUIList)}" />
						</h:commandLink>
					</f:facet>
					<h:selectBooleanCheckbox id="reviewUserAccessCheckBox"
											 value="#{myActionListTable.selected}">
						<p:ajax event="change" listener="#{userActionRequiredBean.onRowSelect(myActionListTable)}"
								update="@this" process="@this"/>
					</h:selectBooleanCheckbox>
				</p:column>
