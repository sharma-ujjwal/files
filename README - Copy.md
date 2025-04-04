```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html
  xmlns="http://www.w3.org/1999/xhtml"
  xmlns:ui="http://java.sun.com/jsf/facelets"
  xmlns:h="http://xmlns.jcp.org/jsf/html"
  xmlns:f="http://java.sun.com/jsf/core"
  xmlns:rich="http://richfaces.org/rich"
  xmlns:a4j="http://richfaces.org/a4j"
  xmlns:c="http://java.sun.com/jstl/core">

<ui:composition template="../common/main.xhtml">

<ui:define name="body">
  <h:outputLabel styleClass="sectionHeader" value="Add Review Reports" />
  <a4j:status>
    <f:facet name="start"><h:graphicImage value="/images/loader.gif"/></f:facet>
  </a4j:status>
  <div class="verticalSpacer"/>
  <table><tbody>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="Review:" styleClass="fieldLabel" />
      </td>
      <td class="rightSpaced">
        <h:selectOneMenu value="#{createBundleBean.selectedReview}" onchange="this.form.submit();">
          <f:selectItems value="#{createBundleBean.availableReviews}" />
        </h:selectOneMenu>
      </td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="Conflict Type:" styleClass="fieldLabel" rendered="#{createBundleBean.segregationOfDuties}" />
      </td>
      <td class="rightSpaced">
        <h:selectOneMenu value="#{createBundleBean.selectedConflictType}" rendered="#{createBundleBean.segregationOfDuties}">
          <f:selectItems value="#{createBundleBean.availableConflictTypes}" />
          <a4j:ajax
              event="change"
              action="#{createBundleBean.conflictTypeChanged}"
              render="addConflictButton,selectedConflicts" />
        </h:selectOneMenu>
      </td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="Access Conflicts:" styleClass="fieldLabel" rendered="#{createBundleBean.segregationOfDuties}"/>
      </td>
      <td class="rightSpaced">
        <h:inputText readonly="true" size="40" id="selectedConflicts" value="#{createBundleBean.displayConflicts}" 
            rendered="#{createBundleBean.segregationOfDuties}" />
        <a4j:outputPanel ajaxRendered="true" rendered="#{createBundleBean.segregationOfDuties}">
        <a4j:commandButton value="Add"
            id="addConflictButton"
            action="#{conflictSelectionBean.init}"
            oncomplete="javascript:Richfaces.showModalPanel('conflictModalPanel',{top:100, left:100, width:1200, height:340});"
            reRender="conflictSelectionForm"
            rendered="#{createBundleBean.showAddConflictButton}"
            />
        </a4j:outputPanel>
      </td>
    </tr>
    <tr>
      <td class="">
        <div class="verticalSpacer"/>
        <h:outputLabel value="Choose Filter Criteria:" styleClass="fieldLabel" />
        <div class="verticalSpacer"/>
      </td>
      <td>&nbsp;</td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="Managers:" styleClass="fieldLabel" />
      </td>
      <td class="rightSpaced">
        <h:inputText readonly="true" size="40" id="selectedManagers" value="#{createBundleBean.displayManagers}" />
        <a4j:commandButton 
            value="Add"
            action="#{managerSelectionBean.init}"
            oncomplete="Richfaces.component(managerModalPanel,{top:100, left:100, width:600, height:340});"
            render="managerSelectionForm" />
      </td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="User Names:" styleClass="fieldLabel" />
      </td>
      <td class="rightSpaced">
        <h:inputText readonly="true" size="40" id="selectedIndividuals" value="#{createBundleBean.displayIndividuals}" />
        <a4j:commandButton 
            value="Add"
            action="#{individualSelectionBean.init}"
            oncomplete="Richfaces.component(individualModalPanel,{top:100, left:100, width:600, height:340});"
            render="individualSelectionForm" />
      </td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="Application Names:" styleClass="fieldLabel" rendered="#{!createBundleBean.segregationOfDuties}" />
      </td>
      <td class="rightSpaced">
        <h:inputText readonly="true" size="40" id="selectedApplications" value="#{createBundleBean.displayApplications}" 
            rendered="#{!createBundleBean.segregationOfDuties}"
        />
        <a4j:commandButton value="Add"
            action="#{applicationSelectionBean.init}"
            oncomplete="Richfaces.component(applicationModalPanel, {top:100, left:100, width:600, height:340});"
            render="applicationSelectionForm"
            rendered="#{!createBundleBean.segregationOfDuties}" />
      </td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="SOX Concern Values:" styleClass="fieldLabel" />
      </td>
      <td class="rightSpaced">
        <h:selectManyCheckbox value="#{createBundleBean.soxConcernValues}" styleClass="fieldValue">
          <f:selectItems value="#{createBundleBean.availableSoxConcernValues}" />
        </h:selectManyCheckbox>
      </td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="Environment:" styleClass="fieldLabel" />
      </td>
      <td class="rightSpaced">
        <h:selectManyCheckbox value="#{createBundleBean.environments}" styleClass="fieldValue">
          <f:selectItems value="#{createBundleBean.availableEnvironments}" />
        </h:selectManyCheckbox>
      </td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="User Status:" styleClass="fieldLabel" />
      </td>
      <td class="rightSpaced">
        <h:selectManyCheckbox value="#{createBundleBean.userStatuses}" styleClass="fieldValue">
          <f:selectItems value="#{createBundleBean.availableUserStatuses}" />
        </h:selectManyCheckbox>
      </td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="User Type:" styleClass="fieldLabel" />
      </td>
      <td class="rightSpaced">
        <h:selectManyListbox value="#{createBundleBean.userTypes}" size="8">
          <f:selectItems value="#{createBundleBean.availableUserTypes}" />
        </h:selectManyListbox>
      </td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="Source Name:" styleClass="fieldLabel" />
      </td>
      <td class="rightSpaced">
        <h:inputText readonly="true" size="40" id="selectedSystems" value="#{createBundleBean.displaySystems}" />
        <a4j:commandButton value="Add"
            action="#{sourceNameSelectionBean.init}"
            oncomplete="javascript:Richfaces.showModalPanel('systemModalPanel',{top:100, left:100, width:600, height:340});"
            render="systemSelectionForm" />
      </td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="Priv Description:" styleClass="fieldLabel" />
      </td>
      <td class="rightSpaced">
        <h:inputText readonly="true" size="40" id="selectedPrivDescriptions" value="#{createBundleBean.displayPrivDescriptions}" />
        <a4j:commandButton 
            value="Add"
            action="#{privDescriptionSelectionBean.init}"
            oncomplete="javascript:Richfaces.showModalPanel('privDescriptionModalPanel',{top:100, left:100, width:600, height:340});"
            render="privDescriptionSelectionForm" />
      </td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="Priv Value" styleClass="fieldLabel" />
      </td>
      <td class="rightSpaced">
        <h:inputText readonly="true" size="40" id="selectedPrivValues" value="#{createBundleBean.displayPrivValues}" />
        <a4j:commandButton 
            value="Add"
            action="#{privValueSelectionBean.init}"
            oncomplete="javascript:Richfaces.showModalPanel('privValueModalPanel',{top:100, left:100, width:600, height:340});"
            reRender="privValueSelectionForm" />
      </td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="Cost Center: " styleClass="fieldLabel" />
      </td>
      <td class="rightSpaced">
        <h:inputText readonly="true" size="40" id="selectedCostCenters" value="#{createBundleBean.displayCostCenters}" />
        <a4j:commandButton value="Add"
            action="#{costCenterSelectionBean.init}"
            oncomplete="javascript:Richfaces.showModalPanel('costCenterModalPanel',{top:100, left:100, width:600, height:340});"
            render="costCenterSelectionForm" />
      </td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="Department:" styleClass="fieldLabel" />
      </td>
      <td class="rightSpaced">
        <h:inputText readonly="true" size="40" id="selectedDepartments" value="#{createBundleBean.displayDepartments}" />
        <a4j:commandButton value="Add"
            action="#{departmentSelectionBean.init}"
            oncomplete="javascript:Richfaces.showModalPanel('departmentModalPanel',{top:100, left:100, width:600, height:340});"
            render="departmentSelectionForm" />
      </td>
    </tr>
    <tr>
      <td class="sideSpaced">
        <h:outputLabel value="Division:" styleClass="fieldLabel" rendered="#{!createBundleBean.segregationOfDuties}" />
      </td>
      <td class="rightSpaced">
        <h:inputText readonly="true" size="40" id="selectedDivisions" value="#{createBundleBean.displayDivisions}" 
            rendered="#{!createBundleBean.segregationOfDuties}"
        />
        <a4j:commandButton value="Add"
            action="#{divisionSelectionBean.init}"
            oncomplete="javascript:Richfaces.showModalPanel('divisionModalPanel',{top:100, left:100, width:600, height:340});"
            render="divisionSelectionForm"
            rendered="#{!createBundleBean.segregationOfDuties}" />
      </td>
    </tr>

  </tbody></table>
  <h:commandButton value="Continue" action="#{createBundleBean.doContinue}" />
  <h:commandButton value="Clear" action="#{createBundleBean.doClear}" />
</ui:define>

<ui:define name="modalPanels">
  <ui:include src="selectionModal.xhtml" >
    <ui:param name="aSelectionBean" value="#{managerSelectionBean}" />
    <ui:param name="formId" value="managerSelectionForm" />
    <ui:param name="panelId" value="managerModalPanel" />
    <ui:param name="rerenderOnApply" value="selectedManagers" />
    <ui:param name="panelLabel" value="Add Managers" />
    <ui:param name="leftLabel" value="Available Managers: " />
    <ui:param name="rightLabel" value="Chosen Managers: " />
  </ui:include>

  <ui:include src="selectionModal.xhtml" >
    <ui:param name="aSelectionBean" value="#{individualSelectionBean}" />
    <ui:param name="formId" value="individualSelectionForm" />
    <ui:param name="panelId" value="individualModalPanel" />
    <ui:param name="rerenderOnApply" value="selectedIndividuals" />
    <ui:param name="panelLabel" value="Add Individuals" />
    <ui:param name="leftLabel" value="Available Individuals: " />
    <ui:param name="rightLabel" value="Chosen Individuals: " />
  </ui:include>

  <ui:include src="selectionModal.xhtml" >
    <ui:param name="aSelectionBean" value="#{applicationSelectionBean}" />
    <ui:param name="formId" value="applicationSelectionForm" />
    <ui:param name="panelId" value="applicationModalPanel" />
    <ui:param name="rerenderOnApply" value="selectedApplications" />
    <ui:param name="panelLabel" value="Add Applications" />
    <ui:param name="leftLabel" value="Available Applications: " />
    <ui:param name="rightLabel" value="Chosen Applications: " />
  </ui:include>

  <ui:include src="selectionModal.xhtml" >
    <ui:param name="aSelectionBean" value="#{sourceNameSelectionBean}" />
    <ui:param name="formId" value="systemSelectionForm" />
    <ui:param name="panelId" value="systemModalPanel" />
    <ui:param name="rerenderOnApply" value="selectedSystems" />
    <ui:param name="panelLabel" value="Add Systems" />
    <ui:param name="leftLabel" value="Available Source Names: " />
    <ui:param name="rightLabel" value="Chosen Source Names: " />
  </ui:include>

  <ui:include src="selectionModal.xhtml" >
    <ui:param name="aSelectionBean" value="#{departmentSelectionBean}" />
    <ui:param name="formId" value="departmentSelectionForm" />
    <ui:param name="panelId" value="departmentModalPanel" />
    <ui:param name="rerenderOnApply" value="selectedDepartments" />
    <ui:param name="panelLabel" value="Add Departments" />
    <ui:param name="leftLabel" value="Available Departments: " />
    <ui:param name="rightLabel" value="Chosen Departments: " />
  </ui:include>
  
  <ui:include src="selectionModal.xhtml" >
    <ui:param name="aSelectionBean" value="#{divisionSelectionBean}" />
    <ui:param name="formId" value="divisionSelectionForm" />
    <ui:param name="panelId" value="divisionModalPanel" />
    <ui:param name="rerenderOnApply" value="selectedDivisions" />
    <ui:param name="panelLabel" value="Add Divisions" />
    <ui:param name="leftLabel" value="Available Divisions: " />
    <ui:param name="rightLabel" value="Chosen Divisions: " />
  </ui:include> 

  <ui:include src="selectionModal.xhtml" >
    <ui:param name="aSelectionBean" value="#{privDescriptionSelectionBean}" />
    <ui:param name="formId" value="privDescriptionSelectionForm" />
    <ui:param name="panelId" value="privDescriptionModalPanel" />
    <ui:param name="rerenderOnApply" value="selectedPrivDescriptions" />
    <ui:param name="panelLabel" value="Add Privilege Descriptions" />
    <ui:param name="leftLabel" value="Available Privilege Descriptions: " />
    <ui:param name="rightLabel" value="Chosen Privilege Descriptions: " />
  </ui:include>

  <ui:include src="selectionModal.xhtml" >
    <ui:param name="aSelectionBean" value="#{privValueSelectionBean}" />
    <ui:param name="formId" value="privValueSelectionForm" />
    <ui:param name="panelId" value="privValueModalPanel" />
    <ui:param name="rerenderOnApply" value="selectedPrivValues" />
    <ui:param name="panelLabel" value="Add Privilege Values" />
    <ui:param name="leftLabel" value="Available Privilege Values: " />
    <ui:param name="rightLabel" value="Chosen Privilege Values: " />
  </ui:include>

  <ui:include src="selectionModal.xhtml" >
    <ui:param name="aSelectionBean" value="#{costCenterSelectionBean}" />
    <ui:param name="formId" value="costCenterSelectionForm" />
    <ui:param name="panelId" value="costCenterModalPanel" />
    <ui:param name="rerenderOnApply" value="selectedCostCenters" />
    <ui:param name="panelLabel" value="Add Cost Centers" />
    <ui:param name="leftLabel" value="Available Cost Centers: " />
    <ui:param name="rightLabel" value="Chosen Cost Centers: " />
  </ui:include>  
  
  <ui:include src="selectionModalBasic.xhtml" >
    <ui:param name="aSelectionBean" value="#{conflictSelectionBean}" />
    <ui:param name="formId" value="conflictSelectionForm" />
    <ui:param name="panelId" value="conflictModalPanel" />
    <ui:param name="rerenderOnApply" value="selectedConflicts" />
    <ui:param name="panelLabel" value="Conflicts: " />
    <ui:param name="leftLabel" value="Available Conflicts: " />
    <ui:param name="rightLabel" value="Chosen Conflicts: " />
  </ui:include>    
</ui:define>

</ui:composition>
</html>
```
