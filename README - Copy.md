```
package com.assurant.inc.sox.ar.client.bean.tasklist;

import java.util.*;

import javax.annotation.PostConstruct;
import javax.faces.component.UIComponent;
import javax.faces.component.UIParameter;
import javax.faces.component.html.HtmlOutputText;
import javax.faces.model.SelectItem;

import org.apache.commons.lang3.StringUtils;

import com.assurant.inc.sox.ar.client.bean.ApplicationDataBean;
import com.assurant.inc.sox.ar.client.bean.SessionDataBean;
import com.assurant.inc.sox.ar.client.bean.util.CommonPageActionHelper;
import com.assurant.inc.sox.ar.client.bean.util.HtmlTableBuilderUtil;
import com.assurant.inc.sox.ar.client.bean.util.JSFUtils;
import com.assurant.inc.sox.ar.client.ui.tasklist.AbstractTaskListUI;
import com.assurant.inc.sox.ar.comparators.GenericComparator;
import com.assurant.inc.sox.ar.dto.enums.tasklist.TaskListField;
import com.assurant.inc.sox.ar.dto.enums.tasklist.TaskListFilterType;
import com.assurant.inc.sox.ar.dto.tasklist.AbstractTaskListDTO;
import com.assurant.inc.sox.consts.TaskTypeCode;
import org.primefaces.component.column.Column;
import org.primefaces.component.commandlink.CommandLink;
import org.primefaces.component.datatable.DataTable;
import org.primefaces.model.FilterMeta;
import org.primefaces.model.LazyDataModel;
import org.primefaces.model.SortMeta;
import org.primefaces.model.SortOrder;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;

public abstract class AbstractTaskListBean<T extends AbstractTaskListUI, K extends AbstractTaskListDTO> {
	private static final Logger logger = LoggerFactory.getLogger(AbstractTaskListBean.class);
	protected static final String SORT_ACTION_PARAM_NAME = "sortFieldName";
	protected static final String WORK_TASK_ACTION_PARAM_NAME = "workTaskName";
	protected static final String WORK_TASK_TYPE_ACTION_PARAM_NAME = "workTaskTypeName";

	private static final List<SelectItem> FILTER_VALUES_TASK_TYPE;
	private static final List<SelectItem> TASK_LIST_FILTER_TYPES;

	protected final List<FilterEntry> filterEntries;

	protected final String beanName;
	protected final String idPrefix;
	protected String taskListFilterTypeCode;
	protected String taskListFilterValue;
	protected String taskListFilterTextValue;
	protected DataTable taskListTable;
	protected List<T> lazyTaskList;
	protected LazyDataModel<T> lazyTaskListModel;
	protected String previousSortFieldCodeValue;
	protected boolean beanLoaded;
	@Autowired
	@Qualifier("sessionDataBean")
	protected SessionDataBean sessionDataBean;
	protected boolean renderTaskList;
	protected Object lock = new Object();
	@Autowired
	@Qualifier("applicationBean")
	protected ApplicationDataBean applicationBean;

	// init the the filter types.
	static {
		List<SelectItem> availableTLFilterTypesLoc = new ArrayList<SelectItem>(2);
		availableTLFilterTypesLoc.add(new SelectItem(TaskListFilterType.REVIEW_TYPE.getCode(), TaskListFilterType.REVIEW_TYPE
		    .getDisplayValue()));
		availableTLFilterTypesLoc.add(new SelectItem(TaskListFilterType.TASK_NAME.getCode(), TaskListFilterType.TASK_NAME
		    .getDisplayValue()));
		availableTLFilterTypesLoc.add(new SelectItem(TaskListFilterType.ASSIGNED_TO.getCode(), TaskListFilterType.ASSIGNED_TO
		    .getDisplayValue()));
		TASK_LIST_FILTER_TYPES = Collections.unmodifiableList(availableTLFilterTypesLoc);

		List<SelectItem> filterValuesTaksType = new ArrayList<SelectItem>(2);
		filterValuesTaksType.add(new SelectItem("", ""));
		for (TaskTypeCode typeCode : TaskTypeCode.values()) {
			filterValuesTaksType.add(new SelectItem(typeCode.getCode(), typeCode.getDisplay()));
		}
		FILTER_VALUES_TASK_TYPE = Collections.unmodifiableList(filterValuesTaksType);
	}

	@PostConstruct
	public void init() {
		lazyTaskList = retrieveTaskLists();
		lazyTaskListModel = new LazyDataModel<>() {
			@Override
			public int count(Map<String, FilterMeta> map) {
				return map.size();
			}

			@Override
			public List<T> load(int first, int pageSize, Map<String, SortMeta> map, Map<String, FilterMeta> map1) {
				System.out.println("LOADING........................ \n \n \n F :" + first + " SIZE : " + pageSize);
				int toIndex = Math.min(first + pageSize, lazyTaskList.size());
				this.setRowCount(lazyTaskList.size());

				return lazyTaskList.subList(first, toIndex);
			}

			@Override
			public String getRowKey(T object) {
				// Replace `getId()` with the method that returns the unique identifier for your object
				return object.getTaskId();
			}

			@Override
			public T getRowData(String rowKey) {
				// Replace `getId()` with the method that returns the unique identifier for your object
				for (T task : lazyTaskList) {
					if (task.getTaskId().equals(rowKey)) {
						return task;
					}
				}
				return null;
			}
		};
	}

	public LazyDataModel<T> getLazyTaskList() {
		return lazyTaskListModel;
	}

	public AbstractTaskListBean(TaskListFilterType filterType, String beanName, String idPrefix) {
		this.taskListFilterTypeCode = filterType.getCode();
		this.filterEntries = new ArrayList<FilterEntry>(1);
		this.beanName = beanName;
		this.idPrefix = idPrefix;
		this.beanLoaded = false;
		this.renderTaskList = true;
	}

	// ------------------------ Faces injected properties ---------------------
	public SessionDataBean getSessionDataBean() {
		return sessionDataBean;
	}

	public void setSessionDataBean(SessionDataBean sessionDataBean) {
		this.sessionDataBean = sessionDataBean;
	}

	public ApplicationDataBean getApplicationBean() {
		return applicationBean;
	}

	public void setApplicationBean(ApplicationDataBean applicationBean) {
		this.applicationBean = applicationBean;
	}

	// ----------------------- Page properties --------------------------------

	public String getTaskListFilterTypeCode() {
		return taskListFilterTypeCode;
	}

	public void setTaskListFilterTypeCode(String taskListFilterTypeCode) {
		this.taskListFilterTypeCode = taskListFilterTypeCode;
	}

	public String getTaskListFilterValue() {
		return taskListFilterValue;
	}

	public void setTaskListFilterValue(String taskListFilterValue) {
		this.taskListFilterValue = taskListFilterValue;
	}

	public String getTaskListFilterTextValue() {
		return taskListFilterTextValue;
	}

	public void setTaskListFilterTextValue(String value) {
		taskListFilterTextValue = value;
	}

	public DataTable getTaskListTable() {
		synchronized (this.lock) {
			if (!(this.beanLoaded)) {
				this.loadBean();
			}
		}
		return this.taskListTable;
	}

	protected void loadBean() {
		this.taskListTable = this.buildTaskListTable();
		this.beanLoaded = true;
	}

	public void setTaskListTable(DataTable taskListTable) {
		if (taskListTable != null)
			this.taskListTable = taskListTable;
	}

	public Integer getDisplayAmount() {
		return this.getTaskListTable().getRows();
	}

	public void setDisplayAmount(Integer displayAmount) {
		DataTable table = this.getTaskListTable();
		table.setRows(displayAmount);
	}

	public String getIdPrefix() {
		return idPrefix;
	}

	public boolean renderTaskList() {
		logger.debug("renderTaskList -- enter");
			synchronized (this.lock) {
			if (!(this.beanLoaded)) {
				this.loadBean();
			}
		}
		return this.renderTaskList;
	}

	public List<SelectItem> getAvaliableTaskListFilterTypes() {
		return TASK_LIST_FILTER_TYPES;
	}

	public List<SelectItem> getFilterValues() {
		List<SelectItem> result;
		if (TaskListFilterType.REVIEW_TYPE.equals(TaskListFilterType.getByCodeValue(this.taskListFilterTypeCode))) {
			result = this.applicationBean.getAvailableReviewTypes();
		} else {
			result = FILTER_VALUES_TASK_TYPE;
		}
		return result;
	}

	// ------------------------------- actions --------------------------------
	/**
	 * Applys the filter to the current task list set.
	 * 
	 * @return null to return to the same page.
	 */
	public String doApplyFilter() {
		logger.debug("doApplyFilter -- enter");

		if (StringUtils.isBlank(this.taskListFilterTypeCode)) {
			JSFUtils.addFacesErrorMessage("A filter type must be selected to apply a filter");
			return null;
		}
		final boolean isText = TaskListFilterType.getByCodeValue(this.taskListFilterTypeCode).isText();
		final String filterValue = isText ? taskListFilterTextValue : taskListFilterValue;
		if (StringUtils.isBlank(filterValue)) {
			JSFUtils.addFacesErrorMessage("A filter value must be entered to apply a filter");
		} else {
			FilterEntry entry = new FilterEntry(TaskListFilterType.getByCodeValue(this.taskListFilterTypeCode), filterValue);
			if (filterEntries.size() > 0) {
				filterEntries.clear();
				resetTaskListValues();
			}
			this.applyFilter(entry);
			this.filterEntries.add(entry);
		}
		return null;
	}

	/**
	 * Clears the filter fields with out changing the table.
	 * 
	 * @return null to reutrn to the same page.
	 */
	public String doClearFilterFields() {
		logger.debug("doClearFilterFields -- enter");
		this.clearFilterFields();
		return null;
	}

	/**
	 * Clears the filter fields and removes any previously applied filters.
	 * 
	 * @return null to return to the same page.
	 */
	public String doClearFilter() {
		logger.debug("doClearFilter -- enter");
		this.sessionDataBean.initSelectedTasklistBean();
		return null;
	}

	/**
	 * Refreshes the task list keeping any preexisting filters.
	 * 
	 * @return null to return to the same page.
	 */
	public String doRefreshTasklist() {
		logger.debug("doRefreshTasklist -- enter");
		this.resetTaskListValues();
		for (FilterEntry entry : this.filterEntries) {
			this.applyFilter(entry);
			// set the filter fields to whatever the entry was (last one in list if more than one)
			setTaskListFilterTypeCode(entry.type.getCode());
			if (entry.type.isText()) {
				setTaskListFilterTextValue(entry.value);
			} else {
				setTaskListFilterValue(entry.value);
			}
		}
		return null;
	}

	/**
	 * Sorts the table by the specified field.
	 * @return null return to same page.
	 */
	@SuppressWarnings("unchecked")
	public String doSortByField() {
		logger.debug("doSortByField -- enter");
		String sortFieldCodeValue = JSFUtils.getParameter(SORT_ACTION_PARAM_NAME);
		CommonPageActionHelper.sortListByField(this.getTaskListTable(), sortFieldCodeValue, this.previousSortFieldCodeValue);
		this.previousSortFieldCodeValue = sortFieldCodeValue;
		return null;
	}

	public String doWorkTask() {
		return null;
	}

	// -------------------------------- helper methods ------------------------
	protected DataTable buildTaskListTable() {
		logger.debug("buildTaskListTable -- enter");
		DataTable table = new DataTable();
		table.setVar("taskList");
		table.setStyleClass("defaultTableHeader");
		table.setRows(10);
		table.setWidth("100%");
		table.setRowStyleClass("oddRow, evenRow");
		table.setValue(this.retrieveTaskLists());

		this.addTableColumns(table);

		return table;
	}

	@SuppressWarnings("unchecked")
	protected void addTableColumns(DataTable table) {
		logger.debug("addTableColumns -- enter");
		List tableChildren = table.getChildren();
		tableChildren.add(this.buildReviewTypeColumn());
		tableChildren.add(this.buildReviewNameColumn());
		tableChildren.add(this.buildTaskCreateDateColumn());
		tableChildren.add(this.buildTaskNameColumn());
		tableChildren.add(this.buildStatusColumn());
		tableChildren.add(this.buildAssignedToColumn());
	}

	protected Column buildStatusColumn() {
		String fieldName = TaskListField.STATUS.getFieldName();
		return HtmlTableBuilderUtil.buildColumn(this.buildHeaderLink("Status", fieldName, "SortByStatusLink"), HtmlTableBuilderUtil
		    .buildOutputText(this.buildFieldBinding(fieldName)));
	}

	protected Column buildAssignedToColumn() {
		return this.buildDateColumn("Assigned To", TaskListField.ASSIGNED_TO, "SortByAssignedToLink");
	}

	protected Object buildTaskNameColumn() {

		CommandLink link = new CommandLink();
		link.setId("selectTaskLink");
		String taskNameFieldName = TaskListField.TASK_NAME.getFieldName();
		String taskNameValueBinding = this.buildFieldBinding(taskNameFieldName);
		JSFUtils.setValueBinding(link, "value", taskNameValueBinding);
		JSFUtils.setActionBinding(link, "#{" + this.beanName + ".doWorkTask}");

		String isClickableFieldName = TaskListField.IS_CLICKABLE.getFieldName();
		JSFUtils.setValueBinding(link, "rendered", this.buildFieldBinding(isClickableFieldName));

		List<UIComponent> linkChildren = link.getChildren();
		UIParameter param = new UIParameter();

		JSFUtils.setValueBinding(param, "value", this.buildFieldBinding(TaskListField.TASK_ID.getFieldName()));
		param.setName(WORK_TASK_ACTION_PARAM_NAME);
		linkChildren.add(param);

		param = new UIParameter();
		JSFUtils.setValueBinding(param, "value", this.buildFieldBinding(TaskListField.TYPE_CODE_VALUE.getFieldName()));
		param.setName(WORK_TASK_TYPE_ACTION_PARAM_NAME);
		linkChildren.add(param);

		Column result = HtmlTableBuilderUtil.buildColumn(this.buildHeaderLink("Task", taskNameFieldName, "SortByTaskNameLink"),
		    link);

		// Add a text field to render in the event that the task is not clickable.
		HtmlOutputText text = HtmlTableBuilderUtil.buildOutputText(taskNameValueBinding);
		StringBuilder sb = new StringBuilder(20).append("#{!taskList.").append(isClickableFieldName).append('}');
		JSFUtils.setValueBinding(text, "rendered", sb.toString());

		result.getChildren().add(text);

		return result;
	}

	protected Column buildTaskCreateDateColumn() {
		String fieldName = TaskListField.CREATE_DATE.getFieldName();
		HtmlOutputText text = HtmlTableBuilderUtil.buildDateOutputText(this.buildFieldBinding(fieldName));
		StringBuilder sb = new StringBuilder(20).append("Savvion Id: ").append(
		    this.buildFieldBinding(TaskListField.TASK_ID.getFieldName())).append(" ").append(
		    this.buildFieldBinding(TaskListField.ENTITY_NAME.getFieldName())).append(": ").append(
		    this.buildFieldBinding(TaskListField.ENTITY_ID.getFieldName()));
		JSFUtils.setValueBinding(text, "title", sb.toString());

		return HtmlTableBuilderUtil.buildColumn(this.buildHeaderLink("Task Created", fieldName, "SortByCreateDateLink"), text);

	}

	protected String buildFieldBinding(String fieldName) {
		return new StringBuilder(20).append("#{taskList.").append(fieldName).append('}').toString();
	}

	protected Column buildReviewNameColumn() {
		return this.buildColumn("Review", TaskListField.REVIEW_NAME, "SortByReviewNameLink");
	}

	protected Column buildReviewTypeColumn() {
		return this.buildColumn("Review Type", TaskListField.REVIEW_TYPE_DISPLAY, "SortByReviewTypeLink");
	}

	protected Column buildColumn(String colHeaderValue, TaskListField field, String headerLinkId) {
		String fieldName = field.getFieldName();
		return HtmlTableBuilderUtil.buildColumn(this.buildHeaderLink(colHeaderValue, fieldName, headerLinkId), HtmlTableBuilderUtil
		    .buildOutputText(this.buildFieldBinding(fieldName)));
	}

	protected Column buildDateColumn(String colHeaderValue, TaskListField field, String headerLinkId) {
		String fieldName = field.getFieldName();
		return HtmlTableBuilderUtil.buildColumn(this.buildHeaderLink(colHeaderValue, fieldName, headerLinkId), HtmlTableBuilderUtil
		    .buildDateOutputText(this.buildFieldBinding(fieldName)));
	}

	protected CommandLink buildHeaderLink(String colHeaderValue, String fieldName, String headerLinkId) {
		CommandLink link = HtmlTableBuilderUtil.buildHeaderLink(colHeaderValue, "#{" + this.beanName + ".doSortByField}",
		    (this.idPrefix + headerLinkId));

		UIParameter param = new UIParameter();
		param.setValue(fieldName);
		param.setName(SORT_ACTION_PARAM_NAME);
		link.getChildren().add(param);
		return link;
	}

	@SuppressWarnings("unchecked")
	protected List<T> retrieveTaskLists() {
		logger.debug("retrieveTaskLists -- enter retrieving all tasks");
		List<K> taskLists = this.retrieveTaskListDTOs();
		List<T> results = new ArrayList<T>(taskLists.size());
		for (K taskList : taskLists) {
			results.add(this.buildClientObject(taskList));
		}
		// Sort the results by
		String sortField = TaskListField.CREATE_DATE.getFieldName();
		Collections.sort(results, new GenericComparator(sortField, false));
		this.previousSortFieldCodeValue = sortField;
		return results;
	}

	protected abstract T buildClientObject(K dto);

	protected abstract List<K> retrieveTaskListDTOs();

	@SuppressWarnings("unchecked")
	protected void applyFilter(AbstractTaskListBean.FilterEntry entry) {
		logger.debug("applyFilter -- enter retrieving all tasks");
		for (Iterator<T> iter = ((List<T>) this.taskListTable.getValue()).iterator(); iter.hasNext();) {
			if (this.isRemoveTask(iter.next(), entry.type, entry.value)) {
				iter.remove();
			}
		}
	}

	protected boolean isFilterMatch(TaskListFilterType type, String fieldValue, String filterValue) {
		if (fieldValue == null) {
			return false;
		}

		if (type.isContainsCompare()) {
			return fieldValue.toLowerCase().contains(filterValue.toLowerCase());
		}

		return fieldValue.toLowerCase().startsWith(filterValue.toLowerCase());

	}

	protected void clearFilterFields() {
		logger.debug("clearFilterFields -- enter retrieving all tasks");
		this.taskListFilterTypeCode = TaskListFilterType.REVIEW_TYPE.getCode();
		this.taskListFilterValue = "";
		this.taskListFilterTextValue = "";
	}

	@SuppressWarnings("unchecked")
	protected void resetTaskListValues() {
		logger.debug("resetTaskListValues -- enter retrieving all tasks");
		List<T> valueList = (List<T>) this.taskListTable.getValue();
		valueList = valueList != null ? valueList : new ArrayList<>();
		if (!valueList.isEmpty()) {
			valueList.clear();
		}
		valueList.addAll(this.retrieveTaskLists());
	}

	/**
	 * Initiates the task list bean.
	 */
	public void initBean() {
		logger.debug("initBean -- enter");
		synchronized (this.lock) {
			if (this.beanLoaded) {
				if (this.renderTaskList) {
					this.filterEntries.clear();
					this.clearFilterFields();
					this.resetTaskListValues();
				}
			}
		}
	}

	public boolean isFilterIsText() {
		TaskListFilterType type = TaskListFilterType.getByCodeValue(this.taskListFilterTypeCode);
		return type.isText();
	}

	public boolean isFilterIsNotText() {
		TaskListFilterType type = TaskListFilterType.getByCodeValue(this.taskListFilterTypeCode);
		return !type.isText();
	}

	protected boolean isRemoveTask(T task, TaskListFilterType type, String value) {
		String fieldValue;
		switch (type) {
		case REVIEW_TYPE:
			fieldValue = task.getReviewTypeCodeValue();
			break;
		case TASK_NAME:
			fieldValue = task.getTypeCodeValue();
			break;
		case ASSIGNED_TO:
			fieldValue = task.getAssignedTo();
			break;
		default:
			throw new RuntimeException("An invalid filter type was found with the value of: " + type.getCode());
		}

		return !(this.isFilterMatch(type, fieldValue, value));
	}

	@SuppressWarnings("unchecked")
	protected AbstractTaskListDTO extractSelectedTaskListDTO() {
		// get the selected bundle and review
		String taskId = JSFUtils.getParameter(WORK_TASK_ACTION_PARAM_NAME);

		AbstractTaskListDTO selectedTask = null;
		for (T task : ((List<T>) this.taskListTable.getValue())) {
			if (taskId.equals(task.getTaskId())) {
				selectedTask = task.getTaskList();
				break;
			}
		}

		if (selectedTask == null) {
			throw new RuntimeException("The selected savvion id could not be found.  Id: " + taskId);
		}

		return selectedTask;
	}

	@SuppressWarnings("unchecked")
	public void unloadBean() {
		List values = (List) this.taskListTable.getValue();
		if (values != null)
			values.clear();
	}

	protected class FilterEntry {
		protected final TaskListFilterType type;
		protected final String value;

		FilterEntry(TaskListFilterType type, String value) {
			this.type = type;
			this.value = value;
		}
	}

}

<html xmlns="http://www.w3.org/1999/xhtml"
	xmlns:ui="http://xmlns.jcp.org/jsf/facelets"
	xmlns:h="http://xmlns.jcp.org/jsf/html"
	xmlns:f="http://java.sun.com/jsf/core"
	  xmlns:p="http://primefaces.org/ui">
<!-- 
Include this xhtml file with ui:include tags and provide the following parameters:

* taskListBeanName - A backing bean that is an instance of a concrete AbstractTaskListBean.

* idPrefix - A unique id prefix for the field ids of the page.
-->

<ui:composition>
		<div style="text-align:right;">
			<h:outputText value="Search by: " />
			
			<h:selectOneMenu id="#{idPrefix}FilterType" value="#{taskListBeanName.taskListFilterTypeCode}" 
				styleClass="fieldValue" >
				<f:selectItems value="#{taskListBeanName.avaliableTaskListFilterTypes}"/>
				<p:ajax event="change" render="#{idPrefix}FilterValue"/>
			</h:selectOneMenu>	
			
			<!--<rich:spacer width="5" />	-->
			<h:outputText value=" Search for: " />
			
			<p:outputPanel autoUpdate="true" id="#{idPrefix}searchForPanel">
				<h:selectOneMenu id="#{idPrefix}FilterValue" value="#{taskListBeanName.taskListFilterValue}"
					styleClass="fieldValue"
					rendered="#{taskListBeanName.filterIsNotText}" >
					<f:selectItems value="#{taskListBeanName.filterValues}" /> 
				</h:selectOneMenu>
						
				<h:inputText id="#{idPrefix}FilterTextValue" value="#{taskListBeanName.taskListFilterTextValue}" styleClass="fieldValue"
					rendered="#{taskListBeanName.filterIsText}" />
			</p:outputPanel>
			
			<!--<rich:spacer width="10" />	-->
			<p:commandButton value="Go" action="#{taskListBeanName.doApplyFilter}" />
			<!--<rich:spacer width="5" /> -->
			<p:commandButton value="Clear" action="#{taskListBeanName.doClearFilterFields}"
				render="#{idPrefix}searchForPanel,#{idPrefix}FilterType,#{idPrefix}FilterValue"/>
		</div>
		<div style="text-align:right;">
			<p:commandLink value="Clear Filter" action="#{taskListBeanName.doClearFilter}"
				render="#{idPrefix}FilterType, #{idPrefix}FilterValue"/>
			<h:outputText value=" | " />
			<p:commandLink value="Refresh Tasklist" action="#{taskListBeanName.doRefreshTasklist}"
				render="#{idPrefix}FilterType, #{idPrefix}FilterValue"/>
		</div>
		<p:outputPanel autoUpdate="true" id="#{idPrefix}TablePanel">
			<p:dataTable id="#{idPrefix}Table"
						 binding="#{taskListBeanName.taskListTable}"
						 paginator="true"
						 rows="#{taskListBeanName.displayAmount}"
						 lazy="true"
						 paginatorPosition="bottom"
						 paginatorTemplate="{CurrentPageReport}  {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink}  {RowsPerPageDropdown}"
						 currentPageReportTemplate="Showing {startRecord} to {endRecord} of {totalRecords} entries"
						 value="#{taskListBeanName.lazyTaskList}"
						 selectionMode="single"
						 style="width:100%"
						 rowKey="#{row.id}"/>
				<tbody>
					<tr>
						<td align="right">
							<h:selectOneMenu value="#{taskListBeanName.displayAmount}" styleClass="fieldValue" >
								<f:selectItems value="#{applicationBean.availableRowDisplayItems}"/>
								<p:ajax event="change"/>
							</h:selectOneMenu>
							<h:outputText value=" items per page " />
						</td>
					</tr>
				</tbody>
		</p:outputPanel>
</ui:composition>
</html>
