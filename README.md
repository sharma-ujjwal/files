# files
local files added in this repo

      ## Xstream

      XStream xstream = new XStream(new JettisonMappedXmlDriver());
			xstream.alias("transactionReplyDTO", TransactionReplyDTO.class);
			xstream.addImplicitCollection(TransactionReplyDTO.class, "events", TransactionEventDTO.class);
			xstream.addPermission(NoTypePermission.NONE);
			xstream.addPermission(NullPermission.NULL);
			xstream.addPermission(PrimitiveTypePermission.PRIMITIVES);
			xstream.allowTypesByWildcard(new String[] {
					TransactionReplyDTO.class.getPackage().getName()+".*"
			});

     ##
     grep -r 'oaemployerc'



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




    import static org.mockito.Mockito.*;
import static org.junit.Assert.*;

import java.net.URLEncoder;
import java.nio.charset.StandardCharsets;
import javax.servlet.http.HttpServletRequest;

import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpUriRequest;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.mockito.junit.MockitoJUnitRunner;
import org.apache.http.client.utils.URIBuilder;

@RunWith(MockitoJUnitRunner.class)
public class YourServiceTest {

    @Mock
    private HttpServletRequest httpRequest;

    @Mock
    private HttpClientBuilder httpClientBuilder;

    @Mock
    private HttpClient httpClient;

    @Mock
    private HttpResponse httpResponse;

    @InjectMocks
    private YourService yourService;

    @Before
    public void setUp() throws Exception {
        MockitoAnnotations.initMocks(this);
        when(httpClientBuilder.build()).thenReturn(httpClient);
    }

    @Test
    public void testGenerateMcsForMember() throws Exception {
        MCSRequestDTO mcsRequest = new MCSRequestDTO();
        mcsRequest.setPolicyNumber("policyNumber123");
        mcsRequest.setBillGroupString("billGroups");
        mcsRequest.setCaseMemberKeysString("caseMemberKeys");
        mcsRequest.setMemberNumber("memberNumber");
        mcsRequest.setDeductionFrequencies(Arrays.asList(DeductionFrequency.MONTHLY));
        mcsRequest.setEffectiveDate("2023-01-01");
        mcsRequest.setReportName("reportName");
        mcsRequest.setReportType(ReportType.MONTHLY);
        mcsRequest.setExternalViewable(true);
        mcsRequest.setExternalUser(false);

        ACOMSRequest acomsRequest = new ACOMSRequest();
        acomsRequest.setInsuranceSystem(InsuranceSystem.SOME_SYSTEM);
        acomsRequest.setRole(Role.SOME_ROLE);
        acomsRequest.setUserName("userName");
        acomsRequest.setSesnGuid("sesnGuid");
        acomsRequest.setCommunicationChnnl(CommunicationChnnl.EMAIL);

        String expectedPayload = "<bytes>cGRmY29udGVudA==</bytes>";
        String baseReqUrl = new URIBuilder(yourService.getServiceUrl())
                .setPath("/billing/mcs/member/SOME_SYSTEM/SOME_ROLE/userName/sesnGuid/EMAIL")
                .addParameter("policyNumber", mcsRequest.getPolicyNumber())
                .addParameter("billGroups", mcsRequest.getBillGroupString())
                .addParameter("caseMemberKeys", mcsRequest.getCaseMemberKeysString())
                .addParameter("memberNumber", mcsRequest.getMemberNumber())
                .addParameter("deductionFrequencies", DeductionFrequency.getFrequenciesAsString(mcsRequest.getDeductionFrequencies()))
                .addParameter("effectiveDate", mcsRequest.getEffectiveDate())
                .addParameter("reportName", URLEncoder.encode(mcsRequest.getReportName(), StandardCharsets.UTF_8.toString()))
                .addParameter("reportTypeName", mcsRequest.getReportType().name())
                .addParameter("externalViewable", String.valueOf(mcsRequest.isExternalViewable()))
                .addParameter("externalUser", String.valueOf(mcsRequest.isExternalUser()))
                .build().toURL().toString();

        when(httpClient.execute(any(HttpUriRequest.class))).thenReturn(httpResponse);
        when(EntityUtils.toString(httpResponse.getEntity())).thenReturn(expectedPayload);

        ResponseWrapper responseWrapper = yourService.generateMcsForMember(mcsRequest, acomsRequest, httpRequest);

        assertNotNull(responseWrapper);
        assertNotNull(responseWrapper.getPayload());
        assertEquals("pdfcontent", new String(responseWrapper.getPayload().bytes));

        verify(httpClient, times(1)).execute(any(HttpUriRequest.class));
        verify(httpRequest, times(1)).getHeader("cookie");
    }
}
