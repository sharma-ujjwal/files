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