```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://java.sun.com/jsf/facelets"
	xmlns:h="http://xmlns.jcp.org/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	xmlns:p="http://primefaces.org/ui">

<ui:composition template="../common/referenceCoreTableTemplate.xhtml">
		<ui:define name="referenceCoreTable">
			<div style="float: left">
				<!--  Search -->
				<p:outputPanel id="searchOutputPanel" autoUpdate="true">
					<h:outputLabel id="departmentSearchLabel"
						value="Department/Cost Center Name: " />

					<!--  department Name Search Text -->
					<h:inputText id="departmentSearchText" maxlength="100"
						value="#{departmentSummaryBean.departmentSearchText}"
						onchange="this.value = this.value.toUpperCase();">
					</h:inputText>
				</p:outputPanel>

				<span style="width: 65px"></span>

				<h:commandButton id="searchButton" value="Go"
							action="#{departmentSummaryBean.goSearch}" />

				<!-- Reset Button -->
				<h:commandButton id="resetButton" value="Reset"
					action="#{departmentSummaryBean.resetSearch}" />


				<!--  Select Filter -->
				<h:outputLabel id="selectFilterLabel" value="Filter: " />  

				<!--  Select Filter ListBox -->
				<h:selectOneMenu id="filterListbox"
					value="#{departmentSummaryBean.activeFilter}">
					<p:ajax event="change"
						action="#{departmentSummaryBean.switchFilterTables}" />
					<f:selectItems value="#{departmentSummaryBean.availableFilters}" />
				</h:selectOneMenu>
			</div>

			<p:outputPanel layout="block" autoUpdate="true" style="float: right;">
				<p:commandButton id="addNewButton" value="Add New Row"
					action="#{departmentSummaryBean.showAddDepartmentPanel}" />

				<p:commandButton id="deleteRowButton" value="Delete Row"
					action="#{departmentSummaryBean.showDeleteDepartmentPanel}"
					rendered="#{departmentSummaryBean.deleteEnabled}" />
			</p:outputPanel>

			<p:outputPanel layout="block" autoUpdate="true"
				style="width: 100%; clear: both;">

				<!-- department Display Table -->
				<p:dataTable id="departmentTable"
							 value="#{departmentSummaryBean.departmentList}"
							 var="department"
							 rows="#{departmentSummaryBean.displayAmount}"
							 styleClass="defaultTableHeader"
							 rowStyleClass="oddRow, evenRow">

					<h:column id="deleteCheckBoxColumn"
							  rendered="#{departmentSummaryBean.deleteEnabled}">
						<f:facet name="header">
							<p:commandLink
								action="#{departmentSummaryBean.switchCheckBoxToggle}"
								value="#{departmentSummaryBean.selectAllLinkText}" 
								styleClass="headerSortLink" />
						</f:facet>
						<h:selectBooleanCheckbox value="#{department.checked}"
							id="DepartmentCheckBox" />
					</h:column>

					<h:column id="idColumn">
						<f:facet name="header">
							<p:commandLink value="ID"
								action="#{departmentSummaryBean.doSort}"
								styleClass="headerSortLink">
								<f:param name="column" value="id" />
							</p:commandLink>
						</f:facet>	
						#{department.id}  
					</h:column>

					<h:column id="nameColumn">
						<f:facet name="header">
							<p:commandLink value="Department Name"
								action="#{departmentSummaryBean.doSort}"
								styleClass="headerSortLink">
								<f:param name="column" value="name" />
							</p:commandLink>
						</f:facet>	
						#{department.name}  
					</h:column>

					<h:column id="costCenterColumn">
						<f:facet name="header">
							<p:commandLink value="Cost Center"
								action="#{departmentSummaryBean.doSort}"
								styleClass="headerSortLink">
								<f:param name="column" value="costCenter" />
							</p:commandLink>
						</f:facet>	
						#{department.costCenter}  
					</h:column>

					<h:column id="departmentNameCostCenterColumn">
						<f:facet name="header">
							<p:commandLink value="Cost Center Name"
								action="#{departmentSummaryBean.doSort}"
								styleClass="headerSortLink">
								<f:param name="column" value="departmentNameCostCenter" />
							</p:commandLink>
						</f:facet>	
						#{department.departmentNameCostCenter}  
					</h:column>

					<h:column id="createdByColumn">
						<f:facet name="header">
							<p:commandLink value="Created by"
								action="#{departmentSummaryBean.doSort}"
								styleClass="headerSortLink">
								<f:param name="column" value="createdBy" />
							</p:commandLink>
						</f:facet>	
						#{department.createdBy}  
					</h:column>

					<h:column id="createdDateColumn">
						<f:facet name="header">
							<p:commandLink value="Created date"
								action="#{departmentSummaryBean.doSort}"
								styleClass="headerSortLink">
								<f:param name="column" value="createdDate" />
							</p:commandLink>
						</f:facet>	
						<h:outputText value="#{department.createdDate}">
							<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}" />
						</h:outputText>  
					</h:column>

					<h:column id="lastChangedByColumn">
						<f:facet name="header">
							<p:commandLink value="Last changed by"
								action="#{departmentSummaryBean.doSort}"
								styleClass="headerSortLink">
								<f:param name="column" value="lastChangedBy" />
							</p:commandLink>
						</f:facet>	
						#{department.lastChangedBy}  
					</h:column>

					<h:column id="lastChangedDateColumn">
						<f:facet name="header">
							<p:commandLink value="Last changed date"
								action="#{departmentSummaryBean.doSort}"
								styleClass="headerSortLink">
								<f:param name="column" value="lastChangedDate" />
							</p:commandLink>
						</f:facet>	
						<h:outputText value="#{department.lastChangedDate}">
							<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}" />
						</h:outputText>  
					</h:column>
					
					<h:column id="deleteFlagColumn">
						<f:facet name="header">
							<p:commandLink value="Delete Flag"
								action="#{departmentSummaryBean.doSort}"
								styleClass="headerSortLink">
								<f:param name="column" value="deleteFlag" />
							</p:commandLink>
						</f:facet>	
						#{department.deleteFlag}  
					</h:column>

					<h:column id="activeFromDateColumn">
						<f:facet name="header">
							<p:commandLink value="Active from"
								action="#{departmentSummaryBean.doSort}"
								styleClass="headerSortLink">
								<f:param name="column" value="activeFromDate" />
							</p:commandLink>
						</f:facet>	
						<h:outputText value="#{department.activeFromDate}">
							<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}" />
						</h:outputText>  
					</h:column>

					<h:column id="activeToDateColumn">
						<f:facet name="header">
							<p:commandLink value="Active to"
								action="#{departmentSummaryBean.doSort}"
								styleClass="headerSortLink">
								<f:param name="column" value="activeToDate" />
							</p:commandLink>
						</f:facet>	
						<h:outputText value="#{department.activeToDate}">
							<f:convertDateTime pattern="yyyy-MM-dd HH:mm:ss" timeZone="#{applicationBean.timeZone}" />
						</h:outputText>  
					</h:column>
				</p:dataTable>

				<!-- Data pagination -->
				<table width="100%">
					<tbody>
						<tr>
							<td align="right">
								<h:selectOneMenu id="departmentDisplayRows"
									value="#{departmentSummaryBean.displayAmount}"
									styleClass="fieldValue">
									<f:selectItems
										value="#{applicationBean.availableRowDisplayItems}" />
									<p:ajax event="change"
										action="#{departmentSummaryBean.doDisplayRowListener}"
										oncomplete="window.scrollTo(0, 0);" />
								</h:selectOneMenu>
								<h:outputText value=" items per page " />
							</td>
						</tr>
					</tbody>
				</table>
			</p:outputPanel>
		</ui:define>

		<ui:define name="modalPanels">
			<!-- add department modal panel -->
			<p:outputPanel ajaxRendered="true" id="adddepartmentModalAjaxPanel">
				<p:dialog id="addModalPanel" resizeable="true"
					draggable="true" height="400" width="450"
					rendered="#{departmentSummaryBean.renderAddDepartmentModalPanel}"
					visible="true">

					<f:facet name="header">
						<h:outputLabel value="Add Department" />
					</f:facet>

					<div>
						<h:messages id="msgId" fatalClass="fatalMessage"
							errorClass="errorMessage" warnClass="warningMessage"
							infoClass="infoMessage" layout="table" />
					</div>

					<h:form id="addDepartmentForm">
						<div class="verticalSpacer" />
						<div>
							<h:outputLabel value="Department Name: " styleClass="fieldLabel" />
							<h:inputText id="nameText" maxlength="50" tabindex="0"
								value="#{departmentSummaryBean.departmentName}" 
								onchange="this.value = this.value.toUpperCase();"/>
						</div>
						<div class="verticalSpacer" />

						<div>
							<h:outputLabel value="Cost Center Number:   "
								styleClass="fieldLabel" />
							<h:inputText id="costCenterNumberText" maxlength="7"
								value="#{departmentSummaryBean.costCenterNumber}" 
								onchange="this.value = this.value.toUpperCase();"/>
						</div>
						<div class="verticalSpacer" />

						<div>
							<p:commandButton id="saveButton" value="Save"
								action="#{departmentSummaryBean.doAddDepartment}" />

							<p:commandButton id="cancelButton" value="Cancel"
								action="#{departmentSummaryBean.doCancelAdd}" />
						</div>
					</h:form>
				</p:dialog>
			</p:outputPanel>

			<!-- delete department modal panel -->
			<p:outputPanel autoUpdate="true"
				id="deleteDepartmentModalAjaxPanel">
				<p:dialog id="deleteConfirmModalPanel" resizeable="true"
					draggable="true" height="400" width="450"
					rendered="#{departmentSummaryBean.renderDeleteDepartmentModalPanel}"
					visible="true" style="overflow:auto;">

					<f:facet name="header">
						<h:outputLabel value="Delete Row(s)" />
					</f:facet>

					<h:form id="deleteConfirmForm">
						<div class="verticalSpacer" />
						<div>
							<div class="sectionHeaderSmall">
								Delete Department(s)?
							</div>
						</div>
						<h:dataTable id="selectDeletedRowsTable"
							value="#{departmentSummaryBean.deletedDepartmentList}"
							var="department">
							<h:column>#{department.departmentNameCostCenter}</h:column>
						</h:dataTable>

						<div>
							<p:commandButton id="deleteButton" value="Delete"
								action="#{departmentSummaryBean.doDelete}" />

							<p:commandButton id="cancelDeleteButton" value="Cancel"
								action="#{departmentSummaryBean.doCancelDelete}" />
						</div>
					</h:form>
				</p:dialog>
			</p:outputPanel>
		</ui:define>
	</ui:composition>
</html>


