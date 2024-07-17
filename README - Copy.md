/*
 * Copyright (c) 2009 - $(year), Assurant Employee Benefits, All rights reserved.
 */
package com.sunlife.inc.oa.employer.beans.helper;

import java.io.IOException;
import java.io.Serializable;
import java.text.DateFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.List;
import java.util.UUID;
import com.sunlife.inc.oa.employer.config.EmployerIocLookup;
import com.sunlife.inc.oa.employer.datatypes.Coverage;
import com.sunlife.inc.oa.employer.datatypes.SystemVarianceDetail;
import com.sunlife.inc.oa.employer.valueBeans.PolicyValueBean;
import org.apache.commons.lang3.StringUtils;
import org.apache.commons.lang3.time.DateUtils;
import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import javax.annotation.PostConstruct;
import javax.enterprise.context.RequestScoped;
import com.assurant.inc.common.constant.EmployerConstants;
import com.assurant.inc.common.constant.JsonConstants;
import com.assurant.inc.common.constant.TransactionTypes;
import com.sunlife.inc.oa.employer.beans.DependentBean;
import com.sunlife.inc.oa.employer.beans.EnrollMemberWizardDataBean;
import com.sunlife.inc.oa.employer.beans.MemberInfoBean;
import com.sunlife.inc.oa.employer.beans.PolicyFormsBean;
import com.sunlife.inc.oa.employer.beans.PolicyInfoDataBean;
import com.sunlife.inc.oa.employer.beans.UserBean;
import com.sunlife.inc.oa.employer.beans.generic.AHelperBean;

import javax.inject.Inject;
import javax.inject.Named;

/**
 * The Class EnrollMemberWizardHelperBean.
 */
@Named("enrollMemberWizardHelperBean")
@RequestScoped
public class EnrollMemberWizardHelperBean extends AHelperBean implements Serializable{
	private static final long serialVersionUID = 1L;
	private static final Logger logger = LoggerFactory.getLogger(EnrollMemberWizardHelperBean.class);
	private static String COMPASS_DATE_FORMAT = "yyyyMMddHHmmss.SSS";
	private static String RETURN_CODE_SUCCESS = "0";
	private static String RETURN_CODE_SUCCESS_PENDING_EOI = "1";
	private static String RETURN_CODE_ERROR = "-1";
	private static int WARNING_VARIANCE_LEVEL = 2;
	private static int ERROR_VARIANCE_LEVEL = 3;
	@Inject
	@Named("enrollMemberWizardDataBean")
	private EnrollMemberWizardDataBean enrollMemberWizardDataBean;
	@Inject
	@Named("memberInfoBean")
	private MemberInfoBean memberInfoBean;
	@Inject
	@Named("policyValueBean")
	private PolicyValueBean policyValueBean;
	@Inject
	@Named("policyInfoDataBean")
	private PolicyInfoDataBean policyInfoDataBean;
	@Inject
	@Named("facilityHelperBean")
	private FacilityHelperBean facilityHelperBean;
	@Inject
	@Named("userBean")
	private UserBean userBean;
	@Inject
	@Named("policyFormsBean")
	private PolicyFormsBean policyFormsBean;
	@Inject
	@Named("varianceDetailHelperBean")
	private VarianceDetailHelperBean varianceDetailHelperBean;

	@Inject
	@Named("employerIocLookup")
	private EmployerIocLookup employerIocLookup;

	@PostConstruct
	public void init() {
		setMiddlewareServiceUrl(employerIocLookup.getMiddlewareSrvcUrl());
	}

	/**
	 * Call the employer middleware enroll member manager to process the
	 * enrollment. This method will return the appropriate enrollment message
	 * based on the results of the enrollment.
	 * 
	 * @return the string with the enrollment message
	 */
	public void processEnrollment() {
		boolean isEnrollmentSuccessful = false;
		String enrollmentMessage = "An error has occurred during the enrollment. Please print this page and contact Customer Advocacy.";

		try {
			JSONObject formattedRequest = new JSONObject();
			JSONObject enrollmentRequest = buildEnrollmentRequest();

			//set if we are an internal user
			if(getUserBean().isInternalAccess()){
				enrollmentRequest.put("internalAccess", getUserBean().isInternalAccess());
			}
			
			int varianceLevel = getUserBean().isInternalAccess() ? WARNING_VARIANCE_LEVEL : ERROR_VARIANCE_LEVEL;
			enrollmentRequest.put("varianceLevel", varianceLevel);

			//build the care event object for mits
			JSONObject careEventObject = buildCareEventObject();
			enrollmentRequest.put("careEvent", careEventObject);
			
			System.out.println("enrollMemberWizardHelperBean: jsonRequest is: " + enrollmentRequest.toString());
			
			//**************************************************************************************************************************
			// Send request to middleware
			// If this is an internal transaction, check the results coming back from compass.  If the results contain trRefNos
			// return those results back and redirect to a different confirmation page.
			//**************************************************************************************************************************
			formattedRequest.put(JsonConstants.JSON_MANAGER, "enrollMemberManager");
			formattedRequest.put(JsonConstants.JSON_REQUEST, enrollmentRequest);

			JSONObject response = executeMiddlewareServiceRequest(formattedRequest);

			String returnCode = response.optString("returnCode");
			String returnMessage = response.optString("returnMessage");
			JSONObject resultSet = response.optJSONObject("resultSet");

			//----------------------------------------------------------------------------------------------------------
			//if this was processed by an internal user, check for error variances and reroute to internal confirmation page
			//----------------------------------------------------------------------------------------------------------
			if(getUserBean().isInternalAccess()){

				if(response != null && (response.has("compassVariance") || response.has("specialHandling"))){
					List<SystemVarianceDetail> varianceDetailList = getVarianceDetailHelperBean().populateVarianceMessages(response);
					getEnrollMemberWizardDataBean().setSystemVarianceDetailList(varianceDetailList);
					isEnrollmentSuccessful = false;
					enrollmentMessage = "This transaction was not completed successfully. The following variance Message(s) were received. Please copy any variances into the notes section of the current transaction in AtWork.";
				}else{
					isEnrollmentSuccessful = true;
					enrollmentMessage = "This transaction was completed successfully.";
				}
			}
			else{
				if (StringUtils.isEmpty(returnCode)) {
					/* If there is not a return code there was a bad error. */
					logger.error("No return code from enrollMemberManager. Check the middleware logs.");
				} else if (returnCode.equals(RETURN_CODE_ERROR)) {
					/* There was an error. Handle based on the return information. */
					if (returnMessage.equalsIgnoreCase("JSON Format Error")) {
						logger.info("EnrollMemberManager returned a format error on policy: " + getPolicyValueBean().getSelectedPolicyNumber());
						enrollmentMessage = "A format error has occurred during the enrollment.  Please print this page and contact Customer Advocacy.";
					} else if (returnMessage.equalsIgnoreCase("EmailError")) {
						logger.info("EnrollMemberManager returned a email error on policy: " + getPolicyValueBean().getSelectedPolicyNumber());
						enrollmentMessage = "An email error has occurred during the enrollment.  Please print this page and contact Customer Advocacy.";
					} else {
						logger.info("EnrollMemberManager returned an error on policy: " + getPolicyValueBean().getSelectedPolicyNumber());
						enrollmentMessage = "An error has occurred during the enrollment.  Please print this page and contact Customer Advocacy.";
					}
				} else if (returnCode.equals(RETURN_CODE_SUCCESS) && returnMessage.equalsIgnoreCase("EmailSuccess")) {
					/*
					 * The enrollment was successful but requires manual work by CA
					 * so let the user know it will take up to two days to process.
					 */
					logger.info("MemberInfoBean: buildEnrollment: Enrollment successful but email was sent.");
					enrollmentMessage = "Congratulations! Your enrollment has been submitted.  Please allow up to two business days for these changes to take effect.";
					isEnrollmentSuccessful = true;
				} else if (returnCode.equals(RETURN_CODE_SUCCESS_PENDING_EOI)) {
					/*
					 * The enrollment was successful but it requires manual work by
					 * CA to get EOI so let the user know there are some pending
					 * amounts. They want a link to the policy forms in this
					 * message, in order for the link to work we need to run the
					 * policy forms load data method.
					 */
	
					/*
					 * Okay.  I just commented this out because they really want it to
					 * work, but the form they want to link to does not exist. Supposedly
					 * they are going to add it then we will put the link back in.
					 * I am adding this comment on 8/1/2012, if this code is still commented
					 * out after 8/1/2013 just delete it.  Thanks, Mike.
					 */
					getPolicyFormsBean().loadPolicyData();
					enrollmentMessage = "Congratulations! Your request has been submitted successfully. Due to contractual provisions, additional coverage amounts are pending approval. You may download the Health Statement on the <a href=\"/employerui/content/policyForms.faces\">Policy Forms</a> page.";
	
					isEnrollmentSuccessful = true;
				} else {
					/*
					 * The enrollment went through with no exceptions or manual work
					 * required.
					 */
					enrollmentMessage = "Congratulations! The enrollment was successful.";
					isEnrollmentSuccessful = true;
				}
	
				if (resultSet != null) {
					getMemberInfoBean().setMemberNumber(resultSet.optString("mbrNo"));
					getMemberInfoBean().setCaseMbrKey(resultSet.optString("CaseMbrKey"));
					getMemberInfoBean().setClientId(resultSet.optString("clientId"));
				} else if(response.has("emailCaseMbrKey")){
					getMemberInfoBean().setCaseMbrKey(response.optString("emailCaseMbrKey"));
				}
			}

		} catch (JSONException e) {
			logger.error("JSONException attempting to process enrollment.");
		} catch (IOException e) {
			logger.error("IOException attempting to process enrollment.");
		} catch (Exception e){
			logger.error("Exception attempting to process enrollment.");			
		}

		getEnrollMemberWizardDataBean().setEnrollmentComplete(true);
		getEnrollMemberWizardDataBean().setEnrollmentSuccessful(isEnrollmentSuccessful);
		getEnrollMemberWizardDataBean().setEnrollmentMessage(enrollmentMessage);
	}

	/**
	 * Builds the enrollment request formatted for the EnrollMemberManager in
	 * Employer Middleware. The EnrollMemberManager is a candidate for
	 * modernization. Once a EnrollMemberRequestDTO is created in the Employer
	 * Middleware Client, this is where it will be populated. For now though we
	 * are still building the request the old way.
	 * 
	 * @return the JSONObject enrollment request
	 * @throws JSONException
	 *             the jSON exception
	 */
	private JSONObject buildEnrollmentRequest() throws JSONException {
		JSONObject enrollmentRequest = new JSONObject();

		Date requestDate = new Date();
		DateFormat compassDateFormat = new SimpleDateFormat(COMPASS_DATE_FORMAT);
		DateFormat employerDateFormat = new SimpleDateFormat(EMPLOYER_DATE_FORMAT);
		String requestDateCompassFormat = compassDateFormat.format(requestDate) + "[-5:America/Chicago]";
		String requestDateEmployerFormat = employerDateFormat.format(requestDate);
		//Added Unique ID in request
		String uniqueID = UUID.randomUUID().toString();

		enrollmentRequest.put(JsonConstants.JSON_UNIQUE_ID, uniqueID);

		enrollmentRequest.put(JsonConstants.JSON_REQUEST_USER, getUserBean().getUserName());
		enrollmentRequest.put(JsonConstants.JSON_REQUEST_DATE, new Date());
		enrollmentRequest.put(JsonConstants.JSON_REQUEST_APPLICATION, "OAc");

		enrollmentRequest.put("PolicyNumber", getPolicyValueBean().getSelectedPolicyNumber());
		enrollmentRequest.put("EffectiveDate", requestDateCompassFormat);
		enrollmentRequest.put("EffectiveDateEmail", requestDateEmployerFormat);
		enrollmentRequest.put("FirstName", getMemberInfoBean().getFirstName());
		enrollmentRequest.put("LastName", getMemberInfoBean().getLastName());
		if (getMemberInfoBean().getDateOfBirth() != null) {
			enrollmentRequest.put("BirthDate", compassDateFormat.format(getMemberInfoBean().getDateOfBirth()) + "[-5:America/Chicago]");
			enrollmentRequest.put("BirthDateFormatted", getMemberInfoBean().getFormatDateOfBirth());
		}
		enrollmentRequest.put("GenderCode", getMemberInfoBean().getGender());
		enrollmentRequest.put("Gender", getMemberInfoBean().getGenderDecode());
		enrollmentRequest.put("BillGroup", getPolicyValueBean().getSelectedBillGroup());
		enrollmentRequest.put("BillGroupKey", getPolicyValueBean().getSelectedBillGroupKey());
		enrollmentRequest.put("MemberGroupKey", getMemberInfoBean().getMemberGroupKey());
		enrollmentRequest.put("MemberGroupName", getMemberInfoBean().getMemberGroupName());

		if (getPolicyInfoDataBean().getCommenceDate() != null) {
			enrollmentRequest.put("SchemeCommencementDateFormatted", employerDateFormat.format(getPolicyInfoDataBean().getCommenceDate()));
		}

		/* If Life Event does not equal NA. */
		if (!getMemberInfoBean().getLifeEvent().equalsIgnoreCase("NA")) {
			enrollmentRequest.put("LifeEvent", getMemberInfoBean().getLifeEvent());
			if (getMemberInfoBean().getLifeEventDate() != null) {
				enrollmentRequest.put("LifeEventDate", getMemberInfoBean().getLifeEventDate());
			}
		}

		/* Employment Status. */
		enrollmentRequest.put("EmploymentStatusCode", getMemberInfoBean().getEmploymentStatus());
		if (getMemberInfoBean().getEmploymentStatus().equalsIgnoreCase("R")) {
			enrollmentRequest.put("EmploymentStatusDecode", "Regular Full-Time Employee");
		} else {
			enrollmentRequest.put("EmploymentStatusDecode", "Part-Time Employee");
		}

		/* Join Scheme Date. */
		if (getMemberInfoBean().getEmploymentStatus().equalsIgnoreCase("P") && getMemberInfoBean().getPartTimeHireDate() != null) {
			enrollmentRequest.put("JoinSchemeDate", compassDateFormat.format(getMemberInfoBean().getPartTimeHireDate()));
			enrollmentRequest.put("JoinSchemeDateEmail", employerDateFormat.format(getMemberInfoBean().getPartTimeHireDate()));
		} else if (getMemberInfoBean().getFullTimeHireDate() != null) {
			enrollmentRequest.put("JoinSchemeDate", compassDateFormat.format(getMemberInfoBean().getFullTimeHireDate()));
			enrollmentRequest.put("JoinSchemeDateEmail", employerDateFormat.format(getMemberInfoBean().getFullTimeHireDate()));
		}

		/* Full Time Date. */
		if (getMemberInfoBean().getFullTimeHireDate() != null) {
			enrollmentRequest.put("fullTimeHireDate", compassDateFormat.format(getMemberInfoBean().getFullTimeHireDate()));
			enrollmentRequest.put("FTHireDateFormatted", getMemberInfoBean().getFormattedFTHireDate());
			enrollmentRequest.put("JoinCompanyDate", compassDateFormat.format(getMemberInfoBean().getFullTimeHireDate()));
			enrollmentRequest.put("JoinCompanyDateEmail", employerDateFormat.format(getMemberInfoBean().getFullTimeHireDate()));
		}

		/* Part Time Date. */
		if (getMemberInfoBean().getPartTimeHireDate() != null) {
			enrollmentRequest.put("partTimeHireDate", compassDateFormat.format(getMemberInfoBean().getPartTimeHireDate()));
			enrollmentRequest.put("PTHireDateFormatted", getMemberInfoBean().getFormattedPTHireDate());
			enrollmentRequest.put("JoinCompanyDate", compassDateFormat.format(getMemberInfoBean().getPartTimeHireDate()));
			enrollmentRequest.put("JoinCompanyDateEmail", employerDateFormat.format(getMemberInfoBean().getPartTimeHireDate()));
		}

		if (StringUtils.isNotEmpty(getMemberInfoBean().getMiddleName())) {
			enrollmentRequest.put("MiddleName", getMemberInfoBean().getMiddleName());
		}
		if (StringUtils.isNotEmpty(getMemberInfoBean().getSuffix())) {
			enrollmentRequest.put("NameSuffix", getMemberInfoBean().getSuffix());
		}

		if (StringUtils.isNotEmpty(getMemberInfoBean().getPreferredLanguage())) {
			enrollmentRequest.put("LanguagePreferenceCode", getMemberInfoBean().getPreferredLanguage());
			enrollmentRequest.put("LanguagePreferenceDecode", getMemberInfoBean().getPreferredLanguageDecode());
		} else {
			/* Default to English. */
			enrollmentRequest.put("LanguagePreferenceCode", "1");
			enrollmentRequest.put("LanguagePreferenceDecode", getMemberInfoBean().getPreferredLanguageDecode());
		}

		if (StringUtils.isNotEmpty(getMemberInfoBean().getSsn())) {
			enrollmentRequest.put("SSN", getMemberInfoBean().getSsn());
		}
		if (StringUtils.isNotEmpty(getMemberInfoBean().getSalaryAmount())) {
			enrollmentRequest.put("SalaryAmount", getMemberInfoBean().getSalaryAmount());
			enrollmentRequest.put("SalaryAmountFormatted", getMemberInfoBean().getFormatSalaryAmount());
		}
		if (StringUtils.isNotEmpty(getMemberInfoBean().getHoursPerWeek())) {
			enrollmentRequest.put("HoursPerWeek", getMemberInfoBean().getHoursPerWeek());
		}
		if (StringUtils.isNotEmpty(getMemberInfoBean().getPayPeriod())) {
			enrollmentRequest.put("SalaryFrequency", getMemberInfoBean().getPayPeriod());
			enrollmentRequest.put("SalaryFrequencyDecode", getMemberInfoBean().getPayPeriodDecode());
		}
		if (getMemberInfoBean().getSalaryEffectiveDate() != null) {
			enrollmentRequest.put("SalaryEffectiveDate", compassDateFormat.format(getMemberInfoBean().getSalaryEffectiveDate()));
			enrollmentRequest.put("SalaryEffectiveDateEmail", getMemberInfoBean().getFormatSalaryDate());
		}
		if (StringUtils.isNotEmpty(getMemberInfoBean().getDepartment())) {
			enrollmentRequest.put("DepartmentDescription", getMemberInfoBean().getDepartment());
			enrollmentRequest.put("DepartmentDecode", getMemberInfoBean().getDepartmentDecode());
		}
		if (getMemberInfoBean().isNeedFacilityID()) {
			String debugMessage = "Member Facility Error: Member Name: " + getMemberInfoBean().getFirstName() + " " + getMemberInfoBean().getLastName();
			String docKey = getFacilityHelperBean().retrieveOrInsertDoctorKey(getMemberInfoBean().getFacilityId(), new Date(), getUserBean().getUserName(), debugMessage, getPolicyValueBean().getSelectedBillGroup(),
					getPolicyValueBean().getSelectedPolicyNumber());

			enrollmentRequest.put("DoctorKey", docKey);
			enrollmentRequest.put("FacilityIdEmail", getMemberInfoBean().getFacilityId());
			enrollmentRequest.put("EstablishedPatient", getMemberInfoBean().isHaveEstablishedPatient());
			enrollmentRequest.put("EstablishedPatientDecode", getMemberInfoBean().getEstablishedPatientDecode());
			enrollmentRequest.put("AsdoValRsltCd", getMemberInfoBean().getAsdoValRsltCd());
			enrollmentRequest.put("AsdoStatCd", "00");
		}

		/*
		 * If we have PPD or VolDental Coverage, we need to make sure the hire
		 * date is no more than 2 months from the current date. If so, we need
		 * to send a manual enrollment. But if we don't have a HireDate at all
		 * then we have bigger issues and the enrollment will fail while trying
		 * to install on COMPASS, in which case an email is already being sent
		 * so don't bother with this.
		 */
		if (getMemberInfoBean().getPartTimeHireDate() != null || getMemberInfoBean().getFullTimeHireDate() != null) {
			if (sendManualEnrollment(getMemberInfoBean().getCoveragesList())) {
				enrollmentRequest.put("EmailEnrollment", "");
			}
		}

		if (getMemberInfoBean().hasEmployeeSmokerCoverage()) {
			enrollmentRequest.put("Smoker", getMemberInfoBean().getSmokerStatus().equals("0") ? "Non-smoker" : "Smoker");
			enrollmentRequest.put("SmokerCode", getMemberInfoBean().getSmokerStatus());
		} else {
			enrollmentRequest.put("Smoker", "NotApplicable");
			enrollmentRequest.put("SmokerCode", "2");
		}

		if (getMemberInfoBean().isSignatureDateRequired() || getMemberInfoBean().getSignatureDate() != null) {
			enrollmentRequest.put("SignatureDateFormatted", employerDateFormat.format(getMemberInfoBean().getSignatureDate()));
		}

		if (StringUtils.isNotEmpty(getMemberInfoBean().getComments())) {
			enrollmentRequest.put("Comment", getMemberInfoBean().getComments());
		}

		if (getMemberInfoBean().isHaveAddress()) {
			enrollmentRequest.put("AddrRec", buildAddressRecord());
		}

		if (getMemberInfoBean().getCoveragesList() != null) {
			enrollmentRequest.put("Benefit", buildBenefitRecord());
		}

		if (getMemberInfoBean().getDependentList() != null && !getMemberInfoBean().getDependentList().isEmpty()) {
			JSONArray dependentRecord = buildDependentRecord();
			if (dependentRecord.length() > 0) {
				enrollmentRequest.put("Dependent", buildDependentRecord());

				if (getMemberInfoBean().getEmploymentStatus().equalsIgnoreCase("P") && getMemberInfoBean().getPartTimeHireDate() != null) {
					enrollmentRequest.put("DpndntEffDate", compassDateFormat.format(getMemberInfoBean().getPartTimeHireDate()));
				} else if (getMemberInfoBean().getFullTimeHireDate() != null) {
					enrollmentRequest.put("DpndntEffDate", compassDateFormat.format(getMemberInfoBean().getFullTimeHireDate()));
				}
			}
		}

		getUserBean().createTimestamp();

		return enrollmentRequest;
	}

	/**
	 * If the enrollment is for a Prepaid or Voluntary Dental and the hire date
	 * is more than two months from the current date (for capitation) we need to
	 * send an e-mail to Customer Advocacy to work the enrollment manually. <br/>
	 * NOTE: If both the full time hire date and part time hire date are null we
	 * will not make it here, so initializing the hire date to null should be
	 * okay.
	 * 
	 * @param coverageList
	 *            the coverage list
	 * @return true, if successful
	 */
	private boolean sendManualEnrollment(List<Coverage> coverageList) {
		Date today = new Date();
		DateFormat dateFormat = new SimpleDateFormat("yyyyMMdd");
		Date hireDate = null;
		boolean isManualCoverageElected = false;

		try {
			/* First check for PPD or Vol Dental Coverage. */
			if (coverageList != null) {
				for (Coverage coverage : coverageList) {
					if (coverage.getBenefitShortName().equalsIgnoreCase(Coverage.SHORT_NAME_PREPAID_EMPLOYEE_DENTAL) || coverage.getBenefitShortName().equalsIgnoreCase(Coverage.SHORT_NAME_VOLUNTARY_EMPLOYEE_DENTAL)) {
						if (!coverage.getCoverageAmount().equalsIgnoreCase("Decline")) {
							isManualCoverageElected = true;
							break;
						}
					}
				}
			}

			// Now check the hire date.
			if (isManualCoverageElected) {
				Date currentDate = dateFormat.parse(dateFormat.format(today));
				if (getMemberInfoBean().getFullTimeHireDate() != null) {
					hireDate = dateFormat.parse(dateFormat.format(getMemberInfoBean().getFullTimeHireDate()));
				} else if (getMemberInfoBean().getPartTimeHireDate() != null) {
					hireDate = dateFormat.parse(dateFormat.format(getMemberInfoBean().getPartTimeHireDate()));
				} else {
					/*
					 * To get here both full time hire date and part time hire
					 * date are null, this should not happen but if it does
					 * return true for a manual enrollment. See the note in the
					 * method header.
					 */
					logger.error("Hire Date for enrollment was null.");
					return true;
				}

				Date latestValidHireDate = DateUtils.addMonths(currentDate, -2);

				if (hireDate.compareTo(latestValidHireDate) == 0) {
					return false;
				}

				if (hireDate.before(latestValidHireDate)) {
					return true;
				}
			}

		} catch (ParseException pe) {
			logger.error("buildEnrollment: ParseException received while comparing dates in sendManualEnrollment(), returning true for manual enrollment.", pe);
			return true;
		}

		return false;
	}

	/**
	 * Builds the address record.
	 * 
	 * @return the jSON object
	 * @throws JSONException
	 *             the jSON exception
	 */
	private JSONObject buildAddressRecord() throws JSONException {
		JSONObject addressRecord = new JSONObject();

		if (StringUtils.isNotEmpty(getMemberInfoBean().getAddrLine1())) {
			addressRecord.put("Line1", getMemberInfoBean().getAddrLine1());
		}
		if (StringUtils.isNotEmpty(getMemberInfoBean().getAddrLine2())) {
			addressRecord.put("Line2", getMemberInfoBean().getAddrLine2());
		}
		if (StringUtils.isNotEmpty(getMemberInfoBean().getZipCode())) {
			addressRecord.put("ZipCode", getMemberInfoBean().getZipCode());
		}
		if (StringUtils.isNotEmpty(getMemberInfoBean().getCity())) {
			addressRecord.put("City", getMemberInfoBean().getCity());
		}
		if (StringUtils.isNotEmpty(getMemberInfoBean().getState())) {
			addressRecord.put("State", getMemberInfoBean().getState());
		}
		if (StringUtils.isNotEmpty(getMemberInfoBean().getCountry())) {
			addressRecord.put("Country", getMemberInfoBean().getCountry());
		}

		return addressRecord;
	}

	/**
	 * Builds the benefit record. <br/>
	 * Rules for the benefits: <br/>
	 * 1) If the coverage amount is Decline, we don't want to send it over. <br/>
	 * 2) If we don't have a dollar amount, don't populate BenefitAmount. <br/>
	 * 3) If we don't have a NARL Key, don't populate the NarlKey. <br/>
	 * 
	 * @return the JSONArray for the benefit record
	 * @throws JSONException
	 *             the jSON exception
	 */
	private JSONArray buildBenefitRecord() throws JSONException {
		JSONArray benefitArray = new JSONArray();

		DateFormat compassDateFormat = new SimpleDateFormat(COMPASS_DATE_FORMAT);
		DateFormat employerDateFormat = new SimpleDateFormat(EMPLOYER_DATE_FORMAT);

		for (Coverage coverage : getMemberInfoBean().getCoveragesList()) {
			JSONObject memberBenefit = new JSONObject();

			/* If the coverage is declined ignore it. */
			if (coverage.getCoverageAmount().equalsIgnoreCase("Decline")) {
				continue;
			}

			memberBenefit.put("BenefitKey", coverage.getBenefitKey());

			/* Set the effective date. */
			if (getMemberInfoBean().isSignatureDateRequired() || getMemberInfoBean().getSignatureDate() != null) {
				memberBenefit.put("SignatureDate", compassDateFormat.format(getMemberInfoBean().getSignatureDate()));
				memberBenefit.put("SignatureDateEmail", employerDateFormat.format(getMemberInfoBean().getSignatureDate()));
			} else {
				if (getMemberInfoBean().getEmploymentStatus().equalsIgnoreCase("P") && getMemberInfoBean().getPartTimeHireDate() != null) {
					memberBenefit.put("SignatureDate", compassDateFormat.format(getMemberInfoBean().getPartTimeHireDate()));
					memberBenefit.put("SignatureDateEmail", employerDateFormat.format(getMemberInfoBean().getPartTimeHireDate()));
				} else if (getMemberInfoBean().getFullTimeHireDate() != null) {
					memberBenefit.put("SignatureDate", compassDateFormat.format(getMemberInfoBean().getFullTimeHireDate()));
					memberBenefit.put("SignatureDateEmail", employerDateFormat.format(getMemberInfoBean().getFullTimeHireDate()));
				}
			}

			/* Set benefit amount. */
			if (coverage.isTimesEarnings()) {
				String noFormat = StringUtils.substringBefore(coverage.getCoverageAmount(), " ");
				memberBenefit.put("BenefitAmount", noFormat += ".0");
			} else if (!coverage.getCoverageAmount().equalsIgnoreCase("Elected") && !coverage.getCoverageAmount().equalsIgnoreCase("Elect")) {
				String noDollarSign = StringUtils.remove(coverage.getCoverageAmount(), "$");
				String noFormat = StringUtils.remove(noDollarSign, ",");
				memberBenefit.put("BenefitAmount", noFormat);
			}
			memberBenefit.put("BenefitAmountEmail", coverage.getCoverageAmount());

			/* Set benefit description and key. */
			if (StringUtils.isNotEmpty(coverage.getNarlKey())) {
				memberBenefit.put("NarlKey", coverage.getNarlKey());
			}
			if (StringUtils.isNotEmpty(coverage.getDescription())) {
				memberBenefit.put("BenefitDescription", coverage.getDescription());
			}
			benefitArray.put(memberBenefit);
		}

		return benefitArray;
	}

	/**
	 * Builds the dependent record.
	 * 
	 * @return the jSON array
	 * @throws JSONException
	 *             the jSON exception
	 */
	private JSONArray buildDependentRecord() throws JSONException {
		JSONArray dependentArray = new JSONArray();

		DateFormat compassDateFormat = new SimpleDateFormat(COMPASS_DATE_FORMAT);
		DateFormat employerDateFormat = new SimpleDateFormat(EMPLOYER_DATE_FORMAT);

		if (getMemberInfoBean().isDependentRequired()) {
			for (DependentBean dependent : getMemberInfoBean().getDependentList()) {
				JSONObject memberDependent = new JSONObject();

				memberDependent.put("FirstName", dependent.getFirstName());
				memberDependent.put("LastName", dependent.getLastName());
				if (dependent.getDateOfBirth() != null) {
					memberDependent.put("BirthDate", compassDateFormat.format(dependent.getDateOfBirth()));
					memberDependent.put("BirthDateFormatted", dependent.getFormatDateOfBirth());
				}
				memberDependent.put("GenderCode", dependent.getGender());
				memberDependent.put("GenderDecode", dependent.getGenderDecode());
				if (dependent.getRelationship().equalsIgnoreCase(EmployerConstants.DOMESTIC_PARTNER_CODE)) {
					memberDependent.put("RelationshipCode", EmployerConstants.SPOUSE_CODE);
					memberDependent.put("RelationshipDecode", "Spouse");
				} else {
					memberDependent.put("RelationshipCode", dependent.getRelationship());
					memberDependent.put("RelationshipDecode", dependent.getRelationshipDecode());
				}
				memberDependent.put("OptTypeCode", EmployerConstants.OPT_TYPE_CODE);
				memberDependent.put("OptTypeDecode", EmployerConstants.OPT_TYPE_DECODE);

				/*
				 * Set the effective date of the dependent to the hire date of
				 * the member.
				 */
				if (getMemberInfoBean().getEmploymentStatus().equalsIgnoreCase("P") && getMemberInfoBean().getPartTimeHireDate() != null) {
					memberDependent.put("EffectiveDate", compassDateFormat.format(getMemberInfoBean().getPartTimeHireDate()));
					memberDependent.put("EffectiveDateEmail", employerDateFormat.format(getMemberInfoBean().getPartTimeHireDate()));
				} else if (getMemberInfoBean().getFullTimeHireDate() != null) {
					memberDependent.put("EffectiveDate", compassDateFormat.format(getMemberInfoBean().getFullTimeHireDate()));
					memberDependent.put("EffectiveDateEmail", employerDateFormat.format(getMemberInfoBean().getFullTimeHireDate()));
				}

				if (StringUtils.isNotEmpty(dependent.getMiddleName())) {
					memberDependent.put("MiddleName", dependent.getMiddleName());
				}
				if (StringUtils.isNotEmpty(dependent.getSsn())) {
					memberDependent.put("SSN", dependent.getSsn());
				}
				if (StringUtils.isNotEmpty(dependent.getSuffix())) {
					memberDependent.put("NameSuffix", dependent.getSuffix());
				}

				if (dependent.isNeedFacilityID()) {
					String debugMessage = "Dependent Facility Error: Member Name: " + getMemberInfoBean().getFirstName() + " " + getMemberInfoBean().getLastName() + " Dependent Name: " + dependent.getFirstName() + " " + dependent.getLastName();

					String docKey = getFacilityHelperBean().retrieveOrInsertDoctorKey(dependent.getFacilityId(), new Date(), getUserBean().getUserName(), debugMessage, getPolicyValueBean().getSelectedBillGroup(),
							getPolicyValueBean().getSelectedPolicyNumber());

					memberDependent.put("DoctorKey", docKey);
					memberDependent.put("FacilityIdEmail", dependent.getFacilityId());
					memberDependent.put("EstablishedPatient", dependent.isEstablishedPatient());
					memberDependent.put("EstablishedPatientDecode", dependent.getEstablishedPatientDecode());
					memberDependent.put("AsdoValRsltCd", dependent.getAsdoValRsltCd());
					memberDependent.put("AsdoStatCd", "00");
				}

				if ((dependent.getRelationship().equals(EmployerConstants.DEPENDENT_SPOUSE) || dependent.getRelationship().equals(EmployerConstants.DEPENDENT_DOMESTICPARTNER)) && getMemberInfoBean().hasDependentSmokerCoverage()) {
					memberDependent.put("Smoker", dependent.getSmokerCode().equals("0") ? "Non-smoker" : "Smoker");
					memberDependent.put("SmokerCode", dependent.getSmokerCode());
				}

				if (StringUtils.isNotEmpty(dependent.getPreferredLanguage())) {
					memberDependent.put("LanguagePreferenceCode", dependent.getPreferredLanguage());
					memberDependent.put("LanguagePreferenceDecode", dependent.getPreferredLanguageDecode());
				} else {
					/* Default to English. */
					memberDependent.put("LanguagePreferenceCode", "1");
					memberDependent.put("LanguagePreferenceDecode", dependent.getPreferredLanguageDecode());
				}
				dependentArray.put(memberDependent);
			}
		} else {
			/*
			 * If no dependents are required but there are dependents something
			 * has gone wrong. It should not be possible to get here, but just
			 * to be safe remove the dependents.
			 */
			getMemberInfoBean().getDependentList().clear();
		}

		return dependentArray;
	}
	
	/******************************************************************************************************************
	 * Method: buildCareEventObject
	 * Parameters: none 
	 * Return: JSONObject
	 * Description:	builds a careEvent object for mits event processing
	 ******************************************************************************************************************/
	private JSONObject buildCareEventObject(){
		DateFormat employerDateFormat = new SimpleDateFormat(EMPLOYER_DATE_FORMAT);
		JSONObject careObject = new JSONObject();

		if (getMemberInfoBean().getDateOfBirth() != null) {
			careObject.put("dateOfBirth", getMemberInfoBean().getFormatDateOfBirth());			
		}
		if (getMemberInfoBean().isSignatureDateRequired() || getMemberInfoBean().getSignatureDate() != null) {
			careObject.put("signatureDate", employerDateFormat.format(getMemberInfoBean().getSignatureDate()));
		}

		careObject.put("gender", getMemberInfoBean().getGenderDecode());
		careObject.put("memberGroupName", getMemberInfoBean().getMemberGroupName());
		careObject.put("memberGroupKey", getMemberInfoBean().getMemberGroupKey());
		careObject.put("billGroupName", getPolicyValueBean().getSelectedBillGroup());
		careObject.put("targetSystem", TransactionTypes.TRGT_SYSTM_CD_COMPASS);
		careObject.put("sessionId", getUserBean().getSessionId());
		return careObject;
	}


	//---------------------------------------------------------------------------------------------------------------------
	// Getters/Setters
	//---------------------------------------------------------------------------------------------------------------------
	public EnrollMemberWizardDataBean getEnrollMemberWizardDataBean() {
		return enrollMemberWizardDataBean;
	}
	public void setEnrollMemberWizardDataBean(EnrollMemberWizardDataBean enrollMemberWizardDataBean) {
		this.enrollMemberWizardDataBean = enrollMemberWizardDataBean;
	}

	public MemberInfoBean getMemberInfoBean() {
		return memberInfoBean;
	}
	public void setMemberInfoBean(MemberInfoBean memberInfoBean) {
		this.memberInfoBean = memberInfoBean;
	}

	public PolicyValueBean getPolicyValueBean() {
		return policyValueBean;
	}
	public void setPolicyValueBean(PolicyValueBean policyValueBean) {
		this.policyValueBean = policyValueBean;
	}

	public PolicyInfoDataBean getPolicyInfoDataBean() {
		return policyInfoDataBean;
	}
	public void setPolicyInfoDataBean(PolicyInfoDataBean policyInfoDataBean) {
		this.policyInfoDataBean = policyInfoDataBean;
	}

	public FacilityHelperBean getFacilityHelperBean() {
		return facilityHelperBean;
	}
	public void setFacilityHelperBean(FacilityHelperBean facilityHelperBean) {
		this.facilityHelperBean = facilityHelperBean;
	}

	public UserBean getUserBean() {
		return userBean;
	}
	public void setUserBean(UserBean userBean) {
		this.userBean = userBean;
	}

	public PolicyFormsBean getPolicyFormsBean() {
		return policyFormsBean;
	}
	public void setPolicyFormsBean(PolicyFormsBean policyFormsBean) {
		this.policyFormsBean = policyFormsBean;
	}

	public VarianceDetailHelperBean getVarianceDetailHelperBean() {
		return varianceDetailHelperBean;
	}
	public void setVarianceDetailHelperBean(VarianceDetailHelperBean varianceDetailHelperBean) {
		this.varianceDetailHelperBean = varianceDetailHelperBean;
	}
}
