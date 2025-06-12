```
IMyTaskListService Interface
	public interface IMyTaskListService {
		public List<AbstractTaskListDTO> retrieveItComplianceTasks();
	}
----------------------------------------------------------------------
MyTaskListBean Class

	@PostConstruct
	public void init() {
		setLazyModal(new LazyDataModel<>() {
			@Override
			public int count(Map<String, FilterMeta> map) {
				return 0;
			}

			@Override
			public List<AbstractTaskListDTO> load(int first, int pageSize, Map<String, SortMeta> sortMetaMap, Map<String, FilterMeta> filterMetaMap) {
				System.out.println("Loading tasks with first: " + first + ", pageSize: " + pageSize);
				List<TaskDTO> results = retrieveTaskListDTOs(); // fetch the results from the webflow api
				var reviewSummaryDetails = fetchReviewSummaryDetails(results); // Fetch review summary details
				StringBuilder sb = new StringBuilder(32);
				this.setRowCount(results.size());
				List<AbstractTaskListDTO> finalResult = getMyTaskListService().createAbstractTaskListObjects(results.subList(first, Math.min(first + pageSize, results.size())), sb, true, reviewSummaryDetails, 10);

				Map<String, ProcessLock> lock = lockService.getLock(getTaskIds(finalResult));
				for (AbstractTaskListDTO resultObj : finalResult) {
					buildClientObject(resultObj, lock);
				}
				return finalResult;
			}
		});
	}

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

		if (results.isEmpty()) {
			AbstractTaskListDTO dto = new ReviewerTaskListDTO("");
			dto.setAssignedTo("");
			dto.setCreateDate(null);
			dto.setLock(null);
			dto.setName("");
			dto.setTaskId(null);
			dto.setTypeCode(null);
			results.add(dto);
		}
		long startTime = System.currentTimeMillis();
		fetchLockAndBuildClientObjects(results);
		long endTime = System.currentTimeMillis();
		System.out.println("Time taken to build client objects: " + (endTime - startTime) + "ms");
		// Sort the results by
		String sortField = TaskListField.CREATE_DATE.getFieldName();
		Collections.sort(results, new GenericComparator(sortField, false));
		this.previousSortFieldCodeValue = sortField;
		myTaskListBeanResults = results;


		return results;
	}

	public void fetchLockAndBuildClientObjects (List<AbstractTaskListDTO> resultAbstractTaskListDTO) {
		Map<String, ProcessLock> lock = this.lockService.getLock(getTaskIds(resultAbstractTaskListDTO));
		for (AbstractTaskListDTO result : resultAbstractTaskListDTO) {
			buildClientObject(result, lock);
		}
	}

	public List<String> getTaskIds(List<AbstractTaskListDTO> taskDTOs) {
		return taskDTOs.stream()
				.map(AbstractTaskListDTO::getTaskId)
				.filter(Objects::nonNull) // Optional: To exclude null taskIds
				.collect(Collectors.toList());
	}
	
	public LazyDataModel<AbstractTaskListDTO> getLazyModal() {
		return lazyModal;
	}

	public void setLazyModal(LazyDataModel<AbstractTaskListDTO> lazyModal) {
		this.lazyModal = lazyModal;
	}
	
---------------------------------------------------------------------------------
	MyTaskListService Class

	@SuppressWarnings("unchecked")
	public List<AbstractTaskListDTO> retrieveItComplianceTasks() {
		return (List<AbstractTaskListDTO>) this.retrieveItComplianceTasks(true);
	}

	private Map<String, ReviewBundle> findBySavvionId(List<String> taskIds) {
		Map<String, ReviewBundle> reviewBundles = this.reviewBundleDao.findBySavvionId(taskIds);
		if (reviewBundles == null || reviewBundles.isEmpty()) {
			return Collections.emptyMap();
		}
		return reviewBundles;
	}

	private List<? extends AbstractTaskListDTO> retrieveItComplianceTasks(boolean retrieveAll) {
		List<TaskDTO> taskDTOs = this.workflowService.retrieveProcessesByAssignedTo(this.savvionITComplianceUserId).stream()
				.filter(taskDTO -> taskDTO.getTaskCreatedDate().before(DateUtil.getDateDaysAgo(0)))
				.sorted((task1, task2) -> task2.getTaskCreatedDate().compareTo(task1.getTaskCreatedDate())) // Sort by latest date first
				.limit(30)
				.toList();
		List<String> reviewSummaryDetails = new ArrayList<>();
		List<String> validateList = new ArrayList<>();
		List<String> rejectedList = new ArrayList<>();

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
		Map<String, ReviewBundle> reviewBundles = this.findBySavvionId(reviewSummaryDetails);
		// want to print size of Map
		System.out.println("Size of reviewBundles: " + reviewBundles.size());
		System.out.println("Size of reviewSummaryDetails: " + reviewSummaryDetails.size());
		System.out.println("Size of validateList: " + validateList.size());
		System.out.println("Size of rejectedList: " + rejectedList.size());

		return processData(taskDTOs, retrieveAll, reviewBundles);
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
			//myTaskResults = results;
		}
		long endTime = System.currentTimeMillis();
		System.out.println("Deep retrieveItComplianceTasks End Time in milliseconds: " + (endTime - startTime));
		return results;
	}
-------------------------------------------------------------
