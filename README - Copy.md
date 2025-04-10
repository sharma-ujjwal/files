```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
	xmlns:h="http://xmlns.jcp.org/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	xmlns:p="http://primefaces.org/ui">

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

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
      xmlns:h="http://xmlns.jcp.org/jsf/html"
      xmlns:f="http://java.sun.com/jsf/core"
      xmlns:p="http://primefaces.org/ui">

    <ui:composition template="../common/main.xhtml">
        <ui:define name="body">
            <!-- Replace a4j:status with p:ajaxStatus -->
            <p:ajaxStatus id="tableMaintenanceAjaxLoadStatus">
                <f:facet name="start">
                    <script type="text/javascript">showModalInfoWindow();</script>
                </f:facet>
                <f:facet name="complete">
                    <script type="text/javascript">hideModalInfoWindow();</script>
                </f:facet>
            </p:ajaxStatus>

            <!-- Replace rich:tabPanel with p:tabView -->
            <p:tabView id="tabPanel" 
                       activeIndex="#{adminConsoleBean.selectedTabIndex}" 
                       dynamic="true"
                       cache="false"
                       styleClass="tabStyle">

                <!-- Replace rich:tab with p:tab -->
                <p:tab id="rejectsTab" 
                       title="Rejects"
                       rendered="#{!sessionDataBean.systemUser.ipsUser}">
                    <p:ajax event="tabChange" 
                            listener="#{adminConsoleBean.switchToRejectedUsers}" />
                    <ui:insert name="rejectsTab" />
                </p:tab>

                <p:tab id="exceptionReportingTab" 
                       title="Exception Reporting"
                       rendered="#{!sessionDataBean.systemUser.ipsUser}">
                    <p:ajax event="tabChange" 
                            listener="#{adminConsoleBean.switchToExceptionReporting}" />
                    <ui:insert name="exceptionTab" />
                </p:tab>

                <p:tab id="tableMaintenanceTab" 
                       title="Table Maintenance">
                    <p:ajax event="tabChange" 
                            listener="#{adminConsoleBean.switchToTableMaintenance}" />
                    <ui:insert name="tableMaintenanceTab" />
                </p:tab>
            </p:tabView>
        </ui:define>
    </ui:composition>
</html>
