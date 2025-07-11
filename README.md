```
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
		this.clearTabData();
		this.activeIndex = 2;
		this.selectedTab = 2;
		TableMaintenanceBean bean = (TableMaintenanceBean) JSFUtils.lookupBean("tableMaintenanceBean");
		return bean.switchMaintenanceTable();
	}

	public String switchToExceptionReporting() {
		clearTabData();
		this.activeIndex = 1;
		this.selectedTab = 1;
		return "switchToExceptionReporting";
	}
	
	public String switchToRejectedUsers() {
		clearTabData();
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
				System.out.println("Switching to Exception Reporting Tab");
				System.out.println("Active Index>>>>>: " + activeIndex);
				System.out.println("Selected Tab:>>>> " + selectedTab);
				switchToTableMaintenance();
			}
	}
	
	private void clearTabData() {
		TableMaintenanceBean tableMaintenanceBean = (TableMaintenanceBean) JSFUtils.lookupBean("tableMaintenanceBean");
		tableMaintenanceBean.init();
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
