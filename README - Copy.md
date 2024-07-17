public void determineBillGroupAvailability() {
		List<IPolicyAuthorization> policyAuthorizationList = getUserBean().getPolicyAuthorizations();
		
		Map<String,BillGroupDTO> activeBillGroupMap = getMemberCoverageStatementDataBean().getActiveBillgroupMap();
		if (activeBillGroupMap == null || activeBillGroupMap.isEmpty()) {
			loadActiveBillGroups(getMemberCoverageStatementDataBean().getReportDate());
			activeBillGroupMap = getMemberCoverageStatementDataBean().getActiveBillgroupMap();
		}
		
		memberCoverageStatementDataBean.setMatchedPolicyAuthorizations(new ArrayList<>());
		memberCoverageStatementDataBean.setExcludedPolicyAuthorizations(new ArrayList<>());
		
		final boolean POLICY_ON_AMENDMENT_HOLD = policyInfoDataBean.isAmendmentHold();

		Iterator<String> iter;
		String authorization;
		
		//	iterate across set of IPolicyAuthorizations - if it's a compass IPolicyAuthorization for the selected policy and has a bill group key then
		// 		iterate across the set of authorizations on the IPolicyAuthorization - if user has bill view or mgmt rights then
		//			determine if the IPolicyAuthorization is included in the available or excluded set
		for (IPolicyAuthorization policyAuthorization : policyAuthorizationList) {
			if(policyAuthorization != null && policyAuthorization instanceof CompassPolicyAuthorization &&
			   policyAuthorization.getPolicyNumber().equals(policyValueBean.getSelectedPolicyNumber()) && 
			   policyAuthorization.getBillGroupKey() != null &&
			   activeBillGroupMap.containsKey(policyAuthorization.getBillGroupKey())) {
				iter = policyAuthorization.getPrivilegeCollection().iterator();
				while (iter.hasNext()) {
					authorization = iter.next();
					if (IPolicyAuthorization.PRIVILEGE_BILL_VIEW.equalsIgnoreCase(authorization) ||
						IPolicyAuthorization.PRIVILEGE_BILL_MANAGEMENT.equalsIgnoreCase(authorization)) {
						
						// create collection of billgroup schedulers for determining availability
						List<BillGroupTransactionScheduler> billingSchedulerList = new ArrayList<>();
						for (BillGroupTransactionScheduler scheduler : policyAuthorization.getTransactionSchedulerList()) {
							if (SCHEDULER_BILLING_PRDF_NAME.equalsIgnoreCase(scheduler.getPrdfName())) {
								billingSchedulerList.add(scheduler);
							}
						}
												
						if (POLICY_ON_AMENDMENT_HOLD) {
							determineBillGroupAvailabilityForAmendmentHold(policyAuthorization, billingSchedulerList);
						}
						else {
							determineBillGroupAvailabilityNoAmendmentHold(policyAuthorization, billingSchedulerList);
						}
						
						break;		// prevent duplicate processing of billgroup in case user has both view & mgmt authorization	
					}										
				}
			}
		} 
		
		//sort matched policy authorizations alpha
		Collections.sort(memberCoverageStatementDataBean.getMatchedPolicyAuthorizations(), this);
		setShowBillGroupPanel(!memberCoverageStatementDataBean.getMatchedPolicyAuthorizations().isEmpty());
		
		//sort excluded policy authorizations alpha
		Collections.sort(memberCoverageStatementDataBean.getExcludedPolicyAuthorizations(), this);
		setShowExcludedBillGroupPanel(!memberCoverageStatementDataBean.getExcludedPolicyAuthorizations().isEmpty());
	}	