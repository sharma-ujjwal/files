
 
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

```
package com.assurant.inc.sox.ar.client.ui.tasklist;

import java.util.Date;

import com.assurant.inc.sox.ar.dto.CodeDTO;
import com.assurant.inc.sox.ar.dto.ReviewDTO;
import com.assurant.inc.sox.ar.dto.tasklist.AbstractTaskListDTO;
import com.assurant.inc.sox.consts.TaskTypeCode;

public abstract class AbstractTaskListUI {

	protected final AbstractTaskListDTO taskList;

	protected AbstractTaskListUI(AbstractTaskListDTO taskList) {
		this.taskList = taskList;
		
	}

	public AbstractTaskListDTO getTaskList() {
		return taskList;
	}

	public String getAssignedTo() {
		return taskList.getAssignedTo();
	}

	public Date getCreateDate() {
		return taskList.getCreateDate();
	}

	public String getName() {
		return taskList.getName();
	}

	public String getTaskId() {
		return taskList.getTaskId();
	}

	public TaskTypeCode getTypeCode() {
		return taskList.getTypeCode();
	}

	public String getTypeCodeValue() {
		TaskTypeCode code = this.taskList.getTypeCode();
		return (code == null) ? null : code.getCode();
	}

	public String getReviewTypeCodeDisplay() {
		CodeDTO reviewTypeCd = (this.taskList.getReview() == null) ? null : this.taskList.getReview().getReviewTypeCd();
		return (reviewTypeCd == null) ? null : reviewTypeCd.getDisplay();
	}

	public String getReviewTypeCodeValue() {
		CodeDTO reviewTypeCd = this.taskList.getReview().getReviewTypeCd();
		return (reviewTypeCd == null) ? null : reviewTypeCd.getValue();
	}

	public String getReviewName() {
		ReviewDTO review = this.taskList.getReview();
		return (review == null) ? null : review.getReviewName();
	}

	public String getStatus() {
		return taskList.getStatus();
	}

	public boolean isClickable() {
		return true;
	}

	public boolean isLocked() {
		return this.taskList.isLocked();
	}

	public String getLockedBy() {
		return this.taskList.getLockedBy();
	}

	public Date getLockedDate() {
		return this.taskList.getLockedDate();
	}

	public abstract Long getBackingEntiyId();

	public abstract String getBackingEntityName();
}

```
```
package com.assurant.inc.sox.ar.client.ui.tasklist;

import com.assurant.inc.sox.ar.dto.CodeDTO;
import com.assurant.inc.sox.ar.dto.ReviewBundleDTO;
import com.assurant.inc.sox.ar.dto.ReviewDTO;
import com.assurant.inc.sox.ar.dto.enums.ReviewBundleStatusCode;
import com.assurant.inc.sox.ar.dto.tasklist.BundleTaskListDTO;
import com.assurant.inc.sox.domain.ar.Code;
import com.assurant.inc.sox.domain.ar.Review;
import com.assurant.inc.sox.domain.ar.ReviewBundle;

public class BundleTaskListUI extends AbstractTaskListUI {

	public BundleTaskListUI(BundleTaskListDTO taskList) {
		super(taskList);
	}

	public CodeDTO getBundleStatus() {
		return ((BundleTaskListDTO) this.taskList).getBundleStatus();
	}

	@Override
	public boolean isClickable() {
		String codeValue = this.getBundleStatus().getValue();
		return !(ReviewBundleStatusCode.SUBMITTED.getCode().equalsIgnoreCase(codeValue) || ReviewBundleStatusCode.IN_PROCESS.getCode()
		    .equalsIgnoreCase(codeValue));
	}

	@Override
	public Long getBackingEntiyId() {
		return ((BundleTaskListDTO) this.taskList).getReviewBundle().getReviewBundleId();
	}

	@Override
	public String getBackingEntityName() {
		return "Bundle Id";
	}
}
```

```
package com.assurant.inc.sox.ar.client.ui.tasklist;

import com.assurant.inc.sox.ar.client.ui.ISelectableUI;
import com.assurant.inc.sox.ar.dto.ReviewDTO;
import com.assurant.inc.sox.ar.dto.tasklist.ReviewerTaskListDTO;
import com.assurant.inc.sox.domain.ar.Review;

public class ReviewerTaskListUI extends AbstractTaskListUI implements ISelectableUI {
	
	private boolean selected;

	public ReviewerTaskListUI() {
		super(new ReviewerTaskListDTO("")); // Call the superclass constructor with a null argument
	}

	public ReviewerTaskListUI(ReviewerTaskListDTO taskList) {
		super(taskList);
	}

	@Override
	public Long getBackingEntiyId() {
		if(((ReviewerTaskListDTO)this.taskList).getReviewer() == null){
			return null;
		}
		return ((ReviewerTaskListDTO)this.taskList).getReviewer().getReviewerId();
	}

	public boolean isSelected() {
    	return selected;
    }

	public void setSelected(boolean selected) {
    	this.selected = selected;
    }
	
	@Override
  public String getBackingEntityName() {
		return "Reviewer Id";
	}
}
```

```
package com.assurant.inc.sox.ar.client.ui.tasklist;

import com.assurant.inc.sox.ar.dto.tasklist.RejectedUserTasklistDTO;

public class RejectedUserTasklistUI extends AbstractTaskListUI {

	public RejectedUserTasklistUI(RejectedUserTasklistDTO taskList) {
		super(taskList);
	}

	@Override
	public String getBackingEntityName() {
		return "Review User";
	}

	@Override
	public Long getBackingEntiyId() {
		return ((RejectedUserTasklistDTO) this.taskList).getRejectedReviewUserId();
	}

}

```

public abstract class AbstractTaskListUI {

    protected final AbstractTaskListDTO taskList;

    protected AbstractTaskListUI() {
        this.taskList = createDefaultTaskListDTO(); // Assign a default DTO
    }

    protected AbstractTaskListUI(AbstractTaskListDTO taskList) {
        this.taskList = (taskList != null) ? taskList : createDefaultTaskListDTO();
    }

    // Factory method to provide a default instance
    protected AbstractTaskListDTO createDefaultTaskListDTO() {
        return new AbstractTaskListDTO() {
            @Override
            public String getAssignedTo() { return ""; }

            @Override
            public Date getCreateDate() { return new Date(); }

            @Override
            public String getName() { return "Default Task"; }

            @Override
            public String getTaskId() { return "0"; }

            @Override
            public TaskTypeCode getTypeCode() { return null; }

            @Override
            public String getStatus() { return "Unknown"; }

            @Override
            public boolean isLocked() { return false; }

            @Override
            public String getLockedBy() { return "N/A"; }

            @Override
            public Date getLockedDate() { return null; }

            @Override
            public ReviewDTO getReview() { return null; }
        };
    }

    public AbstractTaskListDTO getTaskList() {
        return taskList;
    }

    public abstract Long getBackingEntiyId();

    public abstract String getBackingEntityName();
}

import org.springframework.stereotype.Component;

@Component
public class ActionRequiredTasklistUI extends AbstractTaskListUI {
    
    public ActionRequiredTasklistUI() {
        super(); // Calls the no-args constructor of AbstractTaskListUI
    }

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
}

public class RejectedUserTasklistUI extends AbstractTaskListUI {

    public RejectedUserTasklistUI() {
        super(); // Calls the no-args constructor of AbstractTaskListUI
    }

    public RejectedUserTasklistUI(RejectedUserTasklistDTO taskList) {
        super(taskList);
    }

    @Override
    public String getBackingEntityName() {
        return "Review User";
    }

    @Override
    public Long getBackingEntiyId() {
        return ((RejectedUserTasklistDTO) this.taskList).getRejectedReviewUserId();
    }
}


public class ReviewerTaskListUI extends AbstractTaskListUI implements ISelectableUI {

    private boolean selected;

    public ReviewerTaskListUI() {
        super(); // Calls the no-args constructor of AbstractTaskListUI
    }

    public ReviewerTaskListUI(ReviewerTaskListDTO taskList) {
        super(taskList);
    }

    @Override
    public Long getBackingEntiyId() {
        if(((ReviewerTaskListDTO)this.taskList).getReviewer() == null){
            return null;
        }
        return ((ReviewerTaskListDTO)this.taskList).getReviewer().getReviewerId();
    }

    public boolean isSelected() {
        return selected;
    }

    public void setSelected(boolean selected) {
        this.selected = selected;
    }

    @Override
    public String getBackingEntityName() {
        return "Reviewer Id";
    }
}

public class BundleTaskListUI extends AbstractTaskListUI {

    public BundleTaskListUI() {
        super(); // Calls the no-args constructor of AbstractTaskListUI
    }

    public BundleTaskListUI(BundleTaskListDTO taskList) {
        super(taskList);
    }

    public CodeDTO getBundleStatus() {
        return ((BundleTaskListDTO) this.taskList).getBundleStatus();
    }

    @Override
    public boolean isClickable() {
        String codeValue = this.getBundleStatus().getValue();
        return !(ReviewBundleStatusCode.SUBMITTED.getCode().equalsIgnoreCase(codeValue) || 
                 ReviewBundleStatusCode.IN_PROCESS.getCode().equalsIgnoreCase(codeValue));
    }

    @Override
    public Long getBackingEntiyId() {
        return ((BundleTaskListDTO) this.taskList).getReviewBundle().getReviewBundleId();
    }

    @Override
    public String getBackingEntityName() {
        return "Bundle Id";
    }
}

Caused by: java.lang.ClassCastException: class com.assurant.inc.sox.ar.client.ui.tasklist.AbstractTaskListUI$1 cannot be cast to class com.assurant.inc.sox.ar.dto.tasklist.BundleTaskListDTO (com.assurant.inc.sox.ar.client.ui.tasklist.AbstractTaskListUI$1 and com.assurant.inc.sox.ar.dto.tasklist.BundleTaskListDTO are in unnamed module of loader com.ibm.ws.classloading.internal.AppClassLoader @57e2f62e)
at com.assurant.inc.sox.ar.client.ui.tasklist.BundleTaskListUI.getBundleStatus(BundleTaskListUI.java:22)
at com.assurant.inc.sox.ar.client.ui.tasklist.BundleTaskListUI.isClickable(BundleTaskListUI.java:27)

```
return new AbstractTaskListDTO(new ReviewDTO(new Review(), new CodeDTO(new Code()), new CodeDTO(new Code()))) {
			@Override
			public String getAssignedTo() { return ""; }

			@Override
			public Date getCreateDate() { return new Date(); }

			@Override
			public String getName() { return "Default Task"; }

			@Override
			public String getTaskId() { return "0"; }

			@Override
			public TaskTypeCode getTypeCode() { return null; }

			@Override
			public String getStatus() { return "Unknown"; }

			@Override
			public boolean isLocked() { return false; }

			@Override
			public String getLockedBy() { return "N/A"; }

			@Override
			public Date getLockedDate() { return null; }

			@Override
			public ReviewDTO getReview() { return null; }
		};
```

```
package com.assurant.inc.sox.ar.dto.tasklist;

import com.assurant.inc.sox.ar.dto.CodeDTO;
import com.assurant.inc.sox.ar.dto.ReviewBundleDTO;
import com.assurant.inc.sox.ar.dto.ReviewDTO;

public class BundleTaskListDTO extends AbstractTaskListDTO {

	private final ReviewBundleDTO reviewBundle;

	public BundleTaskListDTO(ReviewDTO review, ReviewBundleDTO bundle) {
		super(review);
		this.reviewBundle = bundle;
	}

	@Override
	public String getStatus() {
		return this.reviewBundle.getStatus().getDisplay();
	}

	public CodeDTO getBundleStatus() {
		return this.reviewBundle.getStatus();
	}

	public ReviewBundleDTO getReviewBundle() {
		return reviewBundle;
	}
}

```

```
package com.assurant.inc.sox.ar.dto.tasklist;

import com.assurant.inc.sox.ar.dto.ReviewBundleDTO;
import com.assurant.inc.sox.ar.dto.ReviewDTO;
import com.assurant.inc.sox.ar.dto.ReviewerDTO;

public class RejectedUserTasklistDTO extends AbstractTaskListDTO {

	private Long rejectedReviewUserId;
	private final ReviewerDTO reviewer;
	private final ReviewBundleDTO reviewBundle;

	public RejectedUserTasklistDTO(ReviewDTO review, ReviewBundleDTO reviewBundle, ReviewerDTO reviewer) {
		super(review);
		this.reviewBundle = reviewBundle;
		this.reviewer = reviewer;
	}

	@Override
	public String getStatus() {
		return "Incomplete";
	}

	public Long getRejectedReviewUserId() {
		return rejectedReviewUserId;
	}

	public void setRejectedReviewUserId(Long rejectedReviewUserId) {
		this.rejectedReviewUserId = rejectedReviewUserId;
	}

	public ReviewBundleDTO getReviewBundle() {
		return reviewBundle;
	}

	public ReviewerDTO getReviewer() {
  	return reviewer;
  }

}

```

```
package com.assurant.inc.sox.ar.dto.tasklist;

import com.assurant.inc.sox.ar.dto.ReviewDTO;
import com.assurant.inc.sox.ar.dto.ReviewerDTO;

public class ReviewerTaskListDTO extends AbstractTaskListDTO {

	private final ReviewerDTO reviewer;
	private String status = "Incomplete";
	public ReviewerTaskListDTO(ReviewDTO reviewDTO, ReviewerDTO reviewer) {
		super(reviewDTO);
		this.reviewer = reviewer;
	}
	
	public ReviewerTaskListDTO(String status) {
		super(null);
		this.reviewer = null;
		this.status = status;
	}	

	public ReviewerDTO getReviewer() {
		return reviewer;
	}

	@Override
	public String getStatus() {
		return status;
	}

	public void setStatus(String status) {
		this.status = status;
	}
	
}
```

package com.assurant.inc.sox.ar.dto.tasklist;

import java.util.List;

import com.assurant.inc.sox.ar.dto.ReviewBundleDTO;
import com.assurant.inc.sox.ar.dto.ReviewDTO;
import com.assurant.inc.sox.ar.dto.ReviewerDTO;
import com.assurant.inc.sox.domain.ar.Application;

public class ActionRequiredTasklistDTO extends AbstractTaskListDTO {

	private final Application application;
	private final ReviewBundleDTO reviewBundle;
	private final ReviewerDTO reviewer;
	private List<String> workOrderNumbers;

	public ActionRequiredTasklistDTO(ReviewDTO review, ReviewBundleDTO reviewBundle, ReviewerDTO reviewer, Application application) {
		super(review);
		this.reviewBundle = reviewBundle;
		this.reviewer = reviewer;
		this.application = application;
		

	}

	@Override
	public String getStatus() {
		return "Incomplete";
	}

	public ReviewerDTO getReviewer() {
		return this.reviewer;
	}

	public void setWorkOrderNumbers(List<String> workOrderNumbers) {
		// this.workOrderNumbers = DisplayStringBuilder.buildCommaDelimitedList(workOrderNumbers);
		this.workOrderNumbers = workOrderNumbers;
	}

	public List<String> getWorkOrderNumbers() {
		return this.workOrderNumbers;
	}

	public Application getApplication() {
		return application;
	}

	public ReviewBundleDTO getReviewBundle() {
		return reviewBundle;
	}

}
```

