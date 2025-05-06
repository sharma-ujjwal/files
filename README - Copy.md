<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://java.sun.com/jsf/facelets"
	xmlns:h="http://java.sun.com/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	xmlns:rich="http://richfaces.org/rich"
	xmlns:a4j="http://richfaces.org/a4j">

	<ui:composition template="../common/main.xhtml">
		<ui:define name="body">
			<a4j:status id="tableMaintenanceAjaxLoadStatus"
				onstart="showModalInfoWindow();"
				onstop="hideModalInfoWindow();"></a4j:status>
			<rich:tabPanel id="tabPanel" switchType="server" tabClass="tabStyle"
				selectedTab="#{adminConsoleBean.selectedTab}">

				<rich:tab id="rejectsTab" label="Rejects"
					action="#{adminConsoleBean.switchToRejectedUsers}"
					rendered="#{!sessionDataBean.systemUser.ipsUser}">
					<ui:insert name="rejectsTab" />
				</rich:tab>

				<rich:tab id="exceptionReportingTab"
					label="Exception Reporting"
					action="#{adminConsoleBean.switchToExceptionReporting}"
					rendered="#{!sessionDataBean.systemUser.ipsUser}">
					<ui:insert name="exceptionTab" />
				</rich:tab>

				<rich:tab id="tableMaintenanceTab" label="Table Maintenance"
					action="#{adminConsoleBean.switchToTableMaintenance}">
					<ui:insert name="tableMaintenanceTab" />
				</rich:tab>
			</rich:tabPanel>
		</ui:define>
	</ui:composition>
</html>
