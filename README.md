```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:ui="http://java.sun.com/jsf/facelets"
      xmlns:h="http://java.sun.com/jsf/html"
      xmlns:f="http://java.sun.com/jsf/core">

<ui:composition template="../common/main.xhtml">
    <ui:define name="body">
        <h:form id="adminConsoleNavForm">
            <div class="tabBar">
                <h:commandButton value="Rejects"
                                 action="#{adminConsoleBean.switchToRejectedUsers}"
                                 styleClass="#{adminConsoleBean.selectedTab == 0 ? 'tab active' : 'tab'}" />
                <h:commandButton value="Exception Reporting"
                                 action="#{adminConsoleBean.switchToExceptionReporting}"
                                 styleClass="#{adminConsoleBean.selectedTab == 1 ? 'tab active' : 'tab'}" />
                <h:commandButton value="Table Maintenance"
                                 action="#{adminConsoleBean.switchToTableMaintenance}"
                                 styleClass="#{adminConsoleBean.selectedTab == 2 ? 'tab active' : 'tab'}" />
            </div>
        </h:form>

        <!-- Main tab body (from departmentSummary.xhtml, etc.) -->
        <ui:insert name="tableMaintenanceTab" />
        <ui:insert name="referenceCoreTable" />
        <ui:insert name="modalPanels" />
    </ui:define>
</ui:composition>
</html>


<style>
.tabBar {
    display: flex;
    gap: 6px;
    padding: 0;
    margin-bottom: 10px;
    border-bottom: 2px solid #ccc;
}

.tab {
    background-color: #f2f2f2;
    border: 1px solid #ccc;
    border-bottom: none;
    border-radius: 5px 5px 0 0;
    padding: 6px 16px;
    font-weight: bold;
    cursor: pointer;
    text-align: center;
    text-decoration: none;
    color: #333;
}

.tab:hover {
    background-color: #e0e0e0;
}

.tab.active {
    background-color: #ffffff;
    border-bottom: 2px solid #ffffff;
}
</style>
