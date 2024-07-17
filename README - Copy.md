private void parseArrearsMemberAdjustments(LoadBillDetailResponseDTO billDetailResponse){
		List<BillDepartment> adjustedMemberList = new ArrayList<BillDepartment>();

		for(OnlineBillDepartmentDTO department : billDetailResponse.getOnlineBillDepartmentList()){
			for(OnlineBillMemberAdjustmentsDTO memberAdjustments : department.getOnlineBillMemberAdjustmentsList()){
				List<BillMember> adjustedDepartmentList = new ArrayList<BillMember>();

				//set department totals & name
				BillDepartment billDepartment = new BillDepartment();
				billDepartment.setAdjustedPayrollTotal(formatBillCurrencyDollarSign(String.valueOf(memberAdjustments.getAdjustmentsPayrollTotal())));
				billDepartment.setAdjustedTotal(formatBillCurrencyDollarSign(String.valueOf(memberAdjustments.getAdjustmentsTotal())));
				billDepartment.setAdjustedPremiumTotal(formatBillCurrencyDollarSign(String.valueOf(memberAdjustments.getAdjustmentsPremiumTotal())));
				billDepartment.setName(memberAdjustments.getDisplayName());

				//set members
				int count = 0;
				for(OnlineBillMemberDTO member : memberAdjustments.getOnlineBillMemberList()){
					BillMember billMember = new BillMember();
					billMember.setCaseMemberKey(String.valueOf(member.getCaseMemberKey()));
					billMember.setFirstName(member.getFirstName());
					billMember.setLastName(member.getLastName());
					billMember.setCertNumber(member.getMemberNo());
					billMember.setCount(count++);
					billMember.setAdjustedPayrollDeductionSubtotal(formatBillCurrencyDollarSign(String.valueOf(member.getPayrollTotal())));
					billMember.setAdjustedPremiumSubtotal(formatBillCurrencyDollarSign(String.valueOf(member.getPremiumTotal())));
					billMember.setAdjustedTotal(formatBillCurrencyDollarSign(String.valueOf(member.getTotalAdjustments())));

					//load coverages specific to the member
					List<BillMemberAdjustment> adjustmentList = new ArrayList<BillMemberAdjustment>();
					for(OnlineBillCoverageDTO memberCoverage : member.getOnlineBillCoverageList()){
						BillMemberAdjustment memberAdjustment = new BillMemberAdjustment();
						memberAdjustment.setDescription(memberCoverage.getBenefitShortName());
						memberAdjustment.setPayrollDeduction(formatBillCurrency(String.valueOf(memberCoverage.getPayrollDeduction())));
						memberAdjustment.setPremium(formatBillCurrency(String.valueOf(memberCoverage.getPremiumFees())));
						memberAdjustment.setReason(memberCoverage.getAdjustmentReason());
						adjustmentList.add(memberAdjustment);
					}
					billMember.setAdjustments(adjustmentList);

					//add the member to the current department
					adjustedDepartmentList.add(billMember);
				}

				//check if we have payroll deductions and if so, set indicator that we have them
				if(!getManageBillsDataBean().isPayrollDeductions()){
					checkPayrollDeductions(adjustedDepartmentList);
				}

				//we are done with the current department, add the list of members to the current department
				//and add the current department to the overall department list
				billDepartment.setMembers(adjustedDepartmentList);
				adjustedMemberList.add(billDepartment);
			}
		}
		getManageBillsDataBean().setAdjustedMemberList(adjustedMemberList);
		calcBillAdjustmentTotal(billDetailResponse);

		logger.debug("**************************************************************************");
		logger.debug("ManageBillsHelperBean: parseArrearsMemberAdjustments: = {}", adjustedMemberList.toString());
		logger.debug("**************************************************************************");
	}