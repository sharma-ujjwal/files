
	private void parseLoadPaginationActionResponse(JSONObject response) throws JSONException {
		XStream xstream = new XStream(new JettisonMappedXmlDriver());
		xstream.alias("memberListResponseDTO", MemberListResponseDTO.class);
		xstream.addImplicitCollection(MemberListResponseDTO.class, "memberList", MemberDTO.class);
		xstream.addImplicitCollection(MemberDTO.class, "coverages", CoverageDTO.class);
		xstream.addPermission(NoTypePermission.NONE);
		xstream.addPermission(NullPermission.NULL);
		xstream.addPermission(PrimitiveTypePermission.PRIMITIVES);
		xstream.allowTypesByWildcard(new String[] {
				MemberListResponseDTO.class.getPackage().getName()+".*"
		});

		paginatedMemberList = new ArrayList<>();

		boolean allSelected = true;
		if (response.toString().length() > 2) {
			MemberListResponseDTO memberListReplyDTO = (MemberListResponseDTO) xstream.fromXML(response.toString());

			if (memberListReplyDTO.getMemberList() == null) {
				return;
			}
			int count = 0;
			for (MemberDTO memberDto : memberListReplyDTO.getMemberList()) {

				try {
					
					memberDto.setDateOfBirth(formatForDisplay(memberDto.getDateOfBirth()));

					// NOTE: If the MemberStatus is COBRA, then the
					// MemberStatusEffectiveDate
					// is being set to empty below. Related to SDT 2108543
					if (StringUtils.isNotEmpty(memberDto.getMemberStatusEffectiveDate())) {
						memberDto.setMemberStatusEffectiveDate(formatForDisplay(memberDto.getMemberStatusEffectiveDate()));
					}

					if (StringUtils.isNotEmpty(memberDto.getSalary())) {
						memberDto.setSalary(memberDto.getSalary());
					}

					if (StringUtils.isNotEmpty(memberDto.getSalaryEffectiveDate())) {
						memberDto.setSalaryEffectiveDate(formatForDisplay(memberDto.getSalaryEffectiveDate()));
					}

					if (StringUtils.isNotEmpty(memberDto.getMemberStatus())) {
						/*
						 * This is kind of messed up, but we if the Member
						 * Status is 01 - Active and the Member Group contains
						 * COBRA we want to use COBRA instead of Active. This is
						 * going to make the member search screens and the COBRA
						 * Wizard look all messed up but they assure me that it
						 * is really what they want. If not I suppose it will be
						 * easy enough to remove this chunk of code. Mike
						 */
						if (memberDto.getMemberStatus().equals("01") && memberDto.getMemberGroupName() != null && memberDto.getMemberGroupName().contains("COBRA")) {
							memberDto.setMemberStatus("COBRA");
							// SDT 2108543 - if they are on COBRA, we don't want
							// to show the termination date
							memberDto.setMemberStatusEffectiveDate("");
						} else {
							String statusDecode = getSessionValueBean().getDecode("memberStatusValues", memberDto.getMemberStatus());
							memberDto.setMemberStatus(statusDecode);
						}
					}
					if (StringUtils.isNotEmpty(memberDto.getSsn())) {
						int length = memberDto.getSsn().length();
						memberDto.setSsn(memberDto.getSsn().substring(length - 4));
					}
					if (memberDto.getCoverages() != null) {
						memberDto.setCoverages(populateShortNames(memberDto.getCoverages(), getPolicyValueBean().getMemberGroupKey(memberDto.getMemberGroupName())));
					}
				} catch (Exception e) {
					logger.error("Exception attempting to parse the load pagination action response {}", e.getMessage());
				}

				SelectedMembersDataBean selectedMembersDataBean = getSelectedMembersDataBean();

				Map<String, Object> selectedMembersMap = new LinkedHashMap<>();

				if (selectedMembersDataBean.getSelectedMembersMap() == null) {
					selectedMembersMap.clear();
				} else {
					selectedMembersMap = selectedMembersDataBean.getSelectedMembersMap();
				}

				if (selectedMembersMap.containsKey(memberDto.getCaseMbrKey())) {
					memberDto.setSelectedMember(true);
				} else {
					allSelected = false;
				}
				MemberSummary memberSummary = new MemberSummary(memberDto);
				memberSummary.setIndex(count++);
				paginatedMemberList.add(memberSummary);
			}

			setSelectAllMembers(allSelected);

			totalMembers = Integer.parseInt(memberListReplyDTO.getTotalMembers());
		}
	}


/*
 * Copyright (c) 2009 - 2010, Assurant Employee Benefits, All rights reserved.
 *
 *
 */
package com.assurant.inc.common.dto.member;

import java.util.List;

/**
 * The Class MemberListResponseDTO.
 */
public class MemberListResponseDTO {

	/** The member list. */
	private List<MemberDTO> memberList;

	/** The total members. */
	private String totalMembers;

	/**
	 * Gets the member list.
	 *
	 * @return the member list
	 */
	public List<MemberDTO> getMemberList() {
		return memberList;
	}

	/**
	 * Sets the member list.
	 *
	 * @param memberList the new member list
	 */
	public void setMemberList(List<MemberDTO> memberList) {
		this.memberList = memberList;
	}

	/**
	 * Gets the total members.
	 *
	 * @return the total members
	 */
	public String getTotalMembers() {
		return totalMembers;
	}

	/**
	 * Sets the total members.
	 *
	 * @param totalMembers the new total members
	 */
	public void setTotalMembers(String totalMembers) {
		this.totalMembers = totalMembers;
	}
}


/*
 * Copyright (c) 2009 - $(year), Assurant Employee Benefits, All rights reserved.
 */
package com.assurant.inc.common.dto.member;

import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.List;

import org.apache.commons.lang3.builder.ToStringBuilder;
import org.apache.commons.lang3.builder.ToStringStyle;

import com.assurant.inc.common.dto.coverage.CoverageDTO;

/**
 * The Class MemberDTO.
 */
public class MemberDTO {
	/** The request user. */
	private String requestUser;
	/** The request date. */
	private String requestDate;
	/** The request application. */
	private String requestApplication;
	/** The policy number. */
	private String policyNumber;
	/** The bill group key. */
	private String billGroupKey;
	/** The case key. */
	private String caseKey;
	/** The effective date. */
	private String effectiveDate;
	/** The case mbr key. */
	private String caseMbrKey;
	/** The client id. */
	private String clientId;
	/** The first name. */
	private String firstName;
	/** The middle name. */
	private String middleName;
	/** The last name. */
	private String lastName;
	/** The suffix. */
	private String suffix;
	/** The date of birth. */
	private String dateOfBirth;
	/** The gender. */
	private String gender;
	/** The gender code. */
	private String genderCode;
	/** The ssn. */
	private String ssn;
	/** The salary. */
	private String salary;
	/** The salary effective date. */
	private String salaryEffectiveDate;
	/** The hours. */
	private String hours;
	/** The pay period. */
	private String payPeriod;
	/** The pay period code. */
	private String payPeriodCode;
	/** The facility id. */
	private String facilityId;
	/** The facility benefit key. */
	private String facilityBenefitKey;
	/** The facility effective date. */
	private String facilityEffectiveDate;
	/** The Asdo stat cd. */
	private String AsdoStatCd;
	/** The Asdo val rslt cd. */
	private String AsdoValRsltCd;
	/** The doctor key. */
	private String doctorKey;
	/** The department. */
	private String department;
	/** The department effective date. */
	private String departmentEffectiveDate;
	/** The member group key. */
	private String memberGroupKey;
	/** The employment status. */
	private String employmentStatus;
	/** The full time hire date. */
	private String fullTimeHireDate;
	/** The part time hire date. */
	private String partTimeHireDate;
	/** The smoker. */
	private String smoker;
	/** The smoker code. */
	private String smokerCode;
	/** The language preference. */
	private String languagePreference;
	/** The language preference code. */
	private String languagePreferenceCode;
	/** The signature date. */
	private String signatureDate;
	/** The member status. */
	private String memberStatus;
	/** The member status effective date. */
	private String memberStatusEffectiveDate;
	/** The address. */
	private AddressDTO address;
	/** The termination reason. */
	private String terminationReason;

	/*
	 * Coverages that the member has
	 */
	/** The coverages. */
	List<CoverageDTO> coverages;
	/** The remove coverage list. */
	List<CoverageDTO> removeCoverageList;
	/** The increase coverage list. */
	List<CoverageDTO> increaseCoverageList;
	/** The decrease coverage list. */
	List<CoverageDTO> decreaseCoverageList;
	/** The add coverage list. */
	List<CoverageDTO> addCoverageList;
	/** The remove dependent list. */
	List<DependentDTO> removeDependentList;
	/** The update dependent list. */
	List<DependentDTO> updateDependentList;
	/** The add dependent list. */
	List<DependentDTO> addDependentList;

	// Non-compass fields
	/** The comments. */
	private String comments;
	/** The first name display. */
	private String firstNameDisplay;
	/** The middle name display. */
	private String middleNameDisplay;
	/** The last name display. */
	private String lastNameDisplay;
	/** The suffix display. */
	private String suffixDisplay;
	/** The member number. */
	private String memberNumber;
	/** The member group name. */
	private String memberGroupName;
	/** The old member group. */
	private String oldMemberGroup;
	/** The old department. */
	private String oldDepartment;
	/** The life event. */
	private String lifeEvent;
	/** The life event date. */
	private String lifeEventDate;
	/** The termination date. */
	private String terminationDate;
	/** The member update. */
	private boolean memberUpdate = false;
	/** The compass member update. */
	private boolean compassMemberUpdate = false;
	/** The selected member. */
	private boolean selectedMember = false;

	public boolean isSelectedMember() {
		return selectedMember;
	}
	public void setSelectedMember(boolean selectedMember) {
		this.selectedMember = selectedMember;
	}
	public String getRequestUser() {
		return requestUser;
	}
	public void setRequestUser(String requestUser) {
		this.requestUser = requestUser;
	}

	public String getRequestDate() {
		return requestDate;
	}
	public void setRequestDate(String requestDate) {
		this.requestDate = requestDate;
	}
	public void setRequestDate(Date requestDate) {
		if (requestDate == null) {
			this.requestDate = null;
		} else {
			DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy");
			this.requestDate = dateFormat.format(requestDate);
		}
	}

	public String getRequestApplication() {
		return requestApplication;
	}
	public void setRequestApplication(String requestApplication) {
		this.requestApplication = requestApplication;
	}

	public boolean isCompassMemberUpdate() {
		return compassMemberUpdate;
	}
	public void setCompassMemberUpdate(boolean compassMemberUpdate) {
		this.compassMemberUpdate = compassMemberUpdate;
	}

	public String getMemberGroupName() {
		return memberGroupName;
	}
	public void setMemberGroupName(String memberGroupName) {
		this.memberGroupName = memberGroupName;
	}

	public String getOldMemberGroup() {
		return oldMemberGroup;
	}
	public void setOldMemberGroup(String oldMemberGroup) {
		this.oldMemberGroup = oldMemberGroup;
	}

	public String getFirstNameDisplay() {
		return firstNameDisplay;
	}
	public void setFirstNameDisplay(String firstNameDisplay) {
		this.firstNameDisplay = firstNameDisplay;
	}

	public String getLastNameDisplay() {
		return lastNameDisplay;
	}
	public void setLastNameDisplay(String lastNameDisplay) {
		this.lastNameDisplay = lastNameDisplay;
	}

	public String getMemberNumber() {
		return memberNumber;
	}
	public void setMemberNumber(String memberNumber) {
		this.memberNumber = memberNumber;
	}

	public String getFirstName() {
		return firstName;
	}
	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}

	public String getMiddleName() {
		return middleName;
	}
	public void setMiddleName(String middleName) {
		this.middleName = middleName;
	}

	public String getLastName() {
		return lastName;
	}
	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public String getSuffix() {
		return suffix;
	}
	public void setSuffix(String suffix) {
		this.suffix = suffix;
	}

	public String getDateOfBirth() {
		return dateOfBirth;
	}
	public void setDateOfBirth(String dateOfBirth) {
		this.dateOfBirth = dateOfBirth;
	}

	public String getGender() {
		return gender;
	}
	public void setGender(String gender) {
		this.gender = gender;
	}

	public String getSsn() {
		return ssn;
	}
	public void setSsn(String ssn) {
		this.ssn = ssn;
	}

	public String getSalary() {
		return salary;
	}
	public void setSalary(String salary) {
		this.salary = salary;
	}

	public String getSalaryEffectiveDate() {
		return salaryEffectiveDate;
	}
	public void setSalaryEffectiveDate(String salaryEffectiveDate) {
		this.salaryEffectiveDate = salaryEffectiveDate;
	}

	public String getHours() {
		return hours;
	}
	public void setHours(String hours) {
		this.hours = hours;
	}

	public String getPayPeriod() {
		return payPeriod;
	}
	public void setPayPeriod(String payPeriod) {
		this.payPeriod = payPeriod;
	}

	public String getFacilityId() {
		return facilityId;
	}
	public void setFacilityId(String facilityId) {
		this.facilityId = facilityId;
	}

	public String getDepartment() {
		return department;
	}
	public void setDepartment(String department) {
		this.department = department;
	}

	public String getDepartmentEffectiveDate() {
		return departmentEffectiveDate;
	}
	public void setDepartmentEffectiveDate(String departmentEffectiveDate) {
		this.departmentEffectiveDate = departmentEffectiveDate;
	}

	public String getMemberGroupKey() {
		return memberGroupKey;
	}
	public void setMemberGroupKey(String memberGroupKey) {
		this.memberGroupKey = memberGroupKey;
	}

	public String getEmploymentStatus() {
		return employmentStatus;
	}
	public void setEmploymentStatus(String employmentStatus) {
		this.employmentStatus = employmentStatus;
	}

	public String getFullTimeHireDate() {
		return fullTimeHireDate;
	}
	public void setFullTimeHireDate(String fullTimeHireDate) {
		this.fullTimeHireDate = fullTimeHireDate;
	}

	public String getPartTimeHireDate() {
		return partTimeHireDate;
	}
	public void setPartTimeHireDate(String partTimeHireDate) {
		this.partTimeHireDate = partTimeHireDate;
	}

	public String getSmoker() {
		return smoker;
	}
	public void setSmoker(String smoker) {
		this.smoker = smoker;
	}

	public String getLanguagePreference() {
		return languagePreference;
	}
	public void setLanguagePreference(String languagePreference) {
		this.languagePreference = languagePreference;
	}

	public AddressDTO getAddress() {
		return address;
	}
	public void setAddress(AddressDTO address) {
		this.address = address;
	}

	public String getTerminationReason() {
		return terminationReason;
	}
	public void setTerminationReason(String terminationReason) {
		this.terminationReason = terminationReason;
	}

	public List<CoverageDTO> getRemoveCoverageList() {
		return removeCoverageList;
	}
	public void setRemoveCoverageList(List<CoverageDTO> removeCoverageList) {
		this.removeCoverageList = removeCoverageList;
	}

	public List<CoverageDTO> getAddCoverageList() {
		return addCoverageList;
	}
	public void setAddCoverageList(List<CoverageDTO> addCoverageList) {
		this.addCoverageList = addCoverageList;
	}

	public List<DependentDTO> getRemoveDependentList() {
		return removeDependentList;
	}
	public void setRemoveDependentList(List<DependentDTO> removeDependentList) {
		this.removeDependentList = removeDependentList;
	}

	public List<DependentDTO> getUpdateDependentList() {
		return updateDependentList;
	}
	public void setUpdateDependentList(List<DependentDTO> updateDependentList) {
		this.updateDependentList = updateDependentList;
	}

	public List<DependentDTO> getAddDependentList() {
		return addDependentList;
	}
	public void setAddDependentList(List<DependentDTO> addDependentList) {
		this.addDependentList = addDependentList;
	}

	public String getEffectiveDate() {
		return effectiveDate;
	}
	public void setEffectiveDate(String effectiveDate) {
		this.effectiveDate = effectiveDate;
	}

	public String getCaseMbrKey() {
		return caseMbrKey;
	}
	public void setCaseMbrKey(String caseMbrKey) {
		this.caseMbrKey = caseMbrKey;
	}

	public String getClientId() {
		return clientId;
	}
	public void setClientId(String clientId) {
		this.clientId = clientId;
	}

	public String getSignatureDate() {
		return signatureDate;
	}
	public void setSignatureDate(String signatureDate) {
		this.signatureDate = signatureDate;
	}

	public String getPolicyNumber() {
		return policyNumber;
	}
	public void setPolicyNumber(String policyNumber) {
		this.policyNumber = policyNumber;
	}

	public String getCaseKey() {
		return caseKey;
	}
	public void setCaseKey(String caseKey) {
		this.caseKey = caseKey;
	}

	public String getSmokerCode() {
		return smokerCode;
	}
	public void setSmokerCode(String smokerCode) {
		this.smokerCode = smokerCode;
	}

	public String getMemberStatus() {
		return memberStatus;
	}
	public void setMemberStatus(String memberStatus) {
		this.memberStatus = memberStatus;
	}

	public String getPayPeriodCode() {
		return payPeriodCode;
	}
	public void setPayPeriodCode(String payPeriodCode) {
		this.payPeriodCode = payPeriodCode;
	}

	public String getLanguagePreferenceCode() {
		return languagePreferenceCode;
	}
	public void setLanguagePreferenceCode(String languagePreferenceCode) {
		this.languagePreferenceCode = languagePreferenceCode;
	}

	public String getGenderCode() {
		return genderCode;
	}
	public void setGenderCode(String genderCode) {
		this.genderCode = genderCode;
	}

	public String getFacilityBenefitKey() {
		return facilityBenefitKey;
	}
	public void setFacilityBenefitKey(String facilityBenefitKey) {
		this.facilityBenefitKey = facilityBenefitKey;
	}

	public String getAsdoStatCd() {
		return AsdoStatCd;
	}
	public void setAsdoStatCd(String asdoStatCd) {
		AsdoStatCd = asdoStatCd;
	}

	public String getAsdoValRsltCd() {
		return AsdoValRsltCd;
	}
	public void setAsdoValRsltCd(String asdoValRsltCd) {
		AsdoValRsltCd = asdoValRsltCd;
	}

	public String getFacilityEffectiveDate() {
		return facilityEffectiveDate;
	}
	public void setFacilityEffectiveDate(String facilityEffectiveDate) {
		this.facilityEffectiveDate = facilityEffectiveDate;
	}

	public String getDoctorKey() {
		return doctorKey;
	}
	public void setDoctorKey(String doctorKey) {
		this.doctorKey = doctorKey;
	}

	public boolean isMemberUpdate() {
		return memberUpdate;
	}
	public void setMemberUpdate(boolean memberUpdate) {
		this.memberUpdate = memberUpdate;
	}

	public String getLifeEvent() {
		return lifeEvent;
	}
	public void setLifeEvent(String lifeEvent) {
		this.lifeEvent = lifeEvent;
	}

	public String getLifeEventDate() {
		return lifeEventDate;
	}
	public void setLifeEventDate(String lifeEventDate) {
		this.lifeEventDate = lifeEventDate;
	}

	public String getBillGroupKey() {
		return billGroupKey;
	}
	public void setBillGroupKey(String billGroupKey) {
		this.billGroupKey = billGroupKey;
	}

	public List<CoverageDTO> getIncreaseCoverageList() {
		return increaseCoverageList;
	}
	public void setIncreaseCoverageList(List<CoverageDTO> increaseCoverageList) {
		this.increaseCoverageList = increaseCoverageList;
	}

	public List<CoverageDTO> getDecreaseCoverageList() {
		return decreaseCoverageList;
	}
	public void setDecreaseCoverageList(List<CoverageDTO> decreaseCoverageList) {
		this.decreaseCoverageList = decreaseCoverageList;
	}

	public String getTerminationDate() {
		return terminationDate;
	}
	public void setTerminationDate(String terminationDate) {
		this.terminationDate = terminationDate;
	}

	public String getComments() {
		return comments;
	}
	public void setComments(String comments) {
		this.comments = comments;
	}

	public String getOldDepartment() {
		return oldDepartment;
	}
	public void setOldDepartment(String oldDepartment) {
		this.oldDepartment = oldDepartment;
	}

	public String getMiddleNameDisplay() {
		return middleNameDisplay;
	}
	public void setMiddleNameDisplay(String middleNameDisplay) {
		this.middleNameDisplay = middleNameDisplay;
	}

	public String getSuffixDisplay() {
		return suffixDisplay;
	}
	public void setSuffixDisplay(String suffixDisplay) {
		this.suffixDisplay = suffixDisplay;
	}

	public String getMemberStatusEffectiveDate() {
		return memberStatusEffectiveDate;
	}
	public void setMemberStatusEffectiveDate(String memberStatusEffectiveDate) {
		this.memberStatusEffectiveDate = memberStatusEffectiveDate;
	}

	public List<CoverageDTO> getCoverages() {
		return coverages;
	}
	public void setCoverages(List<CoverageDTO> coverages) {
		this.coverages = coverages;
	}

	@Override
	public String toString(){
		return ToStringBuilder.reflectionToString(this, ToStringStyle.MULTI_LINE_STYLE);
	}
}

/*
 * Copyright (c) 2009 - $(year), Assurant Employee Benefits, All rights reserved.
 */
package com.assurant.inc.common.dto.coverage;

import org.apache.commons.lang3.builder.ToStringBuilder;
import org.apache.commons.lang3.builder.ToStringStyle;

/**
 * The Class CoverageDTO.
 */
public class CoverageDTO {

	/** The benefit key. */
	private String benefitKey;

	/** The description. */
	private String description;

	/** The narl key. */
	private String narlKey;

	/** The amount. */
	private String amount;

	/** The effective date. */
	private String effectiveDate;

	/** The expire date. */
	private String expireDate;

	/** The contrib. */
	private boolean contrib;

	/** The benefit short name. */
	private String benefitShortName;

	/** The benefit plan type. */
	private String benefitPlanType;

	/**
	 * The choice code
	 */
	private String choiceCode;

	/**
	 * Checks if is contrib.
	 *
	 * @return true, if is contrib
	 */
	public boolean isContrib() {
		return contrib;
	}

	/**
	 * Sets the contrib.
	 *
	 * @param contrib the new contrib
	 */
	public void setContrib(boolean contrib) {
		this.contrib = contrib;
	}

	/**
	 * Gets the benefit key.
	 *
	 * @return the benefit key
	 */
	public String getBenefitKey() {
		return benefitKey;
	}

	/**
	 * Sets the benefit key.
	 *
	 * @param benefitKey the new benefit key
	 */
	public void setBenefitKey(String benefitKey) {
		this.benefitKey = benefitKey;
	}

	/**
	 * Gets the amount.
	 *
	 * @return the amount
	 */
	public String getAmount() {
		return amount;
	}

	/**
	 * Sets the amount.
	 *
	 * @param amount the new amount
	 */
	public void setAmount(String amount) {
		this.amount = amount;
	}

	/**
	 * Gets the narl key.
	 *
	 * @return the narl key
	 */
	public String getNarlKey() {
		return narlKey;
	}

	/**
	 * Sets the narl key.
	 *
	 * @param narlKey the new narl key
	 */
	public void setNarlKey(String narlKey) {
		this.narlKey = narlKey;
	}

	/**
	 * Gets the effective date.
	 *
	 * @return the effective date
	 */
	public String getEffectiveDate() {
		return effectiveDate;
	}

	/**
	 * Sets the effective date.
	 *
	 * @param effectiveDate the new effective date
	 */
	public void setEffectiveDate(String effectiveDate) {
		this.effectiveDate = effectiveDate;
	}

	/**
	 * Gets the description.
	 *
	 * @return the description
	 */
	public String getDescription() {
		return description;
	}

	/**
	 * Sets the description.
	 *
	 * @param description the new description
	 */
	public void setDescription(String description) {
		this.description = description;
	}

	/**
	 * Gets the expire date.
	 *
	 * @return the expire date
	 */
	public String getExpireDate() {
		return expireDate;
	}

	/**
	 * Sets the expire date.
	 *
	 * @param expireDate the new expire date
	 */
	public void setExpireDate(String expireDate) {
		this.expireDate = expireDate;
	}

	/**
	 * Gets the benefit short name.
	 *
	 * @return the benefit short name
	 */
	public String getBenefitShortName() {
		return benefitShortName;
	}

	/**
	 * Sets the benefit short name.
	 *
	 * @param benefitShortName the new benefit short name
	 */
	public void setBenefitShortName(String benefitShortName) {
		this.benefitShortName = benefitShortName;
	}

	/**
	 * Gets the benefit plan type.
	 *
	 * @return the benefit plan type
	 */
	public String getBenefitPlanType() {
		return benefitPlanType;
	}

	/**
	 * Sets the benefit plan type.
	 *
	 * @param benefitPlanType the new benefit plan type
	 */
	public void setBenefitPlanType(String benefitPlanType) {
		this.benefitPlanType = benefitPlanType;
	}

	public String getChoiceCode() {
		return choiceCode;
	}

	public void setChoiceCode(String choiceCode) {
		this.choiceCode = choiceCode;
	}

	public boolean isExpireDateNull() {
		if (expireDate == null) {
			return true;
		} else {
			return false;
		}
	}

	/* (non-Javadoc)
	 * @see java.lang.Object#toString()
	 */
	@Override
	public String toString() {
		return ToStringBuilder.reflectionToString(this, ToStringStyle.MULTI_LINE_STYLE);
	}
}
