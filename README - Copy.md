public void submitSingleMcsRequest() {
	final String REQUESTING_APPLICATION = "OAc";
	final String DUPLICATE_REQUEST_RESPONSE_MSG = "Duplicate pending request";
	
	StopWatch stopWatch = new StopWatch();
	long timeToBuildRequest = 0;
	long timeToMakeRequest = 0;
	long timeToProcessResponse = 0;
	
	// log request build timing
	if (logger.isTraceEnabled()) {
		stopWatch.start();
	}
	
	MCSRequestDTO mcsRequest = new MCSRequestDTO();
			
	//
	// set billgroups on request
	//
	List<String> selectedBillGroupKeys = new ArrayList<>();
	for(MCSCompassPolicyAuthorization billGroup : getMemberCoverageStatementDataBean().getMatchedPolicyAuthorizations()) {
		if (billGroup.isSelectedForMCS()) {
			selectedBillGroupKeys.add(billGroup.getBillGroupKey());
		}
	}		
	mcsRequest.setBillGroups(selectedBillGroupKeys);
	
	//
	// set members on request
	//
	String memberNumber = null;
	for (MemberCoverageStatementMember mbr : getMemberCoverageStatementDataBean().getSelectedMembers().values()) {
		memberNumber = mbr.getMemberNumber();
	}
			
	mcsRequest.setMemberNumber(memberNumber);
	mcsRequest.setAllMembers(false);
	ArrayList<String>caseMemberKeys = new ArrayList<>(getMemberCoverageStatementDataBean().getSelectedMembers().keySet());
	mcsRequest.setCaseMemberKeys(caseMemberKeys);
	
	//
	// set deduction frequencies on request	
	//		
	List<DeductionFrequency> frequenices = new ArrayList<>(4);
	if (getMemberCoverageStatementDataBean().isFreqBiWeekly()) {
		frequenices.add(DeductionFrequency.BI_WEEKLY);  		
	}
	if (getMemberCoverageStatementDataBean().isFreqWeekly()) {
		frequenices.add(DeductionFrequency.WEEKLY);		
	}
	if (getMemberCoverageStatementDataBean().isFreqSemiMonthly()) { 
		frequenices.add(DeductionFrequency.SEMI_MONTHLY);
	}		
	if (getMemberCoverageStatementDataBean().isFreqMonthly()) { 
		frequenices.add(DeductionFrequency.MONTHLY);
	}	
	mcsRequest.setDeductionFrequencies(frequenices);
						
	//
	// External/internal user, and externally viewable
	// 
	// NOTE mcsExternalView flag is hard set to false, until such time as we make the option selectable via the user interface
	//
	mcsRequest.setExternalUser(!getUserBean().isInternalAccess());
	boolean mcsExternalView = false;
	mcsRequest.setExternalViewable(getMemberCoverageStatementRulesBean().determineMcsExternalView(getUserBean().isInternalAccess(), mcsExternalView));		
	
	//
	// Member filter criteria
	//			
	mcsRequest.setMemberFilterCriteria(null);
	
	//
	// set report name, type, and date on request
	//
	mcsRequest.setReportName(getMemberCoverageStatementDataBean().getReportName());
	mcsRequest.setEffectiveDate(new SimpleDateFormat("yyyyMMdd").format(getMemberCoverageStatementDataBean().getReportDate()));
	mcsRequest.setReportType(ReportType.MCS_EDIT_MEMBER);
			
	//
	// miscellaneous
	//
	mcsRequest.setPolicyNumber(getPolicyValueBean().getSelectedPolicyNumber());
//TODO		mcsRequest.setRequestApplication(REQUESTING_APPLICATION);
//TODO		mcsRequest.setRequestUser(userBean.getUserName());
	mcsRequest.setCreatedDate(new SimpleDateFormat("yyyyMMdd").format(new Date()));		

	// Capture request build time
	if (logger.isTraceEnabled()) {
		timeToBuildRequest = stopWatch.getTime();
	}		

	//
	// submit request
	//		
	ResponseWrapper<ByteWrapper> response = new ResponseWrapper<>();
	try {
		DocumentServiceClientImpl documentServiceClient = new DocumentServiceClientImpl(getAcomsUrl());
		response = documentServiceClient.generateMcsForMember(mcsRequest,
															  userBean.getAcomsRq(), 
															  (HttpServletRequest) FacesContext.getCurrentInstance().getExternalContext().getRequest());

		// Capture request time
		if (logger.isTraceEnabled()) {
			timeToMakeRequest = stopWatch.getTime();
		}			
					
		logger.debug("MCS request for member submitted. Parameters -- {}", formatRequestParams(mcsRequest));
		
		//
		// handle response
		//
		if (response == null) {
			returnErrors(MCS_FAILURE_MESSAGE);           	
			logger.error("MCS request for member failure; status is unknown or not available. Parameters -- {}", formatRequestParams(mcsRequest));
		}
		else if (response.getResponseCode() == ResponseCode.SUCCESS) { 
			ResponseWrapper rw = extractAndReturnDocument(response.getPayload());
			if (rw.getResponseCode() == ResponseCode.ERROR) {
				returnErrors(MCS_FAILURE_MESSAGE);
				logger.error("MCS request for member error; Response code: {} Response message: {} Parameters -- {}",
						rw.getResponseCode(), rw.getResponseMessage(), formatRequestParams(mcsRequest));
			}
		} 
		else if (response.getResponseCode() == ResponseCode.CANCELED) {
			if (DUPLICATE_REQUEST_RESPONSE_MSG.equals(response.getResponseMessage())) {
				returnErrors(MCS_DUPLICATE_REQUEST_MESSAGE);
				logger.error("MCS request for member canceled; Response code: {} Response message: {} Parameters -- {}",
						response.getResponseCode(), response.getResponseMessage(), formatRequestParams(mcsRequest));
			}
		}
		else {
			returnErrors(MCS_FAILURE_MESSAGE);
			logger.error("MCS request for member error; Response code: {} Response message: {} Parameters -- {}",
					response.getResponseCode(), response.getResponseMessage(), formatRequestParams(mcsRequest));
		}
	}
	catch(Exception e) {
		returnErrors(MCS_FAILURE_MESSAGE);
		logger.error("MCS request for member error; Response code:" + response.getResponseCode() + ". Response message:" + response.getResponseMessage() + ". Parameters --" + formatRequestParams(mcsRequest), e);
	}
	
	// Capture process request time
	if (logger.isTraceEnabled()) {
		timeToProcessResponse = stopWatch.getTime();
	}
	
	// log timing info
	if (logger.isTraceEnabled()) {
		stopWatch.stop();
		
		logger.trace("Build MCS single request took {} milliseconds", timeToBuildRequest);
		logger.trace("Make MCS single request took {} milliseconds", timeToMakeRequest - timeToBuildRequest);
		logger.trace("Process single batch response took {} milliseconds", timeToProcessResponse - timeToMakeRequest);
	}		
}

import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

import java.text.SimpleDateFormat;
import java.util.*;

import javax.servlet.http.HttpServletRequest;

import org.apache.commons.lang3.time.StopWatch;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@ExtendWith(MockitoExtension.class)
public class MemberCoverageStatementHelperBeanTest {

    @Mock
    private UserBean userBean;

    @Mock
    private MemberCoverageStatementDataBean memberCoverageStatementDataBean;

    @Mock
    private PolicyInfoDataBean policyInfoDataBean;

    @Mock
    private PolicyValueBean policyValueBean;

    @Mock
    private MemberCoverageStatementRulesBean memberCoverageStatementRulesBean;

    @Mock
    private DocumentServiceClientImpl documentServiceClient;

    @InjectMocks
    private MemberCoverageStatementHelperBean memberCoverageStatementHelperBean;

    private static final Logger logger = LoggerFactory.getLogger(MemberCoverageStatementHelperBean.class);

    @BeforeEach
    void setup() {
        when(memberCoverageStatementDataBean.getMatchedPolicyAuthorizations()).thenReturn(new ArrayList<>());
        when(memberCoverageStatementDataBean.getSelectedMembers()).thenReturn(new HashMap<>());
        when(userBean.isInternalAccess()).thenReturn(true);
    }

    @Test
    void testSubmitSingleMcsRequest_Success() {
        // Arrange
        when(userBean.isInternalAccess()).thenReturn(false);
        when(userBean.getAcomsRq()).thenReturn("someAcomsRq");

        ResponseWrapper<ByteWrapper> mockResponse = new ResponseWrapper<>();
        mockResponse.setResponseCode(ResponseCode.SUCCESS);
        when(documentServiceClient.generateMcsForMember(any(MCSRequestDTO.class), anyString(), any(HttpServletRequest.class)))
                .thenReturn(mockResponse);

        // Act
        memberCoverageStatementHelperBean.submitSingleMcsRequest();

        // Assert
        verify(documentServiceClient).generateMcsForMember(any(MCSRequestDTO.class), anyString(), any(HttpServletRequest.class));
    }

    @Test
    void testSubmitSingleMcsRequest_DuplicateRequest() {
        // Arrange
        when(userBean.isInternalAccess()).thenReturn(false);
        when(userBean.getAcomsRq()).thenReturn("someAcomsRq");

        ResponseWrapper<ByteWrapper> mockResponse = new ResponseWrapper<>();
        mockResponse.setResponseCode(ResponseCode.CANCELED);
        mockResponse.setResponseMessage("Duplicate pending request");
        when(documentServiceClient.generateMcsForMember(any(MCSRequestDTO.class), anyString(), any(HttpServletRequest.class)))
                .thenReturn(mockResponse);

        // Act
        memberCoverageStatementHelperBean.submitSingleMcsRequest();

        // Assert
        verify(documentServiceClient).generateMcsForMember(any(MCSRequestDTO.class), anyString(), any(HttpServletRequest.class));
        // Additional assertions to check for handling the duplicate request case can be added here
    }

    @Test
    void testSubmitSingleMcsRequest_Error() {
        // Arrange
        when(userBean.isInternalAccess()).thenReturn(false);
        when(userBean.getAcomsRq()).thenReturn("someAcomsRq");

        ResponseWrapper<ByteWrapper> mockResponse = new ResponseWrapper<>();
        mockResponse.setResponseCode(ResponseCode.ERROR);
        when(documentServiceClient.generateMcsForMember(any(MCSRequestDTO.class), anyString(), any(HttpServletRequest.class)))
                .thenReturn(mockResponse);

        // Act
        memberCoverageStatementHelperBean.submitSingleMcsRequest();

        // Assert
        verify(documentServiceClient).generateMcsForMember(any(MCSRequestDTO.class), anyString(), any(HttpServletRequest.class));
        // Additional assertions to check for handling the error case can be added here
    }

    @Test
    void testSubmitSingleMcsRequest_Exception() {
        // Arrange
        when(userBean.isInternalAccess()).thenReturn(false);
        when(userBean.getAcomsRq()).thenReturn("someAcomsRq");

        when(documentServiceClient.generateMcsForMember(any(MCSRequestDTO.class), anyString(), any(HttpServletRequest.class)))
                .thenThrow(new RuntimeException("Service error"));

        // Act
        memberCoverageStatementHelperBean.submitSingleMcsRequest();

        // Assert
        verify(documentServiceClient).generateMcsForMember(any(MCSRequestDTO.class), anyString(), any(HttpServletRequest.class));
        // Additional assertions to check for handling the exception case can be added here
    }
}
