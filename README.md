```
	/**
	 * Build the remove button, attach a listener to it.
	 *
	 * @param accessesDTO the DTO the listener attaches to
	 * @param id to generate a unique id for the component
	 * @return the HtmlAjaxCommandButton built
	 */
	private HtmlCommandButton buildRemoveButton(ReviewUserApplicationAccessesDTO accessesDTO, int id) {
		HtmlCommandButton removeButton = new HtmlCommandButton();
		removeButton.setId("Application" + id + "removeButton");
		removeButton.setValue("None");
		removeButton.setOnclick("setRadios('bodyForm:Application" + id + "Table', 'R'); callSelectOrRejectAllEmployees(false); return false;");
		//removeButton.addActionListener(new RemoveButtonListener(accessesDTO));
		return removeButton;
	}

	/**
	 * Build the keep button, attach a listener to it.
	 *
	 * @param accessesDTO the DTO the listener attaches to
	 * @param id to generate a unique id for the component
	 * @return the HtmlAjaxCommandButton built
	 */
	private HtmlCommandButton buildKeepButton(ReviewUserApplicationAccessesDTO accessesDTO, int id) {
		HtmlCommandButton keepButton = new HtmlCommandButton();
		keepButton.setId("Application" + id + "keepButton");
		keepButton.setValue("All");
		keepButton.setOnclick("setRadios('bodyForm:Application" + id + "Table', 'K'); callSelectOrRejectAllEmployees(true); return false;");
		//keepButton.addActionListener(new KeepButtonListener(accessesDTO));
		return keepButton;
	}

keepButton.setOnclick("setRadios('bodyForm:Application" + id + "Table', 'K') callSelectOrRejectAllEmployees(true); return false;"); 
removeButton.setOnclick("setRadios('bodyForm:Application" + id + "Table', 'R'); callSelectOrRejectAllEmployees(false); return false;");
from this onClick method invoke a method employeeListBean.selectOrRejectAllEmployees(isSelected); add this code when on click is activated similar to above one. the method is 
callSelectOrRejectAllEmployees(boolean isSelected) is present in EmployeeListBean.java
