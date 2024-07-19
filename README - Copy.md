public ResponseWrapper<ByteWrapper> generateMcsForMember(MCSRequestDTO mcsRequest, ACOMSRequest acomsRequest, HttpServletRequest httpRequest) {
        ResponseWrapper<ByteWrapper> responseWrapper;
        try {
            URIBuilder uriBuilder = new URIBuilder(getServiceUrl());
            String path = uriBuilder.build().getPath() + "/billing/" +
                    "mcs/" + "member/" +
                    acomsRequest.getInsuranceSystem().name() + "/" +
                    acomsRequest.getRole().name() + "/" +
                    acomsRequest.getUserName() + "/" +
                    acomsRequest.getSesnGuid() + "/" +
                    acomsRequest.getCommunicationChnnl().name();

            String reportName = mcsRequest.getReportName();
            if (reportName == null) {
                reportName = "";
            }

            uriBuilder.setPath(path)
                    .addParameter("policyNumber", mcsRequest.getPolicyNumber())
                    .addParameter("billGroups", mcsRequest.getBillGroupString())
                    .addParameter("caseMemberKeys", mcsRequest.getCaseMemberKeysString())
                    .addParameter("memberNumber", mcsRequest.getMemberNumber())
                    .addParameter("deductionFrequencies", DeductionFrequency.getFrequenciesAsString(mcsRequest.getDeductionFrequencies()))
                    .addParameter("effectiveDate", mcsRequest.getEffectiveDate())
                    .addParameter("reportName", URLEncoder.encode(reportName, StandardCharsets.UTF_8))
                    .addParameter("reportTypeName", mcsRequest.getReportType().name())
                    .addParameter("externalViewable", String.valueOf(mcsRequest.isExternalViewable()))
                    .addParameter("externalUser", String.valueOf(mcsRequest.isExternalUser()));

            String baseReqUrl = uriBuilder.build().toURL().toString();


            HttpUriRequest uriRequest = RequestBuilder.get().setUri(baseReqUrl).addHeader("Content-Type", "application/xml").addHeader("cookie", getSiteminderCookie(httpRequest)).build();

            logger.info("URI for generateMcsForMember  : {}", uriRequest.getURI());
            HttpClient defaultHttpClient = HttpClientBuilder.create().build();

            HttpResponse httpResponse = defaultHttpClient.execute(uriRequest);

            responseWrapper = handleResponse(httpResponse);

            String payload = new String((responseWrapper.getPayload()).bytes);
            int start = payload.indexOf("<bytes>");
            int end = payload.indexOf("</bytes>");
            String justPDF = payload.substring(start + "<bytes>".length(), end);
            Base64 decoder = new Base64();
            byte[] decodedPDF = decoder.decode(justPDF);
            responseWrapper.setPayload(new ByteWrapper(decodedPDF));
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
        return responseWrapper;
    }

    // Implementation of this method to be used from acoms-client jar
    public ResponseWrapper<ByteWrapper> generateMcsForGroup(MCSRequestDTO mcsRequest, ACOMSRequest acomsRequest, HttpServletRequest httpRequest) {
        return null;
    }

    private ResponseWrapper<ByteWrapper> handleResponse(HttpResponse httpResponse) throws Exception {
        ResponseWrapper<ByteWrapper> responseWrapper;
        int statusCode = httpResponse.getStatusLine().getStatusCode();

        if (statusCode >= 200 && statusCode < 300) {
            HttpEntity entity = httpResponse.getEntity();
            String response = EntityUtils.toString(entity, "UTF-8");
            responseWrapper = buildResponse(response);
        } else {

            throw new Exception("Error accessing ACOMS received error code: " + statusCode);
        }
        return responseWrapper;
    }

    public ResponseWrapper<ByteWrapper> buildResponse(String response) throws Exception {
        ResponseWrapper<ByteWrapper> responseWrapper = new ResponseWrapper<>();

        Document doc = DocumentBuilderFactory.newInstance().newDocumentBuilder().parse(new InputSource(new StringReader(response)));

        NodeList respNodes = doc.getElementsByTagName("responseWrapper");
        if (respNodes.getLength() > 0) {
            Element respElement = (Element) respNodes.item(0);

            String respCode = respElement.getElementsByTagName("responseCode").item(0).getTextContent();

            String respMessage = respElement.getElementsByTagName("responseMessage").item(0).getTextContent();
            if (respCode != null) {
                if (respCode.equalsIgnoreCase("error"))
                    responseWrapper.setResponseCode(ResponseCode.ERROR);
                if (respCode.equalsIgnoreCase("success"))
                    responseWrapper.setResponseCode(ResponseCode.SUCCESS);
                if (respCode.equalsIgnoreCase("warning")) {
                    responseWrapper.setResponseCode(ResponseCode.WARNING);
                }
            } else {
                responseWrapper.setResponseCode(ResponseCode.NOT_FOUND);
            }
            responseWrapper.setResponseMessage(respMessage);

            byte[] bytearray = response.getBytes(StandardCharsets.UTF_8);
            ByteWrapper payloadWrapper = new ByteWrapper(bytearray);

            responseWrapper.setPayload(payloadWrapper);
        }

        return responseWrapper;
    }



    import static org.mockito.Mockito.*;
import static org.junit.Assert.*;

import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpUriRequest;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.junit.Before;
import org.junit.Test;
import org.mockito.ArgumentCaptor;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import javax.servlet.http.HttpServletRequest;
import java.nio.charset.StandardCharsets;
import java.util.Base64;

public class YourClassTest {

    @Mock
    private HttpServletRequest httpRequest;

    @Mock
    private HttpClientBuilder httpClientBuilder;

    @Mock
    private CloseableHttpClient httpClient;

    @Mock
    private HttpResponse httpResponse;

    @InjectMocks
    private YourClass yourClass; // Replace with the actual class name containing generateMcsForMember method

    @Before
    public void setUp() throws Exception {
        MockitoAnnotations.initMocks(this);
        when(httpClientBuilder.build()).thenReturn(httpClient);
    }

    @Test
    public void testGenerateMcsForMember() throws Exception {
        // Mocking the required objects
        MCSRequestDTO mcsRequest = mock(MCSRequestDTO.class);
        ACOMSRequest acomsRequest = mock(ACOMSRequest.class);

        // Setting up the mock responses
        when(mcsRequest.getPolicyNumber()).thenReturn("policyNumber");
        when(mcsRequest.getBillGroupString()).thenReturn("billGroups");
        when(mcsRequest.getCaseMemberKeysString()).thenReturn("caseMemberKeys");
        when(mcsRequest.getMemberNumber()).thenReturn("memberNumber");
        when(mcsRequest.getDeductionFrequencies()).thenReturn(new ArrayList<>());
        when(mcsRequest.getEffectiveDate()).thenReturn("effectiveDate");
        when(mcsRequest.getReportName()).thenReturn("reportName");
        when(mcsRequest.getReportType()).thenReturn(ReportType.SOME_TYPE); // Replace SOME_TYPE with actual type
        when(mcsRequest.isExternalViewable()).thenReturn(true);
        when(mcsRequest.isExternalUser()).thenReturn(true);

        when(acomsRequest.getInsuranceSystem()).thenReturn(InsuranceSystem.SOME_SYSTEM); // Replace SOME_SYSTEM with actual system
        when(acomsRequest.getRole()).thenReturn(Role.SOME_ROLE); // Replace SOME_ROLE with actual role
        when(acomsRequest.getUserName()).thenReturn("userName");
        when(acomsRequest.getSesnGuid()).thenReturn("sesnGuid");
        when(acomsRequest.getCommunicationChnnl()).thenReturn(CommunicationChnnl.SOME_CHANNEL); // Replace SOME_CHANNEL with actual channel

        // Mocking HTTP response
        String mockResponse = "<responseWrapper><responseCode>success</responseCode><responseMessage>Message</responseMessage><bytes>SGVsbG8gd29ybGQ=</bytes></responseWrapper>";
        StringEntity entity = new StringEntity(mockResponse, StandardCharsets.UTF_8);
        when(httpResponse.getEntity()).thenReturn(entity);
        when(httpResponse.getStatusLine().getStatusCode()).thenReturn(200);
        when(httpClient.execute(any(HttpUriRequest.class))).thenReturn(httpResponse);

        // Running the test
        ResponseWrapper<ByteWrapper> responseWrapper = yourClass.generateMcsForMember(mcsRequest, acomsRequest, httpRequest);

        // Verifying the results
        assertNotNull(responseWrapper);
        assertEquals(ResponseCode.SUCCESS, responseWrapper.getResponseCode());
        assertEquals("Message", responseWrapper.getResponseMessage());

        // Verifying the payload
        byte[] decodedPDF = Base64.getDecoder().decode("SGVsbG8gd29ybGQ=");
        assertArrayEquals(decodedPDF, responseWrapper.getPayload().bytes);
    }
}
