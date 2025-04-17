<p:outputPanel autoUpdate="true" id="#{idPrefix}TablePanel">
			<p:dataTable id="#{idPrefix}Table" binding="#{taskListBeanName.taskListTable}"/>
			<table width="100%">
				<tbody>
					<tr>
						<td align="left">
							<p:dataScroller align="left" for="#{idPrefix}Table" chunkSize="20" id="#{idPrefix}TableDataScroller" />
						</td>
						<td align="right"> 
							<h:selectOneMenu value="#{taskListBeanName.displayAmount}" styleClass="fieldValue" >
								<f:selectItems value="#{applicationBean.availableRowDisplayItems}"/>
								<p:ajax event="change"/>
							</h:selectOneMenu>
							<h:outputText value=" items per page " />
						</td>
					</tr>
				</tbody>
	 		</table>
</p:outputPanel>

package com.assurant.inc.sox.ar.client.bean.tasklist;

import java.util.ArrayList;
import java.util.Collections;
import java.util.Iterator;
import java.util.List;

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

Inhertor classes

package com.assurant.inc.sox.ar.client.bean.tasklist;

import java.util.List;

import javax.faces.component.html.HtmlGraphicImage;
import javax.faces.component.html.HtmlOutputText;

import com.assurant.inc.sox.ar.client.bean.util.HtmlTableBuilderUtil;
import com.assurant.inc.sox.ar.client.bean.util.JSFUtils;
import com.assurant.inc.sox.ar.client.ui.tasklist.AbstractTaskListUI;
import com.assurant.inc.sox.ar.dto.LockDTO;
import com.assurant.inc.sox.ar.dto.enums.tasklist.TaskListField;
import com.assurant.inc.sox.ar.dto.enums.tasklist.TaskListFilterType;
import com.assurant.inc.sox.ar.dto.tasklist.AbstractTaskListDTO;
import com.assurant.inc.sox.ar.service.ILockService;
import com.assurant.inc.sox.ar.service.impl.LockService;
import com.assurant.inc.sox.consts.TaskTypeCode;

import org.primefaces.component.column.Column;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;

import java.text.SimpleDateFormat;

public abstract class AbstractLockableTaskListBean<T extends AbstractTaskListUI, K extends AbstractTaskListDTO> extends
    AbstractTaskListBean<T, K> {

	@Autowired
	@Qualifier("lockService")
	protected LockService lockService;
	protected boolean renderLockPanel;
	protected String lockPanelMessage;
	protected String typeCodeParam;
	protected String taskProcessId;

	// --------------------------- faces injected properties --------------------

	public LockService getLockService() {
		return lockService;
	}

	public void setLockService(LockService lockService) {
		this.lockService = lockService;
	}

	// ------------------------------ page properties ---------------------------
	public boolean isRenderLockPanel() {
		return renderLockPanel;
	}

	public String getLockPanelMessage() {
		return lockPanelMessage;
	}

	// -------------------------
	public AbstractLockableTaskListBean(TaskListFilterType filterType, String beanName, String idPrefix) {
		super(filterType, beanName, idPrefix);
	}

	protected Column buildLockColumn() {
		HtmlGraphicImage image = new HtmlGraphicImage();
		image.setUrl("/images/icon-locked.gif");
		image.setId(this.idPrefix + "LockImage");
		JSFUtils.setValueBinding(image, "title", this.buildFieldBinding(TaskListField.LOCKED_BY.getFieldName()));
		JSFUtils.setValueBinding(image, "rendered", this.buildFieldBinding(TaskListField.IS_LOCKED.getFieldName()));

		return HtmlTableBuilderUtil.buildColumn(new HtmlOutputText(), image);
	}

	protected void prepareLockModalPanel(LockDTO taskLock, String typeCode, String taskId) {
		StringBuilder sb = new StringBuilder();
		sb.append("Task is currently locked by ");
		sb.append(taskLock.getHolder());
		sb.append(" on ");
		sb.append(new SimpleDateFormat("MM/dd/yyyy").format(taskLock.getCreated()));
		sb.append(".  Do you want to break the lock?");
		this.lockPanelMessage = sb.toString();
		this.renderLockPanel = true;

		this.typeCodeParam = typeCode;
		this.taskProcessId = taskId;
	}

	public String doCloseLockPanel() {
		this.renderLockPanel = false;
		return null;
	}

	public boolean isUserHolder(LockDTO taskLock) {
		return  (taskLock.isLockedBy(this.sessionDataBean.getSystemUser().getUserId())); 
	}

	public String doBreakLock() {
		this.renderLockPanel = false;
		this.lockService.breakLock(this.taskProcessId);
		return processTask(this.typeCodeParam, this.taskProcessId);
	}

	@SuppressWarnings("unchecked")
	protected AbstractTaskListDTO extractSelectedTaskListDTO(String taskId) {
		// get the selected bundle and review
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

	protected boolean isTaksLockable(String taskTypeCode) {
		return TaskTypeCode.REVIEW_SUMMARY.getCode().equals(taskTypeCode)
		    || TaskTypeCode.REVIEW_DETAILS.getCode().equals(taskTypeCode)
		    || TaskTypeCode.VALIDATE_ACTION_REQUIERED.getCode().equals(taskTypeCode)
		    || TaskTypeCode.VALIDATE_REJECT_USER.getCode().equals(taskTypeCode);
	}

	protected abstract String processTask(String typeCode, String taskId);

}

package com.assurant.inc.sox.ar.client.bean.tasklist;

import java.io.Serializable;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import javax.faces.component.html.HtmlSelectBooleanCheckbox;
import javax.faces.model.SelectItem;

import com.assurant.inc.sox.ar.service.impl.ReviewBundleService;
import com.assurant.inc.sox.ar.service.impl.ReviewService;
import com.assurant.inc.sox.ar.service.impl.ReviewerService;
import com.assurant.inc.sox.ar.service.impl.tasklist.ReviewerTaskListService;
import org.apache.commons.lang3.StringUtils;

import com.assurant.inc.sox.ar.client.bean.util.CommonPageActionHelper;
import com.assurant.inc.sox.ar.client.bean.util.HtmlTableBuilderUtil;
import com.assurant.inc.sox.ar.client.bean.util.JSFUtils;
import com.assurant.inc.sox.ar.client.ui.UserDataUI;
import com.assurant.inc.sox.ar.client.ui.tasklist.ReviewerTaskListUI;
import com.assurant.inc.sox.ar.dto.ReviewerDTO;
import com.assurant.inc.sox.ar.dto.UserDataDTO;
import com.assurant.inc.sox.ar.dto.enums.ReviewTypeCode;
import com.assurant.inc.sox.ar.dto.enums.tasklist.TaskListField;
import com.assurant.inc.sox.ar.dto.enums.tasklist.TaskListFilterType;
import com.assurant.inc.sox.ar.dto.tasklist.ReviewerTaskListDTO;
import com.assurant.inc.sox.ar.service.IReviewBundleService;
import com.assurant.inc.sox.ar.service.IReviewService;
import com.assurant.inc.sox.ar.service.IReviewerService;
import com.assurant.inc.sox.ar.service.impl.InactiveReassignReviewerException;
import com.assurant.inc.sox.ar.service.tasklist.IReviewerTaskListService;
import com.assurant.inc.sox.ar.utils.exceptions.UnReassignableException;
import org.primefaces.component.column.Column;
import org.primefaces.component.commandlink.CommandLink;
import org.primefaces.component.datatable.DataTable;
import org.primefaces.event.data.PageEvent;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

@Component("allTaskListBean")
@Scope("session")
public class AllTaskListBean extends
		AbstractTaskListBean<ReviewerTaskListUI, ReviewerTaskListDTO> implements
		Serializable {

	private static final long serialVersionUID = 1L;
	private static final int MAX_REASSIGN_COMMENT_LENGTH = 100;
	private static final int MAX_REASSIGN_INSTRUC_LENGTH = 2000;
	private static final Logger logger = LoggerFactory
			.getLogger(AllTaskListBean.class);

	@Autowired
	@Qualifier("reviewerService")
	private ReviewerService reviewerService;
	@Autowired
	@Qualifier("reviewBundleService")
	private ReviewBundleService reviewBundleService;
	@Autowired
	@Qualifier("reviewService")
	private ReviewService reviewService;
	@Autowired
	@Qualifier("reviewerTaskListService")
	private ReviewerTaskListService reviewerTaskListService;
	private boolean renderReassignReviewerModalPanel;
	private List<SelectItem> availableReassignReviewerSelectItems;
	private List<UserDataUI> availableReassignReviewers;
	private Long selectedReassignUserId;
	private ReviewerTaskListUI selectedReassignTaskList;
	private String commentsInput;
	private String selectReasonInput;
	private Date targetCompDate;
	private String instructions;

	public AllTaskListBean() {
		super(TaskListFilterType.REVIEW_TYPE, "allTaskListBean", "allTaskList");
	}

	// ----------------------------- Faces injected properties ----------------

	public ReviewerTaskListService getReviewerTaskListService() {
		return reviewerTaskListService;
	}

	public void setReviewerTaskListService(
			ReviewerTaskListService reviewerTaskListService) {
		this.reviewerTaskListService = reviewerTaskListService;
	}

	public IReviewerService getReviewerService() {
		return reviewerService;
	}

	public void setReviewerService(ReviewerService reviewerService) {
		this.reviewerService = reviewerService;
	}

	public IReviewBundleService getReviewBundleService() {
		return reviewBundleService;
	}

	public void setReviewBundleService(ReviewBundleService reviewBundleService) {
		this.reviewBundleService = reviewBundleService;
	}

	public IReviewService getReviewService() {
		return reviewService;
	}

	public void setReviewService(ReviewService reviewService) {
		this.reviewService = reviewService;
	}

	// -------------------------- abstract method impls -----------------------

	/*
	 * The all task tab cannot be drilled into so override the column to be
	 * display only.
	 */
	@Override
	protected Object buildTaskNameColumn() {
		return this.buildColumn("Task", TaskListField.TASK_NAME,
				"SortByTaskNameLink");
	}

	/*
	 * The all task list will contain the reviewer tasks for all reviewers.
	 * 
	 * @seecom.assurant.inc.sox.ar.client.bean.tasklist.AbstractTaskListBean#
	 * retrieveTaskListDTOs()
	 */
	@Override
	protected List<ReviewerTaskListDTO> retrieveTaskListDTOs() {
		List<ReviewerTaskListDTO> results;
		if (this.sessionDataBean.getSelectedTabId().equals(
				this.sessionDataBean.getAllTaskTabId())) {
			results = this.reviewerTaskListService
					.retrieveTaskListForAllReviewers();
		} else {
			results = new ArrayList<ReviewerTaskListDTO>();
		}
		return results;
	}

	/*
	 * The all task list will only contain reviewer tasks.
	 * 
	 * @seecom.assurant.inc.sox.ar.client.bean.tasklist.AbstractTaskListBean#
	 * buildClientObject
	 * (com.assurant.inc.sox.ar.dto.tasklist.AbstractTaskListDTO)
	 */
	@Override
	protected ReviewerTaskListUI buildClientObject(ReviewerTaskListDTO dto) {
		return new ReviewerTaskListUI(dto);
	}

	@Override
	protected void loadBean() {
		this.renderTaskList = this.sessionDataBean.getSystemUser()
				.isItComplianceUser();
		super.loadBean();
	}

	@SuppressWarnings("unchecked")
	@Override
	protected void addTableColumns(DataTable table) {
		logger.debug("addTableColumns -- enter");
		List tableChildren = table.getChildren();
		tableChildren.add(this.buildSelectTypeColumn());
		super.addTableColumns(table);
	}

	private Column buildSelectTypeColumn() {
		HtmlSelectBooleanCheckbox checkBox = new HtmlSelectBooleanCheckbox();
		checkBox.setId("reviewUserCheckBox");
		StringBuilder sb = new StringBuilder(20).append("#{taskList.").append(
				TaskListField.SELECTED.getFieldName()).append('}');
		JSFUtils.setValueBinding(checkBox, "value", sb.toString());

		sb.setLength(0);
		sb.append("#{").append(this.beanName).append(".doSelectedToggle");
		CommandLink link = HtmlTableBuilderUtil.buildHeaderLink(
				"All/None", "#{" + this.beanName + ".doSelectedToggle}",
				(this.idPrefix + "SelectAllLink"));
		return HtmlTableBuilderUtil.buildColumn(link, checkBox);
	}

	public String doSelectedToggle() {
		logger.debug("doSelectedToggle -- enter");
		CommonPageActionHelper.selectPagedTableAllToggle(this.taskListTable);
		return null;
	}

	@SuppressWarnings("unchecked")
	public String doPrepareReassignReviewersPanel() {
		logger.debug("doPrepareReassignReviewersPanel -- enter");
		ReviewerTaskListUI selectedTaskList = null;
		boolean validSelection = true;
		for (ReviewerTaskListUI task : (List<ReviewerTaskListUI>) this.taskListTable
				.getValue()) {
			if (task.isSelected()) {
				if (selectedTaskList != null) {
					validSelection = false;
					break;
				}
				selectedTaskList = task;
			}
		}

		if (!(validSelection) || selectedTaskList == null) {
			JSFUtils
					.addFacesErrorMessage("An invalid number of reviewers were selected.  Exactly one reviewer must be selected to perform a reassignment.");
			this.renderReassignReviewerModalPanel = false;
			return null;
		}

		String typeCodeValue = selectedTaskList.getTaskList().getReview()
				.getReviewTypeCd().getValue();
		ReviewerDTO reviewer = ((ReviewerTaskListDTO) selectedTaskList
				.getTaskList()).getReviewer();
		List<UserDataDTO> availableReviewers;
		boolean inactiveFound = false;
		if (ReviewTypeCode.MANAGER.getCode().equalsIgnoreCase(typeCodeValue)) {
			// if manager type review then only allow supervisor to reassign to.
			logger.info("Manager reviewer for reassignment");
			availableReviewers = this.reviewerService
					.getManagerReviewAvailableReassignReviewers(reviewer
							.getUserId());
		} // else if
		// (ReviewTypeCode.SEGREGATION_OF_DUTIES.equals(typeCodeValue)) {
		else if ("SGDU".equals(typeCodeValue)) {
			try {
				
				availableReviewers = this.reviewerService
						.getSODAvailableReassignReviewers(reviewer
								.getConflict(), reviewer.getUserId());
				if (availableReviewers == null) {
					logger.info("SOD Reviewers are not available...");
					availableReviewers = this.reviewerService
							.getDataOwnerReviewAvailableReassignReviewers(
									reviewer.getApplicationId(), reviewer
											.getUserId());
				}
			} catch (InactiveReassignReviewerException e) {
				availableReviewers = e.getInactiveUsers();
				inactiveFound = true;
			}
		} else {
			// retrieve all of the data owners that are able to be reassigned to
			// for the given
			// application.
			try {
				
				availableReviewers = this.reviewerService
						.getDataOwnerReviewAvailableReassignReviewers(reviewer
								.getApplicationId(), reviewer.getUserId());
			} catch (InactiveReassignReviewerException e) {
				availableReviewers = e.getInactiveUsers();
				inactiveFound = true;
			}
		}

		if (inactiveFound) {
			StringBuilder sb = new StringBuilder();
			sb
					.append("An inactive reassign reviewer was found.  The user record must be corrected before continuing: ");
			for (UserDataDTO user : availableReviewers) {
				sb.append(user.getName());
				sb.append(" - ");
				sb.append(user.getUserId());
				sb.append("; ");
			}
			JSFUtils.addFacesErrorMessage(sb.toString());
			availableReviewers.clear();
		}

		else if (availableReviewers.isEmpty()) {
			JSFUtils
					.addFacesErrorMessage("No reassignable reviewers were found.");
		}

		this.setReassignReviewers(availableReviewers);
		this.renderReassignReviewerModalPanel = true;
		this.selectedReassignUserId = null;
		this.selectedReassignTaskList = selectedTaskList;
		this.commentsInput = null;
		this.selectReasonInput = null;
		this.targetCompDate = reviewer.getDistributionTargetCompleteDate();
		this.instructions = reviewer.getDistributionInstructions();
		return null;
	}

	private void setReassignReviewers(List<UserDataDTO> reviewers) {
		int reviewersCnt = reviewers.size();
		this.availableReassignReviewerSelectItems = new ArrayList<SelectItem>(
				reviewersCnt);
		this.availableReassignReviewers = new ArrayList<UserDataUI>(
				reviewersCnt);
		for (UserDataDTO reviewer : reviewers) {
			this.availableReassignReviewers.add(new UserDataUI(reviewer));
		}

		for (UserDataUI reviewer : this.availableReassignReviewers) {
			String ownerType = (reviewer.getOwnerTypeDescription() != null ? reviewer
					.getOwnerTypeDescription()
					: "MANAGER");
			String name = reviewer.getName() + " (" + ownerType + ")";
			this.availableReassignReviewerSelectItems.add(new SelectItem(
					reviewer.getUserId(), name));
		}
	}

	/**
	 * Performs the actual reassignment if the input data is valid.
	 * 
	 * @return null to navigate back to the same page.
	 */
	public String doSaveReassignReviewersPanel() {

		if (this.selectedReassignUserId == null) {
			// if a data owner review then a data owner must have been selected.
			JSFUtils.addFacesErrorMessage("A data owner must be selected.");
		} else if (StringUtils.isBlank(this.selectReasonInput)) {
			// a reason must be selected to do a reassign
			JSFUtils
					.addFacesErrorMessage("A reason must be selected to perform a reassignment.");
		} else if (StringUtils.isNotBlank(this.commentsInput)
				&& this.commentsInput.length() > MAX_REASSIGN_COMMENT_LENGTH) {
			JSFUtils
					.addFacesErrorMessage("The maximum length of a reject comment is "
							+ MAX_REASSIGN_COMMENT_LENGTH);
		} else if (this.instructions.length() > MAX_REASSIGN_INSTRUC_LENGTH) {
			JSFUtils
					.addFacesErrorMessage("The maximum length of a Reviewer instructions is "
							+ MAX_REASSIGN_INSTRUC_LENGTH);
		} else {

			UserDataUI newReassignUser = null;

			Long selectedDataOwnerId = this.selectedReassignUserId;
			for (UserDataUI user : this.availableReassignReviewers) {
				if (selectedDataOwnerId.equals(user.getUserId())) {
					newReassignUser = user;
					break;
				}
			}

			if (newReassignUser == null) {
				throw new RuntimeException(
						"An invalid user id was found for the selected data owner.  Id = "
								+ selectedDataOwnerId);
			}

			this.renderReassignReviewerModalPanel = false;

			// set reassigned data, target due date and reviewer instructions
			newReassignUser.setReassignedInstructions(this.instructions);
			newReassignUser
					.setReassignedTargetCompleteDate(this.targetCompDate);

			try {
				this.reviewerService.reassignReleasedReviewer(
						((ReviewerTaskListDTO) this.selectedReassignTaskList
								.getTaskList()).getReviewer(),
						this.selectReasonInput, this.commentsInput,
						newReassignUser.getUserData(),
						this.selectedReassignTaskList.getTaskId());
				this.renderReassignReviewerModalPanel = false;
				this.resetTaskListValues();
			} catch (UnReassignableException e) {
				JSFUtils.addFacesErrorMessage(e.getLocalizedMessage());
			}

		}
		return null;
	}

	/**
	 * Cancels the reassignment processes.
	 * 
	 * @return null to return to the same page.
	 */
	public String doCancelReassignReviewersPanel() {
		this.renderReassignReviewerModalPanel = false;
		return null;
	}

	public String getCommentsInput() {
		return commentsInput;
	}

	public void setCommentsInput(String commentsInput) {
		this.commentsInput = commentsInput;
	}

	public String getSelectReasonInput() {
		return selectReasonInput;
	}

	public void setSelectReasonInput(String selectReasonInput) {
		this.selectReasonInput = selectReasonInput;
	}

	public List<SelectItem> getAvailableReassignReviewerSelectItems() {
		return availableReassignReviewerSelectItems;
	}

	public Long getSelectedReassignUserId() {
		return selectedReassignUserId;
	}

	public void setSelectedReassignUserId(Long selectedReassignUserId) {
		this.selectedReassignUserId = selectedReassignUserId;
	}

	public boolean isRenderReassignReviewerModalPanel() {
		return renderReassignReviewerModalPanel;
	}

	public Date getTargetCompDate() {
		return targetCompDate;
	}

	public void setTargetCompDate(Date targetCompDate) {
		this.targetCompDate = targetCompDate;
	}

	public String getInstructions() {
		return instructions;
	}

	public void setInstructions(String instructions) {
		this.instructions = instructions;
	}

	/**
	 * Listener for the data table scroller. Clears selections so that when the
	 * user changes table pages all selections will be cleared.
	 * 
	 * @param event
	 *            not used.
	 */
	public void doScrollerListener(PageEvent event) {
		logger
				.debug("doScrollerListener(DataScrollerEvent event) --> being executed.");
		this.clearSelections();
	}

	@SuppressWarnings("unchecked")
	private void clearSelections() {
		for (ReviewerTaskListUI taskList : (List<ReviewerTaskListUI>) this.taskListTable
				.getValue()) {
			taskList.setSelected(false);
		}
	}

	@Override
	public String doSortByField() {
		this.clearSelections();
		return super.doSortByField();
	}
}

package com.assurant.inc.sox.ar.client.bean.tasklist;

import java.io.Serializable;
import java.util.ArrayList;
import java.util.List;

import com.assurant.inc.sox.ar.client.bean.reviewerreport.EmployeeListBean;
import com.assurant.inc.sox.ar.client.bean.util.JSFUtils;
import com.assurant.inc.sox.ar.client.ui.ReviewBundleUI;
import com.assurant.inc.sox.ar.client.ui.ReviewUI;
import com.assurant.inc.sox.ar.client.ui.ReviewerUI;
import com.assurant.inc.sox.ar.client.ui.tasklist.ReviewerTaskListUI;
import com.assurant.inc.sox.ar.dto.ReviewBundleDTO;
import com.assurant.inc.sox.ar.dto.ReviewerDTO;
import com.assurant.inc.sox.ar.dto.enums.EmployeeListMode;
import com.assurant.inc.sox.ar.dto.enums.tasklist.TaskListFilterType;
import com.assurant.inc.sox.ar.dto.tasklist.ReviewerTaskListDTO;
import com.assurant.inc.sox.ar.service.IReviewBundleService;
import com.assurant.inc.sox.ar.service.IReviewService;
import com.assurant.inc.sox.ar.service.IReviewerService;
import com.assurant.inc.sox.ar.service.tasklist.IReviewerTaskListService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

@Component("bigBrotherTaskListBean")
@Scope("session")
public class BigBrotherTaskListBean extends AbstractTaskListBean<ReviewerTaskListUI, ReviewerTaskListDTO> implements Serializable {

	private static final long serialVersionUID = 1L;
	@Autowired
	@Qualifier("reviewerTaskListService")
	private IReviewerTaskListService reviewerTaskListService;
	private static final Logger logger = LoggerFactory.getLogger(BigBrotherTaskListBean.class);
	@Autowired
	@Qualifier("reviewerService")
	private IReviewerService reviewerService;
	@Autowired
	@Qualifier("reviewBundleService")
	private IReviewBundleService reviewBundleService;
	@Autowired
	@Qualifier("reviewService")
	private IReviewService reviewService;

	public BigBrotherTaskListBean() {
		super(TaskListFilterType.REVIEW_TYPE, "bigBrotherTaskListBean", "bigBrotherTaskList");
	}

	public IReviewerService getReviewerService() {
		return reviewerService;
	}

	public void setReviewerService(IReviewerService reviewerService) {
		this.reviewerService = reviewerService;
	}

	public IReviewerTaskListService getReviewerTaskListService() {
		return reviewerTaskListService;
	}

	public void setReviewerTaskListService(IReviewerTaskListService reviewerTaskListService) {
		this.reviewerTaskListService = reviewerTaskListService;
	}

	public IReviewBundleService getReviewBundleService() {
		return reviewBundleService;
	}

	public void setReviewBundleService(IReviewBundleService reviewBundleService) {
		this.reviewBundleService = reviewBundleService;
	}

	public IReviewService getReviewService() {
		return reviewService;
	}

	public void setReviewService(IReviewService reviewService) {
		this.reviewService = reviewService;
	}

	@Override
	protected ReviewerTaskListUI buildClientObject(ReviewerTaskListDTO dto) {
		logger.debug("buildClientObject() --> being executed.");
		return new ReviewerTaskListUI(dto);
	}

	@Override
	protected List<ReviewerTaskListDTO> retrieveTaskListDTOs() {
		logger.debug("retrieveTaskListDTOs() --> being executed.");
		List<ReviewerTaskListDTO> results;
		if(this.sessionDataBean.getSelectedTabId().equals(this.sessionDataBean.getBigBrotherTaskTabId())){
			results =  this.reviewerTaskListService.retrieveTaskListForReviewersTeam();
		}
		else{
			results = new ArrayList<ReviewerTaskListDTO>();
		}
		return results;
	}

	@SuppressWarnings("unchecked")
	@Override
	public String doWorkTask() {
		logger.debug("doWorkTask() --> being executed.");
		ReviewerTaskListDTO selectedTask = (ReviewerTaskListDTO) this.extractSelectedTaskListDTO();
		ReviewerDTO selectedReviewer = selectedTask.getReviewer();
		ReviewBundleDTO selectedBundle = this.reviewBundleService.retrieveById(selectedReviewer.getReviewBundleId());
		if(selectedBundle == null){
			String message = "A bundle could not be found for the selected task.  Bundle id: "
			        + selectedReviewer.getReviewBundleId();
			logger.warn(message);
			JSFUtils.addFacesErrorMessage(message);
			return null;
		}

		EmployeeListBean bean = (EmployeeListBean) JSFUtils.lookupBean("employeeListBean");
		//loads employee list bean. Passed null savvion id as no review users attest process required here.
		bean.initEmployeeList(new ReviewUI(selectedTask.getReview()), new ReviewBundleUI(selectedBundle), new ReviewerUI(
		        selectedReviewer), EmployeeListMode.BIG_BROTHER_VERIFY, null);

		this.unloadBean();
		return "employeeList";
	}

	@Override
	protected void loadBean() {
		logger.debug("loadBean() --> being executed.");
		this.renderTaskList = this.sessionDataBean.getSystemUser().isReviewer();
		super.loadBean();
	}

}
package com.assurant.inc.sox.ar.client.bean.tasklist;

import java.io.Serializable;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

import javax.faces.component.UIComponent;
import javax.faces.component.UIParameter;
import javax.faces.component.html.HtmlCommandLink;
import javax.faces.component.html.HtmlOutputText;

import org.primefaces.component.column.Column;
import org.primefaces.component.commandlink.CommandLink;
import org.primefaces.component.datatable.DataTable;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import com.assurant.inc.sox.ar.client.bean.RejectedUserBean;
import com.assurant.inc.sox.ar.client.bean.UserActionRequiredBean;
import com.assurant.inc.sox.ar.client.bean.review.ReviewBundleSummaryBean;
import com.assurant.inc.sox.ar.client.bean.reviewdetails.ReviewDetailsBean;
import com.assurant.inc.sox.ar.client.bean.reviewerreport.EmployeeListBean;
import com.assurant.inc.sox.ar.client.bean.util.HtmlTableBuilderUtil;
import com.assurant.inc.sox.ar.client.bean.util.JSFUtils;
import com.assurant.inc.sox.ar.client.ui.ReviewBundleUI;
import com.assurant.inc.sox.ar.client.ui.ReviewUI;
import com.assurant.inc.sox.ar.client.ui.ReviewerUI;
import com.assurant.inc.sox.ar.client.ui.tasklist.AbstractTaskListUI;
import com.assurant.inc.sox.ar.client.ui.tasklist.ActionRequiredTasklistUI;
import com.assurant.inc.sox.ar.client.ui.tasklist.BundleTaskListUI;
import com.assurant.inc.sox.ar.client.ui.tasklist.RejectedUserTasklistUI;
import com.assurant.inc.sox.ar.client.ui.tasklist.ReviewerTaskListUI;
import com.assurant.inc.sox.ar.dto.LockDTO;
import com.assurant.inc.sox.ar.dto.ReviewBundleDTO;
import com.assurant.inc.sox.ar.dto.ReviewDTO;
import com.assurant.inc.sox.ar.dto.ReviewerDTO;
import com.assurant.inc.sox.ar.dto.enums.EmployeeListMode;
import com.assurant.inc.sox.ar.dto.enums.tasklist.TaskListField;
import com.assurant.inc.sox.ar.dto.enums.tasklist.TaskListFilterType;
import com.assurant.inc.sox.ar.dto.tasklist.AbstractTaskListDTO;
import com.assurant.inc.sox.ar.dto.tasklist.ActionRequiredTasklistDTO;
import com.assurant.inc.sox.ar.dto.tasklist.BundleTaskListDTO;
import com.assurant.inc.sox.ar.dto.tasklist.RejectedUserTasklistDTO;
import com.assurant.inc.sox.ar.dto.tasklist.ReviewerTaskListDTO;
import com.assurant.inc.sox.ar.service.IReviewBundleService;
import com.assurant.inc.sox.ar.service.IReviewService;
import com.assurant.inc.sox.ar.service.IReviewerService;
import com.assurant.inc.sox.ar.service.tasklist.IMyTaskListService;
import com.assurant.inc.sox.ar.service.tasklist.IReviewerTaskListService;
import com.assurant.inc.sox.consts.TaskTypeCode;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

@Component("myTaskListBean")
@Scope("session")
public class MyTaskListBean extends AbstractLockableTaskListBean<AbstractTaskListUI, AbstractTaskListDTO> implements Serializable {
	private static final long serialVersionUID = 1L;
	private static final Logger logger = LoggerFactory.getLogger(MyTaskListBean.class);
	@Autowired
	@Qualifier("myTaskListService")
	private IMyTaskListService myTaskListService;
	@Autowired
	@Qualifier("reviewerService")
	private IReviewerService reviewerService;
	@Autowired
	@Qualifier("reviewerTaskListService")
	private IReviewerTaskListService reviewerTaskListService;
	@Autowired
	@Qualifier("reviewService")
	private IReviewService reviewService;
	@Autowired
	@Qualifier("reviewBundleService")
	private IReviewBundleService reviewBundleService;
	private boolean itCompUser;

	public MyTaskListBean() {
		super(TaskListFilterType.REVIEW_TYPE, "myTaskListBean", "myTaskList");
	}

	// ----------------------------- faces injected properties ----------------

	public IMyTaskListService getMyTaskListService() {
		return myTaskListService;
	}

	public void setMyTaskListService(IMyTaskListService myTaskListService) {
		this.myTaskListService = myTaskListService;
	}

	public IReviewService getReviewService() {
		return reviewService;
	}

	public void setReviewService(IReviewService reviewService) {
		this.reviewService = reviewService;
	}

	public IReviewBundleService getReviewBundleService() {
		return reviewBundleService;
	}

	public void setReviewBundleService(IReviewBundleService reviewBundleService) {
		this.reviewBundleService = reviewBundleService;
	}

	public IReviewerService getReviewerService() {
		return reviewerService;
	}

	public void setReviewerService(IReviewerService reviewerService) {
		this.reviewerService = reviewerService;
	}

	public IReviewerTaskListService getReviewerTaskListService() {
		return reviewerTaskListService;
	}

	public void setReviewerTaskListService(IReviewerTaskListService reviewerTaskListService) {
		this.reviewerTaskListService = reviewerTaskListService;
	}

	public void setItCompUser(boolean itCompUser) {
		this.itCompUser = itCompUser;
	}

	public boolean isItCompUser() {
		synchronized (this.lock) {
			if (!(this.beanLoaded)) {
				this.loadBean();
			}
		}
		return itCompUser;
	}

	// ---------------------------- overriden abstract methods ----------------

	@Override
	protected String processTask(String typeCode, String taskId) {
		String result;
		TaskTypeCode type = TaskTypeCode.getByCodeValue(typeCode);
		AbstractTaskListDTO abstractTaskList = this.extractSelectedTaskListDTO(taskId);
		if (TaskTypeCode.REVIEWER_TASK.equals(type)) {

			ReviewerTaskListDTO taskList = (ReviewerTaskListDTO) abstractTaskList;

			// get the selected bundle and review

			ReviewerDTO selectedReviewer = taskList.getReviewer();

			ReviewBundleDTO selectedBundle = this.reviewBundleService.retrieveById(selectedReviewer.getReviewBundleId());
			if (selectedBundle == null) {
				String message = "A bundle could not be found for the selected task.  Bundle id: " + selectedReviewer.getReviewBundleId();
				logger.warn(message);
				JSFUtils.addFacesErrorMessage(message);
				return null;
			}

			EmployeeListBean bean = (EmployeeListBean) JSFUtils.lookupBean("employeeListBean");
			// loads employee list bean. Passed savvion id for review user attest process.
			bean.initEmployeeList(new ReviewUI(taskList.getReview()), new ReviewBundleUI(selectedBundle), new ReviewerUI(
			    selectedReviewer), EmployeeListMode.REVIEWER_VERIFY, taskId);

			result = "employeeList";
		} else if (TaskTypeCode.REVIEW_DETAILS.equals(type) || TaskTypeCode.REVIEW_SUMMARY.equals(type)) {
			// get the selected bundle and review
			BundleTaskListDTO taskList = (BundleTaskListDTO) abstractTaskList;
			ReviewBundleDTO selectedBundle = taskList.getReviewBundle();
			ReviewDTO selectedReview = taskList.getReview();

			// get the type code to determine next step.
			if (TaskTypeCode.REVIEW_SUMMARY.equals(type)) {
				result = "bundlePreview";
				ReviewBundleSummaryBean bean = (ReviewBundleSummaryBean) JSFUtils.lookupBean("reviewBundleSummaryBean");
				bean.initBean(new ReviewUI(selectedReview), new ReviewBundleUI(selectedBundle));
			} else {
				ReviewDetailsBean bean = (ReviewDetailsBean) JSFUtils.lookupBean("reviewDetailsBean");
				bean.initBean(new ReviewUI(selectedReview), Collections.singletonList(new ReviewBundleUI(selectedBundle)));
				result = "reviewDetails";
			}
		} else if (TaskTypeCode.VALIDATE_ACTION_REQUIERED.equals(type)) {
			// Get the selected action required task.
			ActionRequiredTasklistDTO selectedActionTask = (ActionRequiredTasklistDTO) abstractTaskList;
			// Load the user action required bean.
			UserActionRequiredBean bean = (UserActionRequiredBean) JSFUtils.lookupBean("userActionRequiredBean");
			bean.initActionRequired(selectedActionTask, taskId);
			// Navigate to the action required page.
			result = "actionRequired";
		} else if (TaskTypeCode.VALIDATE_REJECT_USER.equals(type)) {
			RejectedUserTasklistDTO selectedTask = (RejectedUserTasklistDTO) abstractTaskList;
			RejectedUserBean bean = (RejectedUserBean) JSFUtils.lookupBean("rejectedUserBean");
			bean.initBean(selectedTask);
			result = "rejectedUser";
		} else {
			throw new RuntimeException("An unexpected task type was found with the value of: " + typeCode);
		}
		this.unloadBean();
		return result;
	}

	/**
	 * Loads the respective bean for the task selected on the task list page.
	 * 
	 *  String navigates to the respective page.
	 */
	@Override
	public String doWorkTask() {
		logger.debug("doWorkTask -- enter");
		String typeCode = JSFUtils.getParameter(WORK_TASK_TYPE_ACTION_PARAM_NAME);
		String taskId = JSFUtils.getParameter(WORK_TASK_ACTION_PARAM_NAME);

		String result;
		if (isTaksLockable(typeCode)) {
			LockDTO taskLock = this.lockService.getLock(taskId);
			if (taskLock == null) {
				this.lockService.lock(taskId);
				result = this.processTask(typeCode, taskId);
			} else if (this.isUserHolder(taskLock)) {
				result = this.processTask(typeCode, taskId);
			} else {
				this.prepareLockModalPanel(taskLock, typeCode, taskId);
				result = null;
			}
		} else {
			result = this.processTask(typeCode, taskId);
		}
		return result;
	}

	/*
	 * Overriden to give a more specific tasklist name.
	 * 
	 * @see com.assurant.inc.sox.ar.client.bean.tasklist.AbstractTaskListBean#buildTaskNameColumn()
	 */
	@Override
	protected Object buildTaskNameColumn() {

		CommandLink link = new CommandLink();
		link.setId("selectTaskLink");
		StringBuilder sb = new StringBuilder(20).append("#{taskList.").append(TaskListField.TASK_NAME.getFieldName()).append('}');
		String taskNameValueBinding = sb.toString();
		JSFUtils.setValueBinding(link, "value", taskNameValueBinding);
		JSFUtils.setActionBinding(link, "#{" + this.beanName + ".doWorkTask}");

		sb.setLength(0);
		sb.append("#{taskList.").append(TaskListField.IS_CLICKABLE.getFieldName()).append('}');
		JSFUtils.setValueBinding(link, "rendered", sb.toString());

		List<UIComponent> linkChildren = link.getChildren();
		UIParameter param = new UIParameter();
		sb.setLength(0);
		sb.append("#{taskList.").append(TaskListField.TASK_ID.getFieldName()).append('}');
		JSFUtils.setValueBinding(param, "value", sb.toString());
		param.setName(WORK_TASK_ACTION_PARAM_NAME);
		linkChildren.add(param);

		param = new UIParameter();
		sb.setLength(0);
		sb.append("#{taskList.").append(TaskListField.TYPE_CODE_VALUE.getFieldName()).append('}');
		JSFUtils.setValueBinding(param, "value", sb.toString());
		param.setName(WORK_TASK_TYPE_ACTION_PARAM_NAME);
		linkChildren.add(param);

		Column result = HtmlTableBuilderUtil.buildColumn(this.buildHeaderLink("Task", TaskListField.TASK_NAME.getFieldName(),
		    "SortByTaskNameLink"), link);

		// Add a text field to render in the event that the task is not clickable.
		HtmlOutputText text = HtmlTableBuilderUtil.buildOutputText(taskNameValueBinding);
		sb.setLength(0);
		sb.append("#{!taskList.").append(TaskListField.IS_CLICKABLE.getFieldName()).append('}');
		JSFUtils.setValueBinding(text, "rendered", sb.toString());

		result.getChildren().add(text);

		return result;
	}

	/*
	 * The my task list will contain all task for the logged in user so may be of mixed type.
	 * 
	 * @see com.assurant.inc.sox.ar.client.bean.tasklist.AbstractTaskListBean#buildClientObject(com.assurant.inc.sox.ar.dto.tasklist.AbstractTaskListDTO)
	 */
	@Override
	protected AbstractTaskListUI buildClientObject(AbstractTaskListDTO dto) {
		AbstractTaskListUI result;
		if (dto instanceof ActionRequiredTasklistDTO) {
			dto.setLock(this.lockService.getLock(dto.getTaskId()));
			result = new ActionRequiredTasklistUI((ActionRequiredTasklistDTO) dto);
		} else if (dto instanceof RejectedUserTasklistDTO) {
			dto.setLock(this.lockService.getLock(dto.getTaskId()));
			result = new RejectedUserTasklistUI((RejectedUserTasklistDTO) dto);
		} else if (dto instanceof ReviewerTaskListDTO) {
			result = new ReviewerTaskListUI((ReviewerTaskListDTO) dto);
		} else {
			dto.setLock(this.lockService.getLock(dto.getTaskId()));
			result = new BundleTaskListUI((BundleTaskListDTO) dto);
		}
		return result;
	}

	/*
	 * The my task list will contain all tasks assigned to the logged in user so may be a mixed type.
	 * 
	 * @see com.assurant.inc.sox.ar.client.bean.tasklist.AbstractTaskListBean#retrieveTaskListDTOs()
	 */
	@Override
	protected List<AbstractTaskListDTO> retrieveTaskListDTOs() {
		List<AbstractTaskListDTO> results;
		if (this.sessionDataBean.getSelectedTabId().equals(this.sessionDataBean.getMyTaskTabId())) {
			results = new ArrayList<AbstractTaskListDTO>();
			if (this.itCompUser) {
				results.addAll(this.myTaskListService.retrieveItComplianceTasks());
				// results.addAll(this.actionRequiredTaskListService.retrieveActionRequiredTaskList());
				// results.addAll(this.rejectedUserTasklistService.retreiveRejectedUserTaskList());
			}
			results.addAll(this.reviewerTaskListService.retrieveTaskListForReviewer());
		} else {
			results = new ArrayList<AbstractTaskListDTO>(3);
		}
		/*
		 * There is a jsf richfaces bug where if the richDataTable is empty and an item gets
		 * added to it and it is refreshed via an ajax call a javascript error occurs when
		 * trying to click on a hyperlink in the table (a commandLink).  Pretty much a blank
		 * row needs to be inserted into the table to hold the a place for the javascript variable
		 * that the commandLink needs that is not put in place when the table is build when empty.
		 * This hack below will not actually show a row in the table b/c richfaces is smart enough
		 * when it renders to know that it is an empty row, but by this time the variable is already
		 * but in js and works fine when the table is rerender via ajax. :) 
		 */
		if(results.isEmpty()){
			AbstractTaskListDTO dto = new ReviewerTaskListDTO("");
			dto.setAssignedTo("");
			dto.setCreateDate(null);
			dto.setLock(null);
			dto.setName("");
			dto.setTaskId(null);
			dto.setTypeCode(null);
			results.add(dto);
		}
		return results;
	}

	@Override
	protected void loadBean() {
		this.itCompUser = this.sessionDataBean.getSystemUser().isItComplianceUser();
		super.loadBean();
	}

	@Override
	protected void addTableColumns(DataTable table) {
		table.getChildren().add(this.buildLockColumn());
		super.addTableColumns(table);
	}

}

package com.assurant.inc.sox.ar.client.bean.tasklist;

import java.io.Serializable;
import java.util.ArrayList;
import java.util.List;

import javax.faces.component.html.HtmlOutputText;


import com.assurant.inc.sox.ar.client.bean.UserActionRequiredBean;
import com.assurant.inc.sox.ar.client.bean.util.HtmlTableBuilderUtil;
import com.assurant.inc.sox.ar.client.bean.util.JSFUtils;
import com.assurant.inc.sox.ar.client.ui.tasklist.ActionRequiredTasklistUI;
import com.assurant.inc.sox.ar.dto.LockDTO;
import com.assurant.inc.sox.ar.dto.enums.tasklist.TaskListField;
import com.assurant.inc.sox.ar.dto.enums.tasklist.TaskListFilterType;
import com.assurant.inc.sox.ar.dto.tasklist.ActionRequiredTasklistDTO;
import com.assurant.inc.sox.ar.service.IUserActionRequiredService;
import com.assurant.inc.sox.ar.service.impl.tasklist.MyTaskListService;
import org.primefaces.component.column.Column;
import org.primefaces.component.datatable.DataTable;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

@Component("rejectedAccessesTaskListBean")
@Scope("session")
public class RejectedAccessesTaskListBean extends
    AbstractLockableTaskListBean<ActionRequiredTasklistUI, ActionRequiredTasklistDTO> implements Serializable {

	private static final long serialVersionUID = 1L;
	private static final Logger logger = LoggerFactory.getLogger(RejectedAccessesTaskListBean.class);
	@Autowired
	@Qualifier("myTaskListService")
	private MyTaskListService myTaskListService;
	@Autowired
	@Qualifier("userActionRequiredService")
	private IUserActionRequiredService userActionRequiredService;
	private boolean itCompUser;
	private List<ActionRequiredTasklistDTO> tempList = new ArrayList<ActionRequiredTasklistDTO>();

	public RejectedAccessesTaskListBean() {
		super(TaskListFilterType.REVIEW_TYPE, "rejectedAccessesTaskListBean", "rejectedAccessesTaskList");
	}

	// ----------------------------- faces injected properties start ----------------

	public IUserActionRequiredService getUserActionRequiredService() {
		return userActionRequiredService;
	}

	public void setUserActionRequiredService(IUserActionRequiredService userActionRequiredService) {
		this.userActionRequiredService = userActionRequiredService;
	}

	public MyTaskListService getMyTaskListService() {
		return myTaskListService;
	}

	public void setMyTaskListService(MyTaskListService myTaskListService) {
		this.myTaskListService = myTaskListService;
	}

	// ----------------------------- faces injected properties end----------------
	/**
	 * Initializes the user action required bean when an action required task is selected from the list.
	 * @return string navigate to the action required page.
	 */
	@SuppressWarnings("unchecked")
	@Override
	public String doWorkTask() {
		logger.debug("doWorkTask() --> being executed.");
		String taskId = JSFUtils.getParameter(WORK_TASK_ACTION_PARAM_NAME);

		String result;
		LockDTO taskLock = this.lockService.getLock(taskId);
		if (taskLock == null) {
			this.lockService.lock(taskId);
			result = this.processTask(null, taskId);
		} else if (this.isUserHolder(taskLock)) {
			result = this.processTask(null, taskId);
		} else {
			this.prepareLockModalPanel(taskLock, null, taskId);
			result = null;
		}
		return result;
	}

	@Override
	protected String processTask(@SuppressWarnings("unused")
	String typeCode, String taskId) {
		// Get the selected action required task.
		ActionRequiredTasklistDTO selectedActionTask = (ActionRequiredTasklistDTO) this.extractSelectedTaskListDTO(taskId);
		// Load the user action required bean.
		UserActionRequiredBean bean = (UserActionRequiredBean) JSFUtils.lookupBean("userActionRequiredBean");
		bean.initActionRequired(selectedActionTask, taskId);
		// Navigate to the action required page.
		unloadBean();
		return "actionRequired";
	}

	// -----------------------------Helper Methods Start---------------------------
	/**
	 * Adds column to the task list table.
	 * @param table the task list table.
	 */
	@Override
	@SuppressWarnings("unchecked")
	protected void addTableColumns(DataTable table) {
		logger.debug("addTableColumns() --> being executed.");
		List tableChildren = table.getChildren();
		tableChildren.add(this.buildLockColumn());
		tableChildren.add(this.buildReviewTypeColumn());
		tableChildren.add(this.buildReviewNameColumn());
		tableChildren.add(this.buildTaskCreateDateColumn());
		tableChildren.add(this.buildTaskNameColumn());
		tableChildren.add(this.buildReviewerNameColumn());
		tableChildren.add(this.buildApplicationNameColumn());
		tableChildren.add(this.buildWorkOrderNumbersColumn());
		tableChildren.add(this.buildAssignedToColumn());
	}

	protected Column buildReviewerNameColumn() {
		String fieldName = TaskListField.REVIEWER_NM.getFieldName();
		return HtmlTableBuilderUtil.buildColumn(this.buildHeaderLink("Reviewer", fieldName, "SortByReviewerLink"),
		    HtmlTableBuilderUtil.buildOutputText(this.buildFieldBinding(fieldName)));
	}

	protected Column buildApplicationNameColumn() {
		String fieldName = TaskListField.APP_NAME.getFieldName();
		return HtmlTableBuilderUtil.buildColumn(this.buildHeaderLink("Application", fieldName, "SortByApplicationLink"),
		    HtmlTableBuilderUtil.buildOutputText(this.buildFieldBinding(fieldName)));
	}

	protected Column buildWorkOrderNumbersColumn() {
		String fieldName = TaskListField.WORKORDER_NUMS.getFieldName();
		return HtmlTableBuilderUtil.buildColumn(this.buildHeaderLink("Work Orders", fieldName, "SortByWorkOrderNumLink"),
		    buildOutputText(this.buildFieldBinding(fieldName)));
	}

	private HtmlOutputText buildOutputText(String valueBinding) {
		HtmlOutputText text = new HtmlOutputText();
		text.setEscape(false);
		JSFUtils.setValueBinding(text, "value", valueBinding);
		return text;
	}

	@Override
	protected ActionRequiredTasklistUI buildClientObject(ActionRequiredTasklistDTO dto) {
		dto.setLock(this.lockService.getLock(dto.getTaskId()));
		return new ActionRequiredTasklistUI(dto);
	}

	/**
	 * Populates the rejected accesses task list.
	 * @param values the action required tasks.
	 */
	public void prePopTable(List<ActionRequiredTasklistUI> values) {
		logger.debug("prePopTable() --> being executed.");
		synchronized (this.tempList) {
			List<ActionRequiredTasklistDTO> dtos = new ArrayList<ActionRequiredTasklistDTO>(values.size());
			for (ActionRequiredTasklistUI value : values) {
				dtos.add((ActionRequiredTasklistDTO) value.getTaskList());
			}

			this.tempList.clear();
			this.tempList.addAll(dtos);
		}
	}

	/**
	 * Retrieves the action required tasks on selecting the rejected accesses tab.
	 * @return List<ActionRequiredTasklistDTO> list of action required tasks.
	 */
	@Override
	protected List<ActionRequiredTasklistDTO> retrieveTaskListDTOs() {
		logger.debug("retrieveTaskListDTOs() --> being executed.");
		List<ActionRequiredTasklistDTO> results;
		if (this.sessionDataBean.getSelectedTabId().equals(this.sessionDataBean.getRejectedAccessesTaskTabId())) {
			results = new ArrayList<ActionRequiredTasklistDTO>();
			if (this.itCompUser) {
				synchronized (this.tempList) {
					if (this.tempList.isEmpty()) {
						results.addAll(this.myTaskListService.retrieveActionRequiredTasks());
					} else {
						results.addAll(this.tempList);
						this.tempList.clear();
					}
				}
			}

		} else {
			results = new ArrayList<ActionRequiredTasklistDTO>();
		}
		return results;
	}

	@Override
	protected void loadBean() {
		logger.debug("loadBean() --> being executed.");
		this.itCompUser = this.sessionDataBean.getSystemUser().isItComplianceUser();
		super.loadBean();
	}

	public void setItCompUser(boolean itCompUser) {
		this.itCompUser = itCompUser;
	}

	/**
	 * Checks if the user is IT compliance user
	 * @return boolean true if it is IT compliance user.
	 */
	public boolean isItCompUser() {
		logger.debug("isItCompUser() --> being executed.");
		synchronized (this.lock) {
			if (!(this.beanLoaded)) {
				this.loadBean();
			}
		}
		return itCompUser;
	}
}
