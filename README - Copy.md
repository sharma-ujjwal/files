<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
        "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:ui="http://java.sun.com/jsf/facelets"
      xmlns:h="http://xmlns.jcp.org/jsf/html"
      xmlns:f="http://java.sun.com/jsf/core"
      xmlns:rich="http://richfaces.org/rich"
      xmlns:a4j="http://richfaces.org/a4j">

<!-- 

Include this xhtml file with ui:include tags and provide the following parameters:

* aSelectionBean - A bean that extends the abstract class SelectionBean.java, and
    defines behavior for how to initialize the lists, what to do when the apply
    button is pressed, and return items when the user hits the search button.

* panelId - A unique id for the panel to use.  Pick an id that is not used by any
    other element on the including page, including other selectionModal.xhtml
    includes.

* formId - A unique id to give the form the modal panel uses.  Pick an id that
    is not used by any other element on the including page, including other
    selectionModal.xhtml includes.

* rerenderOnApply - A component id to rerender after the user hits apply, used in
    case the results of what the user selected needs to be updated somewhere else on
    the page.

* panelLabel - A string to display for the modal panel label.

* leftLabel - A string to display above the left (available items) list.

* rightLabel - A string to display above the right (selected items) list.

-->


<ui:composition>

    <rich:popupPanel id="#{panelId}" moveable="true" resizeable="true">

        <f:facet name="header" style="background-color:#d4cfc7;">
            <h:outputLabel style="font-weight: bold;">#{panelLabel}</h:outputLabel>
        </f:facet>

        <a4j:region id="#{formId}region" style="background-color:#ffffff;">

    <h:form id="#{formId}">
      
      <table><tbody>
        <tr>
        	<td>
        		<h:messages id="noSelectionFoundMessage" style="color: #e80c00; font-size: 11pt;"/>
        	</td>
        </tr>      
        <tr>
          <td colspan="2">
            <h:inputText value="#{aSelectionBean.availableSearchText}" onkeypress="return ((event.keyCode || event.which) != 13);" />
            <a4j:commandButton id="searchSubmitButton" value="Search" action="#{aSelectionBean.searchAvailable}" render="#{formId}" />
            <h:outputText value=" " />
            <a4j:status>
              <f:facet name="start">
                    <h:graphicImage  value="/images/loader.gif"/>
                </f:facet>
            </a4j:status>
          </td>
        </tr>
        <tr>
          <td>
            <div style="width: 200px; height: 200px; overflow: auto; border-style:solid; border-width: 1px">
              <h:dataTable id="bundleDatatable" value="#{aSelectionBean.available}" var="item">
                <h:column>
                  <h:selectBooleanCheckbox value="#{item.selected}">
                    <a4j:ajax event="click" render="" />
                  </h:selectBooleanCheckbox>
                </h:column>
                <h:column>
                  <h:outputText value="#{item.displayString}"/>
                </h:column>
              </h:dataTable>
            </div>
          </td>
          <td>
            <a4j:commandButton value="Add" style="width:60px" action="#{aSelectionBean.add}" render="#{formId}" />
            <br />
            <a4j:commandButton value="Remove" style="width:60px" action="#{aSelectionBean.remove}" render="#{formId}" />
          </td>
          <td>
            #{rightLabel}
            <div style="width: 200px; height: 200px; overflow: auto; border-style:solid; border-width: 1px">
              <h:dataTable value="#{aSelectionBean.chosen}" var="item">
                <h:column><h:selectBooleanCheckbox value="#{item.selected}" /></h:column>
                <h:column><h:outputText value="#{item.displayString}" /></h:column>
              </h:dataTable>
            </div>
          </td>
        </tr>
      </tbody></table>
      <a4j:commandButton
          value="Apply" 
          onclick="Richfaces.hideModalPanel('#{panelId}');"
          action="#{aSelectionBean.apply}"
      	  reRender="#{rerenderOnApply}" />
      <a4j:commandButton
          value="Cancel" 
          onclick="Richfaces.hideModalPanel('#{panelId}');" />
    </h:form>
    </a4j:region> 
   
  </rich:popupPanel>
</ui:composition>

</html>