private void populateAdjustedBillDepartmentList(List<BillDepartment> printBillsMemberList, List<BillDepartment> adjustedBillDepartmentList){

		for(BillDepartment billDepartment : printBillsMemberList){
			List<BillMember> billMemberList = new ArrayList<BillMember>();
			BillDepartment department = new BillDepartment();
			department.setName(billDepartment.getName());
			//department.setMemberPastDueAmountTotal(billDepartment.getMemberPastDueAmountTotal());
			department.setMemberEmployeeTotal(billDepartment.getMemberEmployeeTotal());
			department.setMemberEmployerTotal(billDepartment.getMemberEmployerTotal());
			department.setMemberBillAmountDueTotal(billDepartment.getMemberBillAmountDueTotal());
			int count=0;
			for(BillMember billMember : billDepartment.getMembers()){
				BillMember member = new BillMember();
				member.setFirstName(billMember.getFirstName());
				member.setLastName(billMember.getLastName());
				member.setCertNumber(billMember.getCertNumber());
				//member.setPastDueAmountTotal(billMember.getPastDueAmountTotal());
				member.setEmployerTotal(billMember.getEmployerTotal());
				member.setEmployeeTotal(billMember.getEmployeeTotal());
				member.setBillAmountDueTotal(billMember.getBillAmountDueTotal());
				member.setCount(count++);
				//employment status & waived message make up the waived/cobra column in the
				//member details download spreadsheet
				member.setEmploymentStatus(billMember.getEmploymentStatus());
				member.setWaivedMessage(billMember.getWaivedMessage());


				List<BillMemberCoverage> billMemberCoverageList = new ArrayList<BillMemberCoverage>();
				if(billMember.getCoverages() != null && billMember.getCoverages().size() > 0){
					billMemberCoverageList.addAll(billMember.getCoverages());
					member.setCoverages(billMemberCoverageList);
				}

				List<BillMemberAdjustment> billMemberAdjustmentList = new ArrayList<BillMemberAdjustment>();
				if(billMember.getAdjustments() != null && billMember.getAdjustments().size() > 0){
					billMemberAdjustmentList.addAll(billMember.getAdjustments());
					member.setAdjustments(billMemberAdjustmentList);
				}

				billMemberList.add(member);
			}

			department.setMembers(billMemberList);
			adjustedBillDepartmentList.add(department);
		}
	}