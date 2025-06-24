```
				<p:column id="selectAllLink"
						  styleClass="headerSortLink actionselectAllLinkCls"
						  sortable="true">
					<f:facet name="header">
						<p:commandLink value="All/None"
									   styleClass="headerSortLink"
									   onclick="selectAllCheckBox(event, 'bodyForm:actionReqListTable');" />
					</f:facet>
					<p:selectBooleanCheckbox id="reviewUserAccessCheckBox"
											 value="#{myActionListTable.selected}">
						<p:ajax event="change" listener="#{userActionRequiredBean.onRowSelect}" update="@this"/>
					</p:selectBooleanCheckbox>
				</p:column>

<script type="text/javascript">
var checkedToggleValue = false;

function selectAllCheckBox(event, parentElementId) {
    event.preventDefault();
    var parentElement = document.getElementById(parentElementId);
    var inputs = parentElement.getElementsByTagName('input');

    for (var i = 0; i &lt; inputs.length; i++) {
        var input = inputs[i];
        if (input.type === 'checkbox') {
            input.checked = !checkedToggleValue; // Toggle the checked state
            // Trigger a change event
            var changeEvent = new Event('change', { bubbles: true });
            input.dispatchEvent(changeEvent);
        }
    }
    checkedToggleValue = !checkedToggleValue;
}

public void onRowSelect() {
		if (Objects.nonNull(selectedUserActionReqUIs)) {
			selectedUserActionReqUIs.clear();
		}

		for (UserActionRequiredUI task : myActionRequiredUIList) {
			if (task.isSelected()) {
				selectedUserActionReqUIs.add(task);
			}
		}
	}

for this code, when I click on select all in UI, all checkboxes are selected except one for all the rows which is random when I click select all multiple times. how to fix this issue?
