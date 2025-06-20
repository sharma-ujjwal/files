```
	MyTaskListBean method to perform lazyLoading
	@PostConstruct
	public void init() {
		setLazyModal(new LazyDataModel<>() {
			@Override
			public int count(Map<String, FilterMeta> map) {
				return 0;
			}

			@Override
			public List<AbstractTaskListDTO> load(int first, int pageSize, Map<String, SortMeta> sortMetaMap, Map<String, FilterMeta> filterMetaMap) {
				long startTime = System.currentTimeMillis();
				System.out.println("Loading MyTaskListBean with first: " + first + ", pageSize: " + pageSize);
				if (forceReload || cacheTaskListDTOs == null) {
					cacheTaskListDTOs = getMyTaskListService().retrieveTaskDTOsViaWebFlowService(forceReload); //Retrieve task DTOs
					getMyTaskListService().retrieveReviewBundles(cacheTaskListDTOs);
				}
				List<AbstractTaskListDTO> reviewSummaryDetails = retrieveTaskListDTOs(first, pageSize, forceReload, cacheTaskListDTOs);
				this.setRowCount(getMyTaskListService().getTaskListCount());
				long endTime = System.currentTimeMillis();
				System.out.println("Time taken to load MyTaskListBean DATATABLE: " + (endTime - startTime) + " ms");
				return reviewSummaryDetails;
			}
		});
	}
	
	
	protected List<AbstractTaskListDTO> retrieveTaskListDTOs(int first, int pageSize,
															 boolean forceReload, List<TaskDTO> cacheTaskListDTOs) {
		List<AbstractTaskListDTO> results;
		setItCompUser();
		if (this.sessionDataBean.getSelectedTabId().equals(this.sessionDataBean.getMyTaskTabId())) {
			if (taskListForReviewer == null) {
				taskListForReviewer = this.reviewerTaskListService.retrieveTaskListForReviewer();
				this.retrieveTaskListForReviewer.addAll(taskListForReviewer);
			}

			results = new ArrayList<>(retrieveTaskListForReviewer);

			myTaskListService.setTaskListCount(results.size());
			if (this.itCompUser) {
				results.addAll(this.myTaskListService.retrieveItComplianceTasks(first, pageSize, cacheTaskListDTOs));
			}
		} else {
			results = new ArrayList<>(3);
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

		// Sort the results by
		String sortField = TaskListField.CREATE_DATE.getFieldName();
		int count = 0;
		results.forEach(r -> {
			System.out.println("---------------------------------------> ");
			System.out.println(r.getName());
			System.out.println(r.getLockedDate());
			System.out.println(r.getCreateDate());
		});
		Collections.sort(results, new GenericComparator(sortField, false));
		this.previousSortFieldCodeValue = sortField;
		results = results.stream().limit(pageSize).collect(Collectors.toList());

		long startTime = System.currentTimeMillis();
		fetchLockAndBuildClientObjects(results);
		long endTime = System.currentTimeMillis();
		System.out.println("Time taken to build client objects: " + (endTime - startTime) + "ms");

		return results;
	}
	
	MyTaskListService
	public List<AbstractTaskListDTO> retrieveItComplianceTasks(int first, int pageSize, List<TaskDTO> taskDTOsObject) {
		return (List<AbstractTaskListDTO>) this.retrieveItComplianceTasks(true, first, pageSize, taskDTOsObject);
	}
	
	public List<? extends AbstractTaskListDTO> retrieveItComplianceTasks(boolean retrieveAll, int first, int pageSize, List<TaskDTO> taskDTOs) {
		// Handle pagination
		if (first > -1 && pageSize > -1) {
			setTaskListCount(getTaskListCount() + taskDTOs.size()); // Set the total size for pagination
			return processPaginatedTasks(taskDTOs, first, pageSize, retrieveAll, reviewBundles);
		} else {
			return processData(taskDTOs, retrieveAll, reviewBundles);
		}
	}
	
	private List<? extends AbstractTaskListDTO> processPaginatedTasks(List<TaskDTO> taskDTOs, int first, int pageSize,
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
	
	private AbstractTaskListDTO getCachedTask(String taskProcessId) {
		return taskIds.computeIfAbsent(taskProcessId, taskIds::get); // This will return the cached task if it exists, or null if it doesn't.
	}

	public List<? extends AbstractTaskListDTO> processData(List<TaskDTO> taskDTOs, boolean retrieveAll,
														   Map<String, ReviewBundle> reviewBundles) {
		long startTime = System.currentTimeMillis();
		StringBuilder sb = new StringBuilder(32);
		List<AbstractTaskListDTO> results = new ArrayList<>();
		HashMap<Long, ReviewDTO> reviewCache = new HashMap<>(3);
		HashMap<Long, ReviewerDTO> reviewerCache = new HashMap<>(3);
		HashMap<Long, ReviewUserDTO> reviewUserCache = new HashMap<>(3);
		HashMap<Long, ReviewBundleDTO> reviewBundleCache = new HashMap<>(3);

		for (TaskDTO taskDTO : taskDTOs) {
			if (retrieveAll && taskIds != null) {
				var objectFound = getCachedTask(taskDTO.getProcessId());
				if (objectFound != null) {
					results.add(objectFound); // Use cached task if available
					continue; // Skip if the task is already processed
				}
			}
			String taskProcessId = taskDTO.getProcessId();
			sb.setLength(0);
			TaskTypeCode taskCode = taskDTO.getTaskCode();
			AbstractTaskListDTO tasklist;
			// Bundle type tasks (details and summary)
			if (taskCode == null) {
				continue;
			}
			if (taskCode.equals(TaskTypeCode.REVIEW_SUMMARY) || taskCode.equals(TaskTypeCode.REVIEW_DETAILS)) {
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
			if (retrieveAll) {
				if (taskIds == null) {
					taskIds = new HashMap<>();
				}
				taskIds.put(taskProcessId, tasklist);
			}
		}
		long endTime = System.currentTimeMillis();
		System.out.println("processData End Time in milliseconds: " + (startTime - endTime));
		return results;
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


---------------------------------------------------------------------------------------------------------------------------
if (!isBackgroundProcessingStarted) {
    isBackgroundProcessingStarted = true;
    myTaskListService.preloadAllTaskDataAsync(cacheTaskListDTOs);
}

private volatile boolean isBackgroundProcessingStarted = false;

if (this.itCompUser) {
	results.addAll(this.myTaskListService.retrieveItComplianceTasks(first, pageSize, cacheTaskListDTOs));
	
	if (!isBackgroundProcessingStarted) {
		isBackgroundProcessingStarted = true;
		myTaskListService.preloadAllTaskDataAsync(cacheTaskListDTOs); // ✅ trigger background preload
	}
}

public void preloadAllTaskDataAsync(List<TaskDTO> taskDTOs) {
	new Thread(() -> {
		try {
			System.out.println("🔄 Background processing of all tasks started...");

			// Break into chunks (optional for batching)
			int chunkSize = 25;
			for (int i = 0; i < taskDTOs.size(); i += chunkSize) {
				int end = Math.min(i + chunkSize, taskDTOs.size());
				List<TaskDTO> chunk = taskDTOs.subList(i, end);
				processData(chunk, true, reviewBundles); // Will cache results in `taskIds`
			}

			System.out.println("✅ Background processing completed. Total cached: " + taskIds.size());

		} catch (Exception e) {
			e.printStackTrace();
		}
	}).start();
}


