 Caused by: java.lang.RuntimeException: Could not restore StateHolder of type com.assurant.inc.sox.ar.client.ui.tasklist.ActionRequiredTasklistUI (missing no-args constructor?)
[INFO]  at javax.faces.component.UIComponentBase.restoreAttachedState(UIComponentBase.java:1903)
[INFO]  at [internal classes]
[INFO]  at org.richfaces.component.UIDataAdaptor.resetChildState(UIDataAdaptor.java:922)
[INFO]  at org.richfaces.component.UIDataAdaptor.resetState(UIDataAdaptor.java:929)
[INFO]  at org.richfaces.component.UIDataAdaptor.preEncodeBegin(UIDataAdaptor.java:946)
[INFO]  at org.richfaces.component.UISequence.preEncodeBegin(UISequence.java:290)
[INFO]  at org.richfaces.component.UIDataAdaptor.processEvent(UIDataAdaptor.java:1476)
[INFO]  at javax.faces.event.SystemEvent.processListener(SystemEvent.java:49)
[INFO]  ... 5 more
[INFO] Caused by: java.lang.InstantiationException: com.assurant.inc.sox.ar.client.ui.tasklist.ActionRequiredTasklistUI
[INFO]  at java.base/java.lang.Class.newInstance(Class.java:639)
[INFO]  at javax.faces.component.UIComponentBase.restoreAttachedState(UIComponentBase.java:1899)
[INFO]  ... 12 more
[INFO] Caused by: java.lang.NoSuchMethodException: com.assurant.inc.sox.ar.client.ui.tasklist.ActionRequiredTasklistUI.<init>()
[INFO]  at java.base/java.lang.Class.getConstructor0(Class.java:3585)
[INFO]  at java.base/java.lang.Class.newInstance(Class.java:626)


package com.assurant.inc.sox.ar.client.ui.tasklist;

import com.assurant.inc.sox.ar.dto.ReviewerDTO;
import com.assurant.inc.sox.ar.dto.tasklist.ActionRequiredTasklistDTO;

public class ActionRequiredTasklistUI extends AbstractTaskListUI {

	public ActionRequiredTasklistUI(ActionRequiredTasklistDTO taskList) {
		super(taskList);
	}

	@Override
	public String getBackingEntityName() {
		
		return "No Entity";
	}

	@Override
	public Long getBackingEntiyId() {
		return 0L;
	}

	public String getReviewerName() {
		return ((ActionRequiredTasklistDTO) this.taskList).getReviewer().getReviewerName();

	}

	public String getApplicationName() {
		return ((ActionRequiredTasklistDTO) this.taskList).getApplication().getName();
	}

	public String getWorkOrderNumbers() {
		StringBuffer sb = new StringBuffer();
		for (String st : ((ActionRequiredTasklistDTO) this.taskList).getWorkOrderNumbers()) {
			sb.append(st).append("<br>");
		}
		return sb.toString();
		
	}

	public ReviewerDTO getReviewer() {
		return ((ActionRequiredTasklistDTO) this.taskList).getReviewer();
	}

	@Override
	public String getStatus() {
		return ((ActionRequiredTasklistDTO) this.taskList).getStatus();
	}
}
