```
<p:dataTable value="#{accessListBean.reviewUserApplicationAccessesDTOs}" var="group" varStatus="gStatus" id="groupTable">
    <p:column>
        <p:panel id="accessPanel_#{gStatus.index}" styleClass="myPanelStyle">
            <f:facet name="header">
                <h:panelGrid columns="2" style="width:100%">
                    <h:outputText value="Application Name: #{group.applicationName}" />
                    <h:outputText value="User ID: #{group.applicationUserId}" style="float: right;" />
                </h:panelGrid>
            </f:facet>

            <p:dataTable id="privilegeTable_#{gStatus.index}" value="#{group.accesses}" var="priv">
                ...
                <p:column style="text-align:center;" headerText="Review Status">
                    <f:facet name="header">
                        <p:panelGrid columns="2" cellpadding="2">
                            <p:commandButton value="All"
                                action="#{accessListBean.setAllPrivilegeValues(group.accesses, 'K')}"
                                update="accessListForm:groupTable:#{gStatus.index}:accessPanel_#{gStatus.index}" process="@this"/>

                            <p:commandButton value="None"
                                action="#{accessListBean.setAllPrivilegeValues(group.accesses, 'R')}"
                                update="accessListForm:groupTable:#{gStatus.index}:accessPanel_#{gStatus.index}" process="@this"/>
                        </p:panelGrid>
                    </f:facet>

                    <h:selectOneRadio id="reviewRadio" value="#{priv.keepRemoveFlag}">
                        <f:selectItems value="#{accessListBean.keepRemoveOptions}" var="option"
                            itemLabel="#{option.label}" itemValue="#{option.value}" />
                        <p:ajax event="change"
                            listener="#{accessListBean.statusChanged(priv)}"
                            update="@this" />
                    </h:selectOneRadio>
                </p:column>
            </p:dataTable>
        </p:panel>
    </p:column>
</p:dataTable>

function setRadioButtonValue(radioValue) {
				let inputs = document.getElementsByTagName('input');
				for (let i = 0; i &lt; inputs.length; i++) {
					let input = inputs[i];
					// is it of type radio?
					if (input.type === 'radio') {
						// is it the right radio for the given value to set?
						if (input.value === radioValue) {
							// check it
							input.checked = true;
						}
					}
				}
			}
