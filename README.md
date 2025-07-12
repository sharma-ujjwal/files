```
TableMaintainanceBean.java
package com.assurant.inc.sox.ar.client.bean.admin;

import java.util.ArrayList;
import java.util.List;


import javax.faces.model.SelectItem;

import com.assurant.inc.sox.ar.client.bean.SessionDataBean;
import com.assurant.inc.sox.ar.client.bean.util.JSFUtils;
import com.assurant.inc.sox.ar.dto.enums.TableMaintenanceCode;
import com.assurant.inc.sox.ar.utils.NavigationUtility;
import org.primefaces.PrimeFaces;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

@Component("tableMaintenanceBean")
@Scope("session")
public class TableMaintenanceBean {
	private String selectedTable;
	private boolean isTabSwitched = false;

    public boolean isTabSwitched() {
		return isTabSwitched;
	}

	public void setTabSwitched(boolean isTabSwitched) {
		this.isTabSwitched = isTabSwitched;
	}

	public String getSelectedTable() {
			return selectedTable;
	}

	public void updateTablePagePath() {
		TableMaintenanceCode code = TableMaintenanceCode.valueOf(selectedTable);
        String tablePagePath = "/xhtml/admin/departmentSummary.xhtml";

		if (!isTabSwitched && code.name().equals(TableMaintenanceCode.DEPARTMENTS.name())) {
			System.out.println("===================================================INSIDE DEPARTMENT TABLE MAINTENANCE BEAN===================================================");
			// If the selected table is DEPARTMENTS, redirect to the department summary page
			var navigationHandler = NavigationUtility.getNavigationHandlerInstance();
			navigationHandler.handleNavigation(NavigationUtility.getFacesContextInstance(), null, tablePagePath);
			isTabSwitched = true; // Reset the tab switch flag
		}
	}

	public void onTableDropdownChange() {
		// Update the table page path based on the selected table
		updateTablePagePath();
	}

	public void resetToDefaultTable() {
		this.selectedTable = TableMaintenanceCode.DEPARTMENTS.name();
		updateTablePagePath();
	}

	public void setSelectedTable(String selectedTable) {
		this.selectedTable = selectedTable;
	}

	public List<SelectItem> getAvailableMaintenanceTables() {
		List<SelectItem> availableMaintenanceTables = new ArrayList<>();
		for (TableMaintenanceCode code : TableMaintenanceCode.values()){
			availableMaintenanceTables.add(new SelectItem(code.name(), code.departmentName()));
		}
		return availableMaintenanceTables;
	}

	private void resetTableToDepartmentTable() {
		this.selectedTable = TableMaintenanceCode.DEPARTMENTS.name();
	}
	
	public String switchMaintenanceTable() {
		// first clear all session data from all other reference table
		// session beans
		initMaintenanceTableSessionBeans();
		
		// always force IPS users to the user summary screen; others go to the 
		// department summary screen
		SessionDataBean sessionDataBean = (SessionDataBean) JSFUtils.lookupBean("sessionDataBean");
		if (sessionDataBean.getSystemUser().isIpsUser()) {
			this.selectedTable = TableMaintenanceCode.USERTABLE.name();
		}
		
		TableMaintenanceCode selectedCode = TableMaintenanceCode.valueOf(this.selectedTable);
        String value = switch (selectedCode) {
            case APPLICATION -> "switchToApplicationTable";
            case CONFLICTS -> "switchToConflictsTable";
            case CONFLICTTYPES -> "switchToConflictTypesTable";
            case DATA_OWNER -> "switchToDataOwnerTable";
            case DIVISIONS -> "switchToDivisionsTable";
            case ENVIRONMENT -> "switchToEnvironmentsTable";
            case FUNCTIONDUTY -> "switchToFunctionDutyTable";
            case REVIEW_OWNER -> "switchToReviewOwnerTable";
            case SOD_OWNER -> "switchToSodOwnerTable";
            case SOXCONCERN -> "switchToSoxConcernTable";
            case USERSTATUS -> "switchToUserStatusTable";
            case USERTYPE -> "switchToUserTypeTable";
            case USERTABLE -> "switchToUserTable";
            case PRIVILEGECOMMENTS -> "switchToPrivilegeComments";
            default -> "switchToDepartmentsTable";
        };

		if (selectedCode.name().equals(TableMaintenanceCode.DEPARTMENTS.name())) {
			updateTablePagePath();
		}

		System.out.println("Switching to =============> " + value + " table");
		return value;
	}

	public void init(boolean clearAll) {
		resetTableToDepartmentTable();
		this.initMaintenanceTableSessionBeans();
		if (!clearAll) {
			switchMaintenanceTable();
		}
	}
	
	/*
	 * Initializes all session managed beans so that data is removed from the
	 * session. 
	 */
	private void initMaintenanceTableSessionBeans() {
		((ApplicationSummaryBean) JSFUtils.lookupBean("applicationSummaryBean")).init();
		((ConflictSummaryBean) JSFUtils.lookupBean("conflictSummaryBean")).init();
		((ConflictTypeSummaryBean) JSFUtils.lookupBean("conflictTypeSummaryBean")).init();
		((DataOwnerSummaryBean) JSFUtils.lookupBean("dataOwnerSummaryBean")).init();
		((DepartmentSummaryBean) JSFUtils.lookupBean("departmentSummaryBean")).init();
		((DivisionSummaryBean) JSFUtils.lookupBean("divisionSummaryBean")).init();
		((EnvironmentSummaryBean) JSFUtils.lookupBean("environmentSummaryBean")).init();
		((FunctionDutySummaryBean) JSFUtils.lookupBean("functionDutySummaryBean")).init();
		((PrivilegeCommentSummaryBean) JSFUtils.lookupBean("privilegeCommentSummaryBean")).init();
		((ReviewOwnerSummaryBean) JSFUtils.lookupBean("reviewOwnerSummaryBean")).init();
		((SodOwnerSummaryBean) JSFUtils.lookupBean("sodOwnerSummaryBean")).init();
		((SoxConcernSummaryBean) JSFUtils.lookupBean("soxConcernSummaryBean")).init();
		((UserSummaryBean) JSFUtils.lookupBean("userSummaryBean")).init();
		((UserStatusSummaryBean) JSFUtils.lookupBean("userStatusSummaryBean")).init();
		((UserTypeSummaryBean) JSFUtils.lookupBean("userTypeSummaryBean")).init();
	}
}


AdminConsoleBean.java

package com.assurant.inc.sox.ar.client.bean.admin;

import com.assurant.inc.sox.ar.client.bean.util.JSFUtils;
import com.assurant.inc.sox.ar.dto.SystemUserDTO;
import org.primefaces.event.TabChangeEvent;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;


/**
 * 
 * @author Brian Olson
 *
 */
@Component("adminConsoleBean")
@Scope("session")
public class AdminConsoleBean {

	@Autowired
	@Qualifier("sessionSystemUser")
	private SystemUserDTO systemUser;
	
	private static final String REJECTS_TAB_ID = "rejectsTab";
	private static final String EXCEPTIONS_TAB_ID = "exceptionReportingTab";
	private static final String TABLE_MAINTENANCE_TAB_ID = "tableMaintenanceTab";
	
	private int selectedTab;
	private int activeIndex;

	public void setSelectedTab(int selectedTabIndex) {
		if (selectedTabIndex == 0) {
			this.selectedTab = 0;
		} else if (selectedTabIndex == 1) {
			this.selectedTab = 1;
		} else if (selectedTabIndex == 2) {
			this.selectedTab = 2;
		}
	}
	
	public void init() {
		this.switchToTableMaintenance();
	}
	
	public String switchToTableMaintenance() {
		this.clearTabData(true);
		this.activeIndex = 2;
		this.selectedTab = 2;
		TableMaintenanceBean bean = (TableMaintenanceBean) JSFUtils.lookupBean("tableMaintenanceBean");
		bean.setTabSwitched(false);
		return bean.switchMaintenanceTable();
	}

	public String switchToExceptionReporting() {
		clearTabData(false);
		this.activeIndex = 1;
		this.selectedTab = 1;
		return "switchToExceptionReporting";
	}
	
	public String switchToRejectedUsers() {
		clearTabData(false);
		this.activeIndex = 0;
		this.selectedTab = 0;
		return "switchToRejectedUsers";
	}

	public void onTabChange(TabChangeEvent event) {
			String tabId = event.getTab().getId();
			if (REJECTS_TAB_ID.equals(tabId)) {
				this.activeIndex = 0;
				this.selectedTab = activeIndex;
				switchToRejectedUsers();
			} else if (EXCEPTIONS_TAB_ID.equals(tabId)) {
				this.activeIndex = 1;
				this.selectedTab = activeIndex;
				switchToExceptionReporting();
			} else if (TABLE_MAINTENANCE_TAB_ID.equals(tabId)) {
				this.activeIndex = 2;
				this.selectedTab = activeIndex;
				switchToTableMaintenance();
			}
	}
	
	private void clearTabData(boolean clearAll) {
		TableMaintenanceBean tableMaintenanceBean = (TableMaintenanceBean) JSFUtils.lookupBean("tableMaintenanceBean");
		if (clearAll) { tableMaintenanceBean.setTabSwitched(false); }
		tableMaintenanceBean.init(clearAll);
		ExceptionSummaryBean exceptionSummaryBean = (ExceptionSummaryBean) JSFUtils.lookupBean("exceptionSummaryBean");
		exceptionSummaryBean.init();
		RejectedUserSummaryBean rejectedUserSummaryBean = (RejectedUserSummaryBean) JSFUtils.lookupBean("rejectedUserSummaryBean");
		rejectedUserSummaryBean.init();
	}

	public SystemUserDTO getSystemUser() {
		return systemUser;
	}

	public void setSystemUser(SystemUserDTO systemUser) {
		this.systemUser = systemUser;
	}

	public int getActiveIndex() {
		return activeIndex;
	}

	public void setActiveIndex(int activeIndex) {
		this.activeIndex = activeIndex;
	}

	public int getSelectedTab() {
		return selectedTab;
	}
}





referenceCoreTableTemplate.xhtml
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
	xmlns:h="http://xmlns.jcp.org/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	xmlns:p="http://primefaces.org/ui" >

	<ui:composition template="../common/adminConsoleTemplate.xhtml">
		<ui:define name="tableMaintenanceTab">
			<!--  Select a Table -->
			<h:panelGrid id="selectTablePanelGrid" rendered="#{!sessionDataBean.systemUser.ipsUser}" columns="3">
				<h:outputLabel id="selectTableLabel" styleClass="selectTableLabelCls" value="Select a table: " />

				<h:selectOneMenu id="tableMaintenanceListbox"
								 onchange="document.getElementById('bodyForm:tabPanel:tableMaintenanceSubmit').click();"
								 value="#{tableMaintenanceBean.selectedTable}">
					<f:selectItems value="#{tableMaintenanceBean.availableMaintenanceTables}" />
				</h:selectOneMenu>
				<h:commandButton id="tableMaintenanceSubmit" style="display: none;"
					action="#{tableMaintenanceBean.switchMaintenanceTable}" />
			</h:panelGrid>

			<div class="verticalSpacer"/>

			<ui:insert name="referenceCoreTable" />
		</ui:define>
	</ui:composition>
</html>


adminConsoletTemplate
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
	  xmlns:ui="http://java.sun.com/jsf/facelets"
	  xmlns:h="http://java.sun.com/jsf/html"
	  xmlns:f="http://java.sun.com/jsf/core"
	  xmlns:p="http://primefaces.org/ui">

<ui:composition template="../common/main.xhtml">
	<ui:define name="body">
		<p:ajaxStatus id="tableMaintenanceAjaxLoadStatus"
					  onstart="showModalInfoWindow();"
					  oncomplete="hideModalInfoWindow();" />

		<p:tabView id="tabPanel"
				   activeIndex="#{adminConsoleBean.selectedTab}"
				   dynamic="true"
				   cache="false"
				   styleClass="tabStyle userTableTabAdminConsole" style="min-height: 25vh;">
			<p:ajax event="tabChange"
					update="bodyForm:headerMessages"
					listener="#{adminConsoleBean.onTabChange}" />
			<p:tab id="rejectsTab" title="Rejects" rendered="#{!sessionDataBean.systemUser.ipsUser}">
			  <ui:include src="/xhtml/admin/rejectedUserSummary.xhtml" />
			</p:tab>
			<p:tab id="exceptionReportingTab" title="Exception Reporting" rendered="#{!sessionDataBean.systemUser.ipsUser}">
			  <ui:include src="/xhtml/admin/exceptionSummary.xhtml" />
			</p:tab>
			<p:tab id="tableMaintenanceTab" title="Table Maintenance">
			  <ui:insert name="tableMaintenanceTab" />
			</p:tab>
		</p:tabView>
	</ui:define>
</ui:composition>
</html>
