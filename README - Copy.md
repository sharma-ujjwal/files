	public String loadSingleMemberForEdit(String memberNumber, String memberLastName, String memberFirstName, String pagStartPos, String pagEndPos) {
		JSONObject response = null;
		MemberListRequestDTO request = new MemberListRequestDTO();
		request.setRequestUser(getUserBean().getUserName());
		request.setRequestApplication("OAc");
		request.setRequestDate(new Date());
		request.setPaginationStartPosition(pagStartPos);
		request.setPaginationEndPosition(pagEndPos);

		request.setBgrpKey(getPolicyValueBean().getSelectedBillGroupKey());
		request.setPolicyNumber(getPolicyValueBean().getSelectedPolicyNumber());
		request.setEffectiveDate(formatForMiddleware(new Date()));
		request.setResponseStructure(MemberListRequestDTO.DEPARTMENT);
		request.setFirstName(memberFirstName);
		request.setLastName(memberLastName);

		try {
			JSONObject formattedRequest = createJSONObjectRequest(request, getUserBean().getUserName(), "memberListManager");

			response = executeMiddlewareServiceRequest(formattedRequest);
		} catch (Exception e) {
			logger.error("loadSingleMemberForEdit() error {}", e);
		}

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
		if (response != null && response.toString().length() > 2) {
			MemberListResponseDTO memberListResponseDTO = (MemberListResponseDTO) xstream.fromXML(response.toString());
			if (memberListResponseDTO.getMemberList() == null) {
				return "";
			}

			for (MemberDTO memberDto : memberListResponseDTO.getMemberList()) {
				if (memberDto.getMemberNumber().equals(memberNumber)) {
					return getPaginationControlsBean().doSelectSingleMemberAction(memberDto.getCaseMbrKey(), memberDto.getClientId(), memberDto.getMemberGroupKey());
				}
			}

		}
		return "";
	}
	
	
	public JSONObject executeMiddlewareServiceRequest(JSONObject request) throws JSONException, IOException {
		JSONObject result = ServiceExecutor.executeRequest(employerIocLookup.getMiddlewareSrvcUrl(), request, null);

		// If the result does not have a Return Code or a Return Message then
		// something
		// bad has happened. In that case we will build an error message and
		// return.
		if (!result.has("returnCode") || !result.has("returnMessage") || !result.has("resultSet")) {
			logger.error("executeMiddlewareServiceRequest Error: Unknown Error. Request: {}", request);
			return new JSONObject();
		}

		// If the Return Code is -1 then there was an error so log the error.
		if (result.optString("returnCode").equals("-1")) {
			logger.error("executeMiddlewareServiceRequest Error: {} Request: {}", result.optString("returnMessage"), request);
			return new JSONObject();
		}

		return result.getJSONObject("resultSet");
	}



import static org.mockito.Mockito.*;
import static org.junit.Assert.*;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.MockitoJUnitRunner;
import org.mockito.MockitoAnnotations;

import java.util.Date;
import org.json.JSONObject;

@RunWith(MockitoJUnitRunner.class)
public class YourClassTest {

    @Mock
    private UserBean userBean;
    
    @Mock
    private PolicyValueBean policyValueBean;
    
    @Mock
    private PaginationControlsBean paginationControlsBean;
    
    @InjectMocks
    private YourClass yourClass; // Replace YourClass with the actual class name containing the method
    
    @Before
    public void setUp() {
        MockitoAnnotations.initMocks(this);
        when(userBean.getUserName()).thenReturn("testUser");
        when(policyValueBean.getSelectedBillGroupKey()).thenReturn("testBillGroupKey");
        when(policyValueBean.getSelectedPolicyNumber()).thenReturn("testPolicyNumber");
    }

    @Test
    public void testLoadSingleMemberForEdit() throws Exception {
        String memberNumber = "12345";
        String memberLastName = "Doe";
        String memberFirstName = "John";
        String pagStartPos = "0";
        String pagEndPos = "10";

        // Mocking the JSON response
        JSONObject mockResponse = new JSONObject();
        mockResponse.put("returnCode", "0");
        mockResponse.put("returnMessage", "Success");

        JSONObject resultSet = new JSONObject();
        // Assuming that the response structure includes a "memberList" field
        resultSet.put("memberList", "[{\"memberNumber\":\"12345\", \"caseMbrKey\":\"case123\", \"clientId\":\"client123\", \"memberGroupKey\":\"group123\"}]");
        mockResponse.put("resultSet", resultSet);

        // Mocking the executeMiddlewareServiceRequest method
        when(yourClass.executeMiddlewareServiceRequest(any(JSONObject.class))).thenReturn(resultSet);

        // Mocking the doSelectSingleMemberAction method
        when(paginationControlsBean.doSelectSingleMemberAction("case123", "client123", "group123")).thenReturn("success");

        String result = yourClass.loadSingleMemberForEdit(memberNumber, memberLastName, memberFirstName, pagStartPos, pagEndPos);
        assertEquals("success", result);
    }
}	
