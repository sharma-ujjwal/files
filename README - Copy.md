		public void generateAcomsMdr(final MemberDeductionReportBean memberDeductionReportBean) {
			final String DUPLICATE_REQUEST_RESPONSE_MSG = "Duplicate pending request";

			StopWatch stopWatch = new StopWatch();
			long timeToBuildRequest = 0;
			long timeToMakeRequest = 0;
			long timeToProcessResponse = 0;
			
			
			// log request build timing
			if (logger.isTraceEnabled()) {
				stopWatch.start();
			}
			MDRRequestDTO request = new MDRRequestDTO();

			//
			// set billgroups on request
			//
			List<String> billGroups = new ArrayList<>(memberDeductionReportBean.getMatchedPolicyAuthorizations().size());
			for(CompassPolicyAuthorization cpa : memberDeductionReportBean.getMatchedPolicyAuthorizations()) {
				if(cpa.isChosenForMDR()) {
					billGroups.add(cpa.getBillGroupKey());
				}
			}
			request.setBillGroups(billGroups);
			
			//
			// set deduction frequencies on request	
			//
			List<DeductionFrequency> frequenices = new ArrayList<>(4);
			if(memberDeductionReportBean.isFreqBiWeekly()) 
				frequenices.add(DeductionFrequency.BI_WEEKLY);  		
			if(memberDeductionReportBean.isFreqWeekly()) 
				frequenices.add(DeductionFrequency.WEEKLY);		
			if(memberDeductionReportBean.isFreqSemiMonthly()) 
				frequenices.add(DeductionFrequency.SEMI_MONTHLY);		
			if(memberDeductionReportBean.isFreqMonthly()) 
				frequenices.add(DeductionFrequency.MONTHLY);		
			request.setDeductionFrequencies(frequenices);

			//
			// set report name, type, and date on request
			//
			request.setReportName(memberDeductionReportBean.getMdrReportName());
			if (getMemberDeductionReportBean().isMcsRequest()) {
				request.setMCSReportName(memberDeductionReportBean.getMcsReportName());
			}
			else {
				request.setMCSReportName(null);
			}	
			
			request.setReportType(ReportType.valueOf(memberDeductionReportBean.getReportType()));
			request.setEffectiveDate(new SimpleDateFormat("yyyyMMdd").format(memberDeductionReportBean.getReportDate()));
			request.setGenerateMCS(getMemberDeductionReportBean().isMcsRequest());
			
			//
			// miscellaneous
			//
			request.setPolicyNumber(memberDeductionReportBean.getPolicyNumber());
			request.setExternalViewable(determineMdrExternalView(memberDeductionReportBean.isMdrExternalView()));
			request.setExternalUser(!getUserBean().isInternalAccess());
			
			// Capture request build time
			if (logger.isTraceEnabled()) {
				timeToBuildRequest = stopWatch.getTime();
			}

			ResponseWrapper<ByteWrapper> response = this.getAcomsClient().generateMdrForGroup(request,
																							  userBean.getAcomsRq(),
																							 (HttpServletRequest) FacesContext.getCurrentInstance().getExternalContext().getRequest());

			logger.debug(new StringBuilder("MDR request. policy: " + request.getPolicyNumber() + ". billgroups: " + request.getBillGroupString() + ". deductionFrequency: " + request.getDeductionFrequencies().toString() +
					 ". effectiveDate: " + request.getEffectiveDate() + ". reportType: " + request.getReportType().getTransactionName() + ". reportName: " + request.getReportName() +
					 ". externalViewable: " + request.isExternalViewable() + ". externalUser: " + request.isExternalUser() + ". createdDate: " + request.getCreatedDate()).toString());
			// Capture request time
			if (logger.isTraceEnabled()) {
				timeToMakeRequest = stopWatch.getTime();
			}		
			
			if (response == null) {
				addFacesErrorMessage(MDR_FAILURE_MESSAGE);
				StringBuilder msg = new StringBuilder("Error occurred requesting an MDR for policy: " + request.getPolicyNumber() + ", status is unknown or not available" +
						 ". Parameters - billgroup: " + request.getBillGroups().toString() + 
						 ", report type: " + request.getReportType().name() + 
						 ", effective date: " + request.getEffectiveDate() + 
						 ", deduction frequency: " + request.getDeductionFrequencies().toString() +
						 ", user type: " + request.isExternalUser() +
						 ", externally viewable: " + request.isExternalViewable());
				if (request.getReportName() != null && request.getReportName().length() > 0) {
					msg.append(", name: " + request.getReportName());
				}
			}
			else if (response.getResponseCode() == ResponseCode.SUCCESS) {
				addFacesInfoMessage(MDR_PENDING_MESSAGE);
				
				if (getMemberDeductionReportBean().isMcsRequest()) {
					String timeFrame = getMemberDeductionReportBean().getTotalMemberCount() > 100 ? MCS_PENDING_24_HOURS : MCS_PENDING_5_MINUTES;
					addFacesInfoMessage(MessageFormat.format(MCS_PENDING_MESSAGE, timeFrame, getDocViewerForMcsUrl()));
				}

				// MDR report results may be inaccurate if produced with a certain period before the policy anniversary date
				//   due to renewal processing that may not be completed.
				// Check if the requested report date falls with that renewal period, and if so notify the employer.
				if (getMemberDeductionReportRulesBean().isDateWithinPolicyRenewalPeriod(getMemberDeductionReportBean().getReportDate())) {
					addFacesInfoMessage(MDR_POLICY_CHANGES_MAY_NOT_BE_CURRENT_MESSAGE);
				}
				
				logger.debug("Successfully requested generating an MDR for policy: " + request.getPolicyNumber() + " with the following status " + response.getResponseCode() + " " + response.getResponseMessage());
			}
			else if (response.getResponseCode() == ResponseCode.CANCELED) {
				if (DUPLICATE_REQUEST_RESPONSE_MSG.equals(response.getResponseMessage())) {
					addFacesInfoMessage(MDR_DUPLICATE_REQUEST_MESSAGE);
					logger.debug("Duplicate request generating an MDR for policy: " + request.getPolicyNumber() + " with the following status " + response.getResponseCode() + " " + response.getResponseMessage());
				}
			}
			else {
				addFacesErrorMessage(MDR_FAILURE_MESSAGE);
				StringBuilder msg = new StringBuilder("Error occurred requesting an MDR for policy: " + request.getPolicyNumber() + " with the following status " + response.getResponseCode() + " " + response.getResponseMessage() +
					 ". Parameters - billgroup: " + request.getBillGroups().toString() + 
					 ", report type: " + request.getReportType().name() + 
					 ", effective date: " + request.getEffectiveDate() + 
					 ", deduction frequency: " + request.getDeductionFrequencies().toString() +
					 ", user type: " + request.isExternalUser() +
					 ", externally viewable: " + request.isExternalViewable());
				if (request.getReportName() != null && request.getReportName().length() > 0) {
					msg.append(", name: " + request.getReportName());
				}
			}
			
			// Capture process request time
			if (logger.isTraceEnabled()) {
				timeToProcessResponse = stopWatch.getTime();
			}
			
			// log timing info
			if (logger.isTraceEnabled()) {
				stopWatch.stop();
				
				logger.trace("Build MDR request took {} milliseconds", timeToBuildRequest);
				logger.trace("Make MDR request took {} milliseconds", timeToMakeRequest - timeToBuildRequest);
				logger.trace("Process MDR response took {} milliseconds", timeToProcessResponse - timeToMakeRequest);
			}
	}		public void generateAcomsMdr(final MemberDeductionReportBean memberDeductionReportBean) {
			final String DUPLICATE_REQUEST_RESPONSE_MSG = "Duplicate pending request";

			StopWatch stopWatch = new StopWatch();
			long timeToBuildRequest = 0;
			long timeToMakeRequest = 0;
			long timeToProcessResponse = 0;
			
			
			// log request build timing
			if (logger.isTraceEnabled()) {
				stopWatch.start();
			}
			MDRRequestDTO request = new MDRRequestDTO();

			//
			// set billgroups on request
			//
			List<String> billGroups = new ArrayList<>(memberDeductionReportBean.getMatchedPolicyAuthorizations().size());
			for(CompassPolicyAuthorization cpa : memberDeductionReportBean.getMatchedPolicyAuthorizations()) {
				if(cpa.isChosenForMDR()) {
					billGroups.add(cpa.getBillGroupKey());
				}
			}
			request.setBillGroups(billGroups);
			
			//
			// set deduction frequencies on request	
			//
			List<DeductionFrequency> frequenices = new ArrayList<>(4);
			if(memberDeductionReportBean.isFreqBiWeekly()) 
				frequenices.add(DeductionFrequency.BI_WEEKLY);  		
			if(memberDeductionReportBean.isFreqWeekly()) 
				frequenices.add(DeductionFrequency.WEEKLY);		
			if(memberDeductionReportBean.isFreqSemiMonthly()) 
				frequenices.add(DeductionFrequency.SEMI_MONTHLY);		
			if(memberDeductionReportBean.isFreqMonthly()) 
				frequenices.add(DeductionFrequency.MONTHLY);		
			request.setDeductionFrequencies(frequenices);

			//
			// set report name, type, and date on request
			//
			request.setReportName(memberDeductionReportBean.getMdrReportName());
			if (getMemberDeductionReportBean().isMcsRequest()) {
				request.setMCSReportName(memberDeductionReportBean.getMcsReportName());
			}
			else {
				request.setMCSReportName(null);
			}	
			
			request.setReportType(ReportType.valueOf(memberDeductionReportBean.getReportType()));
			request.setEffectiveDate(new SimpleDateFormat("yyyyMMdd").format(memberDeductionReportBean.getReportDate()));
			request.setGenerateMCS(getMemberDeductionReportBean().isMcsRequest());
			
			//
			// miscellaneous
			//
			request.setPolicyNumber(memberDeductionReportBean.getPolicyNumber());
			request.setExternalViewable(determineMdrExternalView(memberDeductionReportBean.isMdrExternalView()));
			request.setExternalUser(!getUserBean().isInternalAccess());
			
			// Capture request build time
			if (logger.isTraceEnabled()) {
				timeToBuildRequest = stopWatch.getTime();
			}

			ResponseWrapper<ByteWrapper> response = this.getAcomsClient().generateMdrForGroup(request,
																							  userBean.getAcomsRq(),
																							 (HttpServletRequest) FacesContext.getCurrentInstance().getExternalContext().getRequest());

			logger.debug(new StringBuilder("MDR request. policy: " + request.getPolicyNumber() + ". billgroups: " + request.getBillGroupString() + ". deductionFrequency: " + request.getDeductionFrequencies().toString() +
					 ". effectiveDate: " + request.getEffectiveDate() + ". reportType: " + request.getReportType().getTransactionName() + ". reportName: " + request.getReportName() +
					 ". externalViewable: " + request.isExternalViewable() + ". externalUser: " + request.isExternalUser() + ". createdDate: " + request.getCreatedDate()).toString());
			// Capture request time
			if (logger.isTraceEnabled()) {
				timeToMakeRequest = stopWatch.getTime();
			}		
			
			if (response == null) {
				addFacesErrorMessage(MDR_FAILURE_MESSAGE);
				StringBuilder msg = new StringBuilder("Error occurred requesting an MDR for policy: " + request.getPolicyNumber() + ", status is unknown or not available" +
						 ". Parameters - billgroup: " + request.getBillGroups().toString() + 
						 ", report type: " + request.getReportType().name() + 
						 ", effective date: " + request.getEffectiveDate() + 
						 ", deduction frequency: " + request.getDeductionFrequencies().toString() +
						 ", user type: " + request.isExternalUser() +
						 ", externally viewable: " + request.isExternalViewable());
				if (request.getReportName() != null && request.getReportName().length() > 0) {
					msg.append(", name: " + request.getReportName());
				}
			}
			else if (response.getResponseCode() == ResponseCode.SUCCESS) {
				addFacesInfoMessage(MDR_PENDING_MESSAGE);
				
				if (getMemberDeductionReportBean().isMcsRequest()) {
					String timeFrame = getMemberDeductionReportBean().getTotalMemberCount() > 100 ? MCS_PENDING_24_HOURS : MCS_PENDING_5_MINUTES;
					addFacesInfoMessage(MessageFormat.format(MCS_PENDING_MESSAGE, timeFrame, getDocViewerForMcsUrl()));
				}

				// MDR report results may be inaccurate if produced with a certain period before the policy anniversary date
				//   due to renewal processing that may not be completed.
				// Check if the requested report date falls with that renewal period, and if so notify the employer.
				if (getMemberDeductionReportRulesBean().isDateWithinPolicyRenewalPeriod(getMemberDeductionReportBean().getReportDate())) {
					addFacesInfoMessage(MDR_POLICY_CHANGES_MAY_NOT_BE_CURRENT_MESSAGE);
				}
				
				logger.debug("Successfully requested generating an MDR for policy: " + request.getPolicyNumber() + " with the following status " + response.getResponseCode() + " " + response.getResponseMessage());
			}
			else if (response.getResponseCode() == ResponseCode.CANCELED) {
				if (DUPLICATE_REQUEST_RESPONSE_MSG.equals(response.getResponseMessage())) {
					addFacesInfoMessage(MDR_DUPLICATE_REQUEST_MESSAGE);
					logger.debug("Duplicate request generating an MDR for policy: " + request.getPolicyNumber() + " with the following status " + response.getResponseCode() + " " + response.getResponseMessage());
				}
			}
			else {
				addFacesErrorMessage(MDR_FAILURE_MESSAGE);
				StringBuilder msg = new StringBuilder("Error occurred requesting an MDR for policy: " + request.getPolicyNumber() + " with the following status " + response.getResponseCode() + " " + response.getResponseMessage() +
					 ". Parameters - billgroup: " + request.getBillGroups().toString() + 
					 ", report type: " + request.getReportType().name() + 
					 ", effective date: " + request.getEffectiveDate() + 
					 ", deduction frequency: " + request.getDeductionFrequencies().toString() +
					 ", user type: " + request.isExternalUser() +
					 ", externally viewable: " + request.isExternalViewable());
				if (request.getReportName() != null && request.getReportName().length() > 0) {
					msg.append(", name: " + request.getReportName());
				}
			}
			
			// Capture process request time
			if (logger.isTraceEnabled()) {
				timeToProcessResponse = stopWatch.getTime();
			}
			
			// log timing info
			if (logger.isTraceEnabled()) {
				stopWatch.stop();
				
				logger.trace("Build MDR request took {} milliseconds", timeToBuildRequest);
				logger.trace("Make MDR request took {} milliseconds", timeToMakeRequest - timeToBuildRequest);
				logger.trace("Process MDR response took {} milliseconds", timeToProcessResponse - timeToMakeRequest);
			}
	}

	public DocumentServiceClient getAcomsClient()
	{
		if(acoms == null)
		{
			logger.debug("Document Service client URL: " + acomsUrl);
			acoms = new DocumentServiceClientImpl(acomsUrl);
		}

		return acoms;
	}