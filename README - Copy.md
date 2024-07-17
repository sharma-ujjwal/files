private void parseArrearsMemberDetails(LoadBillDetailResponseDTO billDetailResponse){
		List<BillDepartment> mbrDepartmentList = new ArrayList<BillDepartment>();

		for(OnlineBillDepartmentDTO department : billDetailResponse.getOnlineBillDepartmentList()){

			for(OnlineBillDepartmentMembersDTO departmentMembers : department.getOnlineBillDepartmentMemberList()){
				List<BillMember> departmentMemberList = new ArrayList<BillMember>();

				//set department totals & name
				BillDepartment billDepartment = new BillDepartment();
				billDepartment.setMemberEmployeeTotal(departmentMembers.getDepartmentEmployeeTotal());
				billDepartment.setMemberEmployerTotal(departmentMembers.getDepartmentEmployerTotal());
				//billDepartment.setMemberPastDueAmountTotal(departmentMembers.getDepartmentPastDueTotal());
				billDepartment.setMemberBillAmountDueTotal(departmentMembers.getDepartmentTotalAmountDueTotal());
				billDepartment.setName(departmentMembers.getDisplayName());

				//set members
				int count=0;
				for(OnlineBillMemberDTO member : departmentMembers.getOnlineBillMemberList()){
					BillMember billMember = new BillMember();
					billMember.setCaseMemberKey(String.valueOf(member.getCaseMemberKey()));
					billMember.setFirstName(member.getFirstName());
					billMember.setLastName(member.getLastName());
					billMember.setCertNumber(member.getMemberNo());
					billMember.setWaivedMessage(member.getWaivedMessage());
					billMember.setCount(count++);
					billMember.setEmployeeTotal(member.getEmployeeTotal());
					billMember.setEmployerTotal(member.getEmployerTotal());
					//billMember.setPastDueAmountTotal(member.getPastDueAmountTotal());
					billMember.setBillAmountDueTotal(member.getBillAmountDueTotal());
					billMember.setAmountRemittedTotal(member.getBillAmountDueTotal());

					//load coverages specific to the member
					List<BillMemberCoverage> coverageList = new ArrayList<BillMemberCoverage>();
					for(OnlineBillCoverageDTO memberCoverage : member.getOnlineBillCoverageList()){
						BillMemberCoverage billMemberCoverage = new BillMemberCoverage();
						billMemberCoverage.setShortName(memberCoverage.getBenefitShortName());
						//billMemberCoverage.setPastDueAmount(memberCoverage.getPastDueAmount());
						billMemberCoverage.setCoverageAmount(memberCoverage.getBenefitAmount());
						billMemberCoverage.setEmployerContribution(memberCoverage.getEmployerContribution());
						billMemberCoverage.setEmployeeContribution(memberCoverage.getEmployeeContribution());
						billMemberCoverage.setBillAmountDue(memberCoverage.getBenefitTotal());
						billMemberCoverage.setAmountRemitted(memberCoverage.getBenefitTotal());
						billMemberCoverage.setAgeBand(memberCoverage.getAgeBand());
						billMemberCoverage.setRollupIndicator(memberCoverage.getRollupIndicator());
						billMemberCoverage.setPremiumAdjustmentReason("NA");
						coverageList.add(billMemberCoverage);
					}
					billMember.setCoverages(coverageList);

					//load the summarized benefits
					billMember.setSummarizedCoverageList(retrieveSummarizedCoverage(coverageList));

					//add the member to the current department
					departmentMemberList.add(billMember);
				}

				//we are done with the current department, add the list of members to the current department
				//and add the current department to the overall department list
				billDepartment.setMembers(departmentMemberList);
				mbrDepartmentList.add(billDepartment);
			}
		}
		getManageBillsDataBean().setTotalBillAmountDue(billDetailResponse.getTotalAmountDue());
		getManageBillsDataBean().setMemberList(mbrDepartmentList);

		logger.debug("**************************************************************************");
		logger.debug("ManageBillsHelperBean: parseArrearsMemberDetails: = {}", mbrDepartmentList.toString());
		logger.debug("**************************************************************************");
	}