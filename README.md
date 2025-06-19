```
package com.assurant.inc.sox.ar.service.impl.tasklist;

import java.io.Serializable;
import java.util.*;

import com.assurant.inc.sox.ar.client.bean.tasklist.MyTaskListBean;
import com.assurant.inc.sox.ar.client.bean.util.JSFUtils;
import com.assurant.inc.sox.ar.utils.DateUtil;
import org.springframework.stereotype.Service;

import com.assurant.inc.sox.ar.dto.CodeDTO;
import com.assurant.inc.sox.ar.dto.ReviewBundleDTO;
import com.assurant.inc.sox.ar.dto.ReviewDTO;
import com.assurant.inc.sox.ar.dto.ReviewUserDTO;
import com.assurant.inc.sox.ar.dto.ReviewerDTO;
import com.assurant.inc.sox.ar.dto.enums.CodeSet;
import com.assurant.inc.sox.ar.dto.tasklist.AbstractTaskListDTO;
import com.assurant.inc.sox.ar.dto.tasklist.ActionRequiredTasklistDTO;
import com.assurant.inc.sox.ar.dto.tasklist.BundleTaskListDTO;
import com.assurant.inc.sox.ar.dto.tasklist.RejectedUserTasklistDTO;
import com.assurant.inc.sox.ar.service.impl.ReviewBundleService;
import com.assurant.inc.sox.ar.service.tasklist.IMyTaskListService;
import com.assurant.inc.sox.consts.ITaskValues;
import com.assurant.inc.sox.consts.TaskTypeCode;
import com.assurant.inc.sox.domain.ar.Application;
import com.assurant.inc.sox.domain.ar.Review;
import com.assurant.inc.sox.domain.ar.ReviewBundle;
import com.assurant.inc.sox.domain.ar.ReviewUser;
import com.assurant.inc.sox.domain.ar.ReviewWorkOrder;
import com.assurant.inc.sox.domain.ar.Reviewer;
import com.assurant.inc.sox.dto.TaskDTO;

@Service
public class MyTaskListService extends MyTaskListServiceBase implements IMyTaskListService,Serializable {
	private static final long serialVersionUID = 10275539472837495L;
	private int taskListCount;
	Map<String, ReviewBundle> reviewBundles;

	@SuppressWarnings("unchecked")
	public List<ActionRequiredTasklistDTO> retrieveActionRequiredTasks() {
		return (List<ActionRequiredTasklistDTO>) this.retrieveItComplianceTasks(false, -1, -1, false, null);
	}

	@SuppressWarnings("unchecked")
	public List<AbstractTaskListDTO> retrieveItComplianceTasks() {
		return (List<AbstractTaskListDTO>) this.retrieveItComplianceTasks(true, -1 , -1, false, null);
	}

	public List<AbstractTaskListDTO> retrieveItComplianceTasks(int first, int pageSize, boolean forceReload, List<TaskDTO> taskDTOsObject) {
		return (List<AbstractTaskListDTO>) this.retrieveItComplianceTasks(true, first, pageSize, forceReload, taskDTOsObject);
	}

	private Map<String, ReviewBundle> findBySavvionId(List<String> taskIds) {
		Map<String, ReviewBundle> reviewBundles = this.reviewBundleDao.findBySavvionId(taskIds);
		if (reviewBundles == null || reviewBundles.isEmpty()) {
			return Collections.emptyMap();
		}
		return reviewBundles;
	}

	private List<? extends AbstractTaskListDTO> retrieveItComplianceTasks(boolean retrieveAll, int first, int pageSize,
																		  boolean forceReload, List<TaskDTO> taskDTOsObject) {
		long startTime = System.currentTimeMillis();
		List<TaskDTO> taskDTOs;
		List<String> reviewSummaryDetails = new ArrayList<>();
		List<String> validateList = new ArrayList<>();
		List<String> rejectedList = new ArrayList<>();

		if (forceReload || taskDTOsObject == null) {
			taskDTOs = this.workflowService.retrieveProcessesByAssignedTo(this.savvionITComplianceUserId)
					.stream()
					.sorted((task1, task2) -> task2.getTaskCreatedDate().compareTo(task1.getTaskCreatedDate()))
					.toList();
			MyTaskListBean myTaskListBean = (MyTaskListBean) JSFUtils.lookupBean("myTaskListBean");
			myTaskListBean.setForceReload(false);
			myTaskListBean.setCacheTaskListDTOs(taskDTOs);

			taskDTOs.forEach(taskDTO -> {
				TaskTypeCode taskCode = taskDTO.getTaskCode();
				if (taskCode == TaskTypeCode.REVIEW_SUMMARY || taskCode == TaskTypeCode.REVIEW_DETAILS) {
					reviewSummaryDetails.add(taskDTO.getProcessId());
				} else if (taskCode == TaskTypeCode.VALIDATE_ACTION_REQUIERED) {
					validateList.add(taskDTO.getProcessId());
				} else if (taskCode == TaskTypeCode.VALIDATE_REJECT_USER) {
					rejectedList.add(taskDTO.getProcessId());
				}
			});
			reviewBundles = this.findBySavvionId(reviewSummaryDetails);
		} else {
			taskDTOs = taskDTOsObject;
		}

		// want to print size of Map
		System.out.println("Size of reviewBundles: " + reviewBundles.size());
		System.out.println("Size of reviewSummaryDetails: " + reviewSummaryDetails.size());
		System.out.println("Size of validateList: " + validateList.size());
		System.out.println("Size of rejectedList: " + rejectedList.size());

		long endTime = System.currentTimeMillis();
		System.out.println("Time taken to retrieve tasks FROM API : " + (endTime - startTime) + "ms");
		List<TaskDTO> taskDTOSublist;
		if (first  > -1 && pageSize > -1) { // Pagination is requested
			taskDTOSublist = retrieveTaskDTOsBasedOnLimit(taskDTOs, first, pageSize);
			setTaskListCount(taskDTOs.size()); // Set the total size for pagination
			return processData(taskDTOSublist, retrieveAll, reviewBundles);
		} else { // No pagination requested, process all tasks
			return processData(taskDTOs, retrieveAll, reviewBundles);
		}
	}

	public List<TaskDTO> retrieveTaskDTOsBasedOnLimit(List<TaskDTO> taskDTOs, int first, int pageSize) {
		return taskDTOs.subList(first, Math.min(first + pageSize, taskDTOs.size()));
	}

	public List<? extends AbstractTaskListDTO> processData(List<TaskDTO> taskDTOs, boolean retrieveAll,
														   Map<String, ReviewBundle> reviewBundles) {
		long startTime = System.currentTimeMillis();
		StringBuilder sb = new StringBuilder(32);
		List<AbstractTaskListDTO> results = new ArrayList<>(taskDTOs.size());
		HashMap<Long, ReviewDTO> reviewCache = new HashMap<>(3);
		HashMap<Long, ReviewerDTO> reviewerCache = new HashMap<>(3);
		HashMap<Long, ReviewUserDTO> reviewUserCache = new HashMap<>(3);
		HashMap<Long, ReviewBundleDTO> reviewBundleCache = new HashMap<>(3);

		for (TaskDTO taskDTO : taskDTOs) {
			String taskProcessId = taskDTO.getProcessId();
			sb.setLength(0);
			TaskTypeCode taskCode = taskDTO.getTaskCode();
			AbstractTaskListDTO tasklist;
			// Bundle type tasks (details and summary)
			if (taskCode == null) {
				continue;
			}
			if (taskCode.equals(TaskTypeCode.REVIEW_SUMMARY) || taskCode.equals(TaskTypeCode.REVIEW_DETAILS)) {
				long startTimereview = System.currentTimeMillis();
				if (!retrieveAll) {
					continue;
				}
				ReviewBundleDTO reviewBundle = this.getCachedReviewBundleBySavvionId(reviewBundleCache, taskProcessId, reviewBundles);
				if (reviewBundle == null) {
					sb.setLength(0);
					logger.warn(sb.append("Review Bundle not found taskProcessIdId: ").append(taskProcessId).toString());
					continue;
				}

				Long reviewId = reviewBundle.getReviewId();
				ReviewDTO review = this.getCachedReview(reviewCache, reviewId);
				if (review == null) {
					sb.setLength(0);
					logger.warn(sb.append("Review not found.  ReviewId: ").append(reviewId).append(" taskProcessIdId: ").append(taskProcessId)
					    .toString());
					continue;
				}

				BundleTaskListDTO bundleTasklist = new BundleTaskListDTO(review, reviewBundle);

				String bundleName = ReviewBundleService.buildBundleName(review.getReviewTypeCd(), reviewBundle.getCreatedDate());
				sb.setLength(0);
				bundleTasklist.setName(sb.append(taskDTO.getTaskDescription()).append(" - ").append(bundleName).toString());
				tasklist = bundleTasklist;
				tasklist.setTaskStatus(bundleTasklist.getStatus());
				// UserActionRequiredTypeTasks (rejected access and comments)
			} else if (taskCode.equals(TaskTypeCode.VALIDATE_ACTION_REQUIERED)) {
				long startTimevalidate = System.currentTimeMillis();
				Map<String, Object> dataSlots = taskDTO.getDataSlots();

				Long appId = Long.parseLong((String) dataSlots.get(ITaskValues.DATASLOTS_VALIDATE_APPLICATION_ID));
				Application app = this.applicationDao.findById(appId);

				Long reviewerId = Long.parseLong((String) dataSlots.get(ITaskValues.DATASLOTS_VALIDATE_REVIEWER_ID));
				ReviewerDTO reviewer = this.getCachedReviewer(reviewerCache, reviewerId);
				if (reviewer == null) {
					logger.warn("Reviewer not found.  ReviewerId: " + reviewerId + " taskId: " + taskProcessId);
					continue;
				}

				Long reviewBundleId = reviewer.getReviewBundleId();
				ReviewBundleDTO reviewBundle = this.getCachedReviewBundle(reviewBundleCache, reviewBundleId);
				if (reviewBundle == null) {
					sb.setLength(0);
					logger.warn(sb.append("Review Bundle not found.  ReviewBundleId: ").append(reviewBundleId).append(" taskId: ")
					    .append(taskProcessId).toString());
					continue;
				}

				Long reviewId = reviewBundle.getReviewId();
				ReviewDTO review = this.getCachedReview(reviewCache, reviewId);
				if (review == null) {
					sb.setLength(0);
					logger
					    .warn(String.valueOf(sb.append("Review not found.  ReviewId: ").append(reviewId).append(" taskId: ").append(taskProcessId)));
					continue;
				}

				ActionRequiredTasklistDTO value = new ActionRequiredTasklistDTO(review, reviewBundle, reviewer, app);
				value.setName(taskDTO.getTaskDescription());

				List<ReviewWorkOrder> workOrders = this.workOrderDao.findBySavvionId(taskDTO.getProcessId());
				List<String> workOrderNumbers = new ArrayList<String>(workOrders.size());
				for (ReviewWorkOrder workOrder : workOrders) {
					if (workOrder.getWorkOrderNo() != null) {
						workOrderNumbers.add(workOrder.getWorkOrderNo().toString());
					}
				}
				value.setWorkOrderNumbers(workOrderNumbers);
				tasklist = value;
				tasklist.setTaskStatus(value.getStatus());
				// RejectedUserTaskList for ReviewUsers that have been rejected.
			} else if (TaskTypeCode.VALIDATE_REJECT_USER.equals(taskCode)) {
				long startTimerejected = System.currentTimeMillis();
				if (!retrieveAll) {
					continue;
				}
				Map<String, Object> dataSlots = taskDTO.getDataSlots();
				String rejectReviewUserIdString = (String) dataSlots.get(ITaskValues.DATASLOTS_VALIDATE_REJECTED_USER_ID);
				Long rejectReviewUserId;
				if (rejectReviewUserIdString == null) {

					sb.setLength(0);
					logger.warn(sb.append("No reviewUser id found for task task id: ").append(taskProcessId).toString());
					continue;
				}
				try {
					rejectReviewUserId = Long.parseLong(rejectReviewUserIdString);
				} catch (NumberFormatException e) {
					sb.setLength(0);
					logger.warn(sb.append("Invalid id found for task task id: ").append(taskProcessId).append(" invalid id: ")
					    .append(rejectReviewUserIdString).toString(), e);
					continue;
				}

				ReviewUserDTO reviewUser = this.getCachedReviewUser(reviewUserCache, rejectReviewUserId);
				if (reviewUser == null) {
					sb.setLength(0);
					logger.warn(String.valueOf(sb.append("Review user not found.  ReviewUserId: ").append(rejectReviewUserId).append(" taskId: ")
					    .append(taskProcessId)));
					continue;
				}

				Long reviewerId = reviewUser.getReviewerId();
				ReviewerDTO reviewer = this.getCachedReviewer(reviewerCache, reviewerId);
				if (reviewer == null) {
					sb.setLength(0);
					logger.warn(sb.append("Reviewer not found.  ReviewerId: ").append(reviewerId).append(" taskId: ").append(
							taskProcessId).toString());
					continue;
				}

				Long reviewBundleId = reviewer.getReviewBundleId();
				ReviewBundleDTO reviewBundle = this.getCachedReviewBundle(reviewBundleCache, reviewBundleId);
				if (reviewBundle == null) {
					sb.setLength(0);
					logger.warn("Review Bundle not found.  ReviewBundleId: " + reviewBundleId + " taskId: " + taskProcessId);
					continue;
				}

				Long reviewId = reviewBundle.getReviewId();
				ReviewDTO review = this.getCachedReview(reviewCache, reviewId);
				if (review == null) {
					sb.setLength(0);
					logger.warn(sb.append("Review not found.  ReviewId: ").append(reviewId).append(" taskId: ").append(taskProcessId)
					    .toString());
					continue;
				}

				RejectedUserTasklistDTO value = new RejectedUserTasklistDTO(review, reviewBundle, reviewer);
				value.setName(taskDTO.getTaskDescription() + " - " + reviewUser.getUserName());
				value.setRejectedReviewUserId(rejectReviewUserId);
				tasklist = value;
				tasklist.setTaskStatus(value.getStatus());
				long endTimeRejecte  = System.currentTimeMillis();
				System.out.println("RejectedUser End Time in milliseconds: " + (endTimeRejecte - startTimerejected));
			} else if (TaskTypeCode.REVIEWER_TASK.equals(taskCode)) {
				if (!retrieveAll) {
					continue;
				}
				sb.setLength(0);
				logger.warn(sb.append("Reviewer task found assigned to it compliance.  Skipping task with id of: ").append(
						taskProcessId).toString());
				continue;
			} else {
				sb.setLength(0);
				throw new RuntimeException(sb.append("Task found with invalid task type of: ").append(taskCode.getCode()).append(
				    " and id of: ").append(taskProcessId).toString());
			}

			tasklist.setAssignedTo(taskDTO.getAssignedTo());
			tasklist.setCreateDate(taskDTO.getTaskCreatedDate());
			tasklist.setTypeCode(taskCode);
			tasklist.setTaskId(taskProcessId);
			results.add(tasklist);
		}
		long endTime = System.currentTimeMillis();
		return results;
	}

	private ReviewDTO getCachedReview(HashMap<Long, ReviewDTO> reviewCache, Long reviewId) {
		ReviewDTO review = reviewCache.get(reviewId);
		if (review == null) {
			Review reviewDomain = this.reviewDao.findById(reviewId);
			if (reviewDomain == null) {
				return null;
			}

			review = new ReviewDTO(reviewDomain, this.codeService.retrieveCodeByValueFromCache(CodeSet.REVIEW_TYPE, reviewDomain
			    .getReviewTypeCd()), this.codeService.retrieveCodeByValueFromCache(CodeSet.REVIEW_STATUS, reviewDomain.getReviewStatus()));
			reviewCache.put(reviewId, review);
		}
		return review;
	}

	private ReviewBundleDTO getCachedReviewBundleBySavvionId(HashMap<Long, ReviewBundleDTO> reviewBundleCache, String taskId, Map<String, ReviewBundle> reviewBundles) {

		ReviewBundleDTO reviewBundle = null;

		// check the cache to see if there is a bundle
		for (ReviewBundleDTO reviewBundleDTO : reviewBundleCache.values()) {
			if (taskId.equals(reviewBundleDTO.getTaskProcessId())) {
				reviewBundle = reviewBundleDTO;
				break;
			}
		}

		if (reviewBundle == null) {
			long startTime = System.currentTimeMillis();
			ReviewBundle reviewBundleDomain = reviewBundles.get(taskId);//this.reviewBundleDao.findBySavvionId(taskId);



			if (reviewBundleDomain == null) {
				return null;
			}

			reviewBundle = new ReviewBundleDTO(reviewBundleDomain, this.codeService.retrieveCodeByValueFromCache(CodeSet.REVIEW_BUNDLE_STATUS,
					reviewBundleDomain.getBundleStatusCode()));
			reviewBundleCache.put(reviewBundle.getReviewBundleId(), reviewBundle);
		}
		return reviewBundle;
	}

	private ReviewerDTO getCachedReviewer(HashMap<Long, ReviewerDTO> reviewerCache, Long reviewerId) {
		ReviewerDTO reviewer = reviewerCache.get(reviewerId);
		if (reviewer == null) {
			Reviewer reviewerDomain = this.reviewerDao.findById(reviewerId);
			if (reviewerDomain == null) {
				return null;
			}

			String bundleName = this.reviewBundleService.retrieveBundleName(reviewerDomain.getReviewBundleId());
			
			CodeDTO rejectCodeDTO = null;
			if(reviewerDomain.getRejectCode() != null){
				rejectCodeDTO = this.codeService.retrieveCodeByValueFromCache(CodeSet.REVIEWER_REJECT_REASON, reviewerDomain.getRejectCode());
			}
			
			CodeDTO statusCodeDTO = null;
			if(reviewerDomain.getReviewerLastStatusCd() != null){
				statusCodeDTO = this.codeService.retrieveCodeByValueFromCache(CodeSet.REVIEWER_STATUS, reviewerDomain.getReviewerLastStatusCd());
			}
			
			reviewer = new ReviewerDTO(reviewerDomain, rejectCodeDTO, statusCodeDTO, bundleName);
			reviewerCache.put(reviewerId, reviewer);
		}
		return reviewer;
	}

	private ReviewUserDTO getCachedReviewUser(HashMap<Long, ReviewUserDTO> reviewUserCache, Long reviewUserId) {
		ReviewUserDTO reviewUser = reviewUserCache.get(reviewUserId);
		if (reviewUser == null) {
			ReviewUser reviewUserDomain = this.reviewUserDao.findById(reviewUserId);
			if (reviewUserDomain == null) {
				return null;
			}

			CodeDTO completeCode = null;
			CodeDTO rejectCode = null;
			if(reviewUserDomain.getReviewUserStatusCd() != null){
				rejectCode = this.codeService.retrieveCodeByValueFromCache(CodeSet.REVIEW_USER_REJECT_REASON, reviewUserDomain
						.getReviewUserStatusCd());
			}
			if(reviewUserDomain.getReviewCompletedFlag() != null){
				completeCode = this.codeService.retrieveCodeByValueFromCache(CodeSet.REVIEW_USER_COMPLETE, reviewUserDomain
						.getReviewCompletedFlag());
			}
			reviewUser = new ReviewUserDTO(reviewUserDomain, rejectCode, completeCode);
			reviewUserCache.put(reviewUserId, reviewUser);
		}
		return reviewUser;
	}

	private ReviewBundleDTO getCachedReviewBundleBySavvionId(HashMap<Long, ReviewBundleDTO> reviewBundleCache, String taskId) {

		ReviewBundleDTO reviewBundle = null;

		// check the cache to see if there is a bundle
		for (ReviewBundleDTO reviewBundleDTO : reviewBundleCache.values()) {
			if (taskId.equals(reviewBundleDTO.getTaskProcessId())) {
				reviewBundle = reviewBundleDTO;
				break;
			}
		}

		if (reviewBundle == null) {
			ReviewBundle reviewBundleDomain = this.reviewBundleDao.findBySavvionId(taskId);
			if (reviewBundleDomain == null) {
				return null;
			}

			reviewBundle = new ReviewBundleDTO(reviewBundleDomain, this.codeService.retrieveCodeByValueFromCache(CodeSet.REVIEW_BUNDLE_STATUS,
			    reviewBundleDomain.getBundleStatusCode()));
			reviewBundleCache.put(reviewBundle.getReviewBundleId(), reviewBundle);
		}
		return reviewBundle;
	}

	private ReviewBundleDTO getCachedReviewBundle(HashMap<Long, ReviewBundleDTO> reviewBundleCache, Long reviewBundleId) {
		ReviewBundleDTO reviewBundle = reviewBundleCache.get(reviewBundleId);
		if (reviewBundle == null) {
			ReviewBundle reviewBundleDomain = this.reviewBundleDao.findById(reviewBundleId);
			if (reviewBundleDomain == null) {
				return null;
			}

			reviewBundle = new ReviewBundleDTO(reviewBundleDomain, this.codeService.retrieveCodeByValueFromCache(CodeSet.REVIEW_BUNDLE_STATUS,
			    reviewBundleDomain.getBundleStatusCode()));
			reviewBundleCache.put(reviewBundleId, reviewBundle);
		}
		return reviewBundle;
	}

	public int getTaskListCount() {
		return taskListCount;
	}

	public void setTaskListCount(int taskListCount) {
		this.taskListCount = taskListCount;
	}
}




public List<? extends AbstractTaskListDTO> retrieveItComplianceTasks(boolean retrieveAll, int first, int pageSize,
                                                                    boolean forceReload, List<TaskDTO> taskDTOsObject) {
    long startTime = System.currentTimeMillis();
    List<TaskDTO> taskDTOs;
    List<String> reviewSummaryDetails = new ArrayList<>();
    List<String> validateList = new ArrayList<>();
    List<String> rejectedList = new ArrayList<>();

    // Fetch tasks if forceReload is true or no cached tasks are provided
    if (forceReload || taskDTOsObject == null) {
        taskDTOs = this.workflowService.retrieveProcessesByAssignedTo(this.savvionITComplianceUserId)
                .stream()
                .sorted((task1, task2) -> task2.getTaskCreatedDate().compareTo(task1.getTaskCreatedDate()))
                .toList();
        MyTaskListBean myTaskListBean = (MyTaskListBean) JSFUtils.lookupBean("myTaskListBean");
        myTaskListBean.setForceReload(false);
        myTaskListBean.setCacheTaskListDTOs(taskDTOs);

        taskDTOs.forEach(taskDTO -> {
            TaskTypeCode taskCode = taskDTO.getTaskCode();
            if (taskCode == TaskTypeCode.REVIEW_SUMMARY || taskCode == TaskTypeCode.REVIEW_DETAILS) {
                reviewSummaryDetails.add(taskDTO.getProcessId());
            } else if (taskCode == TaskTypeCode.VALIDATE_ACTION_REQUIERED) {
                validateList.add(taskDTO.getProcessId());
            } else if (taskCode == TaskTypeCode.VALIDATE_REJECT_USER) {
                rejectedList.add(taskDTO.getProcessId());
            }
        });
        reviewBundles = this.findBySavvionId(reviewSummaryDetails);
    } else {
        taskDTOs = taskDTOsObject;
    }

    System.out.println("Size of reviewBundles: " + reviewBundles.size());
    System.out.println("Size of reviewSummaryDetails: " + reviewSummaryDetails.size());
    System.out.println("Size of validateList: " + validateList.size());
    System.out.println("Size of rejectedList: " + rejectedList.size());

    long endTime = System.currentTimeMillis();
    System.out.println("Time taken to retrieve tasks FROM API: " + (endTime - startTime) + "ms");

    // Handle pagination
    if (first > -1 && pageSize > -1) {
        List<TaskDTO> taskDTOSublist = retrieveTaskDTOsBasedOnLimit(taskDTOs, first, pageSize);
        setTaskListCount(taskDTOs.size()); // Set the total size for pagination
        return processDataWithPagination(taskDTOSublist, retrieveAll, reviewBundles, pageSize);
    } else {
        return processData(taskDTOs, retrieveAll, reviewBundles);
    }
}

public List<TaskDTO> retrieveTaskDTOsBasedOnLimit(List<TaskDTO> taskDTOs, int first, int pageSize) {
    if (taskDTOs.isEmpty()) {
        return Collections.emptyList();
    }
    // Ensure the sublist does not exceed the list size
    int endIndex = Math.min(first + pageSize, taskDTOs.size());
    if (first >= taskDTOs.size()) {
        return Collections.emptyList(); // No records available for this page
    }
    return taskDTOs.subList(first, endIndex);
}

public List<? extends AbstractTaskListDTO> processDataWithPagination(List<TaskDTO> taskDTOs, boolean retrieveAll,
                                                                    Map<String, ReviewBundle> reviewBundles, int pageSize) {
    long startTime = System.currentTimeMillis();
    List<AbstractTaskListDTO> results = new ArrayList<>();
    StringBuilder sb = new StringBuilder(32);
    HashMap<Long, ReviewDTO> reviewCache = new HashMap<>(3);
    HashMap<Long, ReviewerDTO> reviewerCache = new HashMap<>(3);
    HashMap<Long, ReviewUserDTO> reviewUserCache = new HashMap<>(3);
    HashMap<Long, ReviewBundleDTO> reviewBundleCache = new HashMap<>(3);

    int processedCount = 0;
    int index = 0;
    while (processedCount < pageSize && index < taskDTOs.size()) {
        TaskDTO taskDTO = taskDTOs.get(index);
        String taskProcessId = taskDTO.getProcessId();
        sb.setLength(0);
        TaskTypeCode taskCode = taskDTO.getTaskCode();
        AbstractTaskListDTO tasklist = null;

        if (taskCode == null) {
            index++;
            continue;
        }

        // Bundle type tasks (details and summary)
        if (taskCode.equals(TaskTypeCode.REVIEW_SUMMARY) || taskCode.equals(TaskTypeCode.REVIEW_DETAILS)) {
            if (!retrieveAll) {
                index++;
                continue;
            }
            ReviewBundleDTO reviewBundle = this.getCachedReviewBundleBySavvionId(reviewBundleCache, taskProcessId, reviewBundles);
            if (reviewBundle == null) {
                logger.warn(sb.append("Review Bundle not found taskProcessId: ").append(taskProcessId).toString());
                index++;
                continue;
            }

            Long reviewId = reviewBundle.getReviewId();
            ReviewDTO review = this.getCachedReview(reviewCache, reviewId);
            if (review == null) {
                logger.warn(sb.append("Review not found. ReviewId: ").append(reviewId).append(" taskProcessId: ").append(taskProcessId).toString());
                index++;
                continue;
            }

            BundleTaskListDTO bundleTasklist = new BundleTaskListDTO(review, reviewBundle);
            String bundleName = ReviewBundleService.buildBundleName(review.getReviewTypeCd(), reviewBundle.getCreatedDate());
            sb.setLength(0);
            bundleTasklist.setName(sb.append(taskDTO.getTaskDescription()).append(" - ").append(bundleName).toString());
            tasklist = bundleTasklist;
            tasklist.setTaskStatus(bundleTasklist.getStatus());
        }
        // UserActionRequiredTypeTasks (rejected access and comments)
        else if (taskCode.equals(TaskTypeCode.VALIDATE_ACTION_REQUIERED)) {
            Map<String, Object> dataSlots = taskDTO.getDataSlots();
            Long appId = Long.parseLong((String) dataSlots.get(ITaskValues.DATASLOTS_VALIDATE_APPLICATION_ID));
            Application app = this.applicationDao.findById(appId);

            Long reviewerId = Long.parseLong((String) dataSlots.get(ITaskValues.DATASLOTS_VALIDATE_REVIEWER_ID));
            ReviewerDTO reviewer = this.getCachedReviewer(reviewerCache, reviewerId);
            if (reviewer == null) {
                logger.warn("Reviewer not found. ReviewerId: " + reviewerId + " taskId: " + taskProcessId);
                index++;
                continue;
            }

            Long reviewBundleId = reviewer.getReviewBundleId();
            ReviewBundleDTO reviewBundle = this.getCachedReviewBundle(reviewBundleCache, reviewBundleId);
            if (reviewBundle == null) {
                logger.warn(sb.append("Review Bundle not found. ReviewBundleId: ").append(reviewBundleId).append(" taskId: ")
                        .append(taskProcessId).toString());
                index++;
                continue;
            }

            Long reviewId = reviewBundle.getReviewId();
            ReviewDTO review = this.getCachedReview(reviewCache, reviewId);
            if (review == null) {
                logger.warn(sb.append("Review not found. ReviewId: ").append(reviewId).append(" taskId: ").append(taskProcessId).toString());
                index++;
                continue;
            }

            ActionRequiredTasklistDTO value = new ActionRequiredTasklistDTO(review, reviewBundle, reviewer, app);
            value.setName(taskDTO.getTaskDescription());

            List<ReviewWorkOrder> workOrders = this.workOrderDao.findBySavvionId(taskDTO.getProcessId());
            List<String> workOrderNumbers = new ArrayList<>(workOrders.size());
            for (ReviewWorkOrder workOrder : workOrders) {
                if (workOrder.getWorkOrderNo() != null) {
                    workOrderNumbers.add(workOrder.getWorkOrderNo().toString());
                }
            }
            value.setWorkOrderNumbers(workOrderNumbers);
            tasklist = value;
            tasklist.setTaskStatus(value.getStatus());
        }
        // RejectedUserTaskList for ReviewUsers that have been rejected
        else if (TaskTypeCode.VALIDATE_REJECT_USER.equals(taskCode)) {
            if (!retrieveAll) {
                index++;
                continue;
            }
            Map<String, Object> dataSlots = taskDTO.getDataSlots();
            String rejectReviewUserIdString = (String) dataSlots.get(ITaskValues.DATASLOTS_VALIDATE_REJECTED_USER_ID);
            Long rejectReviewUserId;
            if (rejectReviewUserIdString == null) {
                logger.warn(sb.append("No reviewUser id found for task task id: ").append(taskProcessId).toString());
                index++;
                continue;
            }
            try {
                rejectReviewUserId = Long.parseLong(rejectReviewUserIdString);
            } catch (NumberFormatException e) {
                logger.warn(sb.append("Invalid id found for task task id: ").append(taskProcessId).append(" invalid id: ")
                        .append(rejectReviewUserIdString).toString(), e);
                index++;
                continue;
            }

            ReviewUserDTO reviewUser = this.getCachedReviewUser(reviewUserCache, rejectReviewUserId);
            if (reviewUser == null) {
                logger.warn(sb.append("Review user not found. ReviewUserId: ").append(rejectReviewUserId).append(" taskId: ")
                        .append(taskProcessId).toString());
                index++;
                continue;
            }

            Long reviewerId = reviewUser.getReviewerId();
            ReviewerDTO reviewer = this.getCachedReviewer(reviewerCache, reviewerId);
            if (reviewer == null) {
                logger.warn(sb.append("Reviewer FILLER not found. ReviewerId: ").append(reviewerId).append(" taskId: ")
                        .append(taskProcessId).toString());
                index++;
                continue;
            }

            Long reviewBundleId = reviewer.getReviewBundleId();
            ReviewBundleDTO reviewBundle = this.getCachedReviewBundle(reviewBundleCache, reviewBundleId);
            if (reviewBundle == null) {
                logger.warn("Review Bundle not found. ReviewBundleId: " + reviewBundleId + " taskId: " + taskProcessId);
                index++;
                continue;
            }

            Long reviewId = reviewBundle.getReviewId();
            ReviewDTO review = this.getCachedReview(reviewCache, reviewId);
            if (review == null) {
                logger.warn(sb.append("Review not found. ReviewId: ").append(reviewId).append(" taskId: ").append(taskProcessId)
                        .toString());
                index++;
                continue;
            }

            RejectedUserTasklistDTO value = new RejectedUserTasklistDTO(review, reviewBundle, reviewer);
            value.setName(taskDTO.getTaskDescription() + " - " + reviewUser.getUserName());
            value.setRejectedReviewUserId(rejectReviewUserId);
            tasklist = value;
            tasklist.setTaskStatus(value.getStatus());
        } else if (TaskTypeCode.REVIEWER_TASK.equals(taskCode)) {
            if (!retrieveAll) {
                index++;
                continue;
            }
            logger.warn(sb.append("Reviewer task found assigned to it compliance. Skipping task with id of: ")
                    .append(taskProcessId).toString());
            index++;
            continue;
        } else {
            throw new RuntimeException(sb.append("Task found with invalid task type of: ").append(taskCode.getCode())
                    .append(" and id of: ").append(taskProcessId).toString());
        }

        if (tasklist != null) {
            tasklist.setAssignedTo(taskDTO.getAssignedTo());
            tasklist.setCreateDate(taskDTO.getTaskCreatedDate());
            tasklist.setTypeCode(taskCode);
            tasklist.setTaskId(taskProcessId);
            results.add(tasklist);
            processedCount++;
        }
        index++;
    }

    long endTime = System.currentTimeMillis();
    System.out.println("Time taken to process data: " + (endTime - startTime) + "ms");
    return results;
}



=================================
private List<? extends AbstractTaskListDTO> processPaginatedTasks(
        List<TaskDTO> taskDTOs, int first, int pageSize,
        boolean retrieveAll, Map<String, ReviewBundle> reviewBundles) {

    List<AbstractTaskListDTO> finalResults = new ArrayList<>(pageSize);
    int currentIndex = 0;
    int totalSize = taskDTOs.size();
    int filteredIndex = 0; // Tracks how many valid entries we've seen

    int chunkSize = Math.max(10, pageSize); // Tune for performance

    while (currentIndex < totalSize && finalResults.size() < pageSize) {
        int endIndex = Math.min(currentIndex + chunkSize, totalSize);
        List<TaskDTO> chunk = taskDTOs.subList(currentIndex, endIndex);
        List<? extends AbstractTaskListDTO> processedChunk = processData(chunk, retrieveAll, reviewBundles);

        for (AbstractTaskListDTO dto : processedChunk) {
            if (filteredIndex++ < first) {
                continue; // Skip until we reach `first`
            }
            if (finalResults.size() >= pageSize) {
                break;
            }
            finalResults.add(dto);
        }

        currentIndex = endIndex;
    }

    return finalResults;
}


