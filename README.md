

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




public String doSaveDependentEdits() {
    if (validateDependent()) {
		passedDpndntValidation = true;

		return getAuthorizationHelperBean().getForwardAuthorization("navEnrollMemberSummary");
	}

	setTrackChanges(false);
	return null;
}




 import static org.junit.Assert.*;
import static org.mockito.Mockito.*;

import org.junit.Before;
import org.junit.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

public class YourClassTest {

    @InjectMocks
    private YourClass yourClass;

    @Mock
    private AuthorizationHelperBean authorizationHelperBean;

    @Before
    public void setUp() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    public void testDoSaveDependentEdits_ValidationPasses() {
        // Arrange
        when(yourClass.validateDependent()).thenReturn(true);
        when(authorizationHelperBean.getForwardAuthorization("navEnrollMemberSummary")).thenReturn("expectedForward");

        // Act
        String result = yourClass.doSaveDependentEdits();

        // Assert
        assertEquals("expectedForward", result);
        assertTrue(yourClass.passedDpndntValidation);
    }

    @Test
    public void testDoSaveDependentEdits_ValidationFails() {
        // Arrange
        when(yourClass.validateDependent()).thenReturn(false);

        // Act
        String result = yourClass.doSaveDependentEdits();

        // Assert
        assertNull(result);
        verify(authorizationHelperBean, never()).getForwardAuthorization(anyString());
    }
}


	public String getMiddlewareSrvcUrl() {
		return middlewareSrvcUrl;
	}

	public String getApplicationExternalUrl() {
		return applicationExternalUrl;
	}

	public String getApplicationInternalUrl() {
		return applicationInternalUrl;
	}

	public boolean getProductionEnvironment() {
		return productionEnvironment;
	}

	public String getMyProfileUrl() {
		return myProfileUrl;
	}

	public String getCompassStatusMessage() {
		return compassStatusMessage;
	}

	public Integer getManageBillsMemberDetailsPaginationSize() {
		return manageBillsMemberDetailsPaginationSize;
	}

	public Integer getPremiumReconMemberDetailsPaginationSize() {
		return premiumReconMemberDetailsPaginationSize;
	}

	public String getSolarServiceUrl() {
		return solarServiceUrl;
	}

	public Integer getSolarServicePort() {
		return solarServicePort;
	}

	public String getAcomsUrl() {
		return acomsUrl;
	}

	public String getGroupFactsUrl() {
		return groupFactsUrl;
	}

	public String getGhUrl() {
		return ghUrl;
	}

	public String getvResMemberReporting() {
		return vResMemberReporting;
	}

	public String getDocViewerUrl() {
		return docViewerUrl;
	}

	public String getMdrHelpUrl() {
		return mdrHelpUrl;
	}

	public String getMcsHelpUrl() {
		return mcsHelpUrl;
	}

	public Integer getMcsMembersPaginationSize() {
		return mcsMembersPaginationSize;
	}

	public String getMemberCoverageStatementEnabled() {
		return memberCoverageStatementEnabled;
	}

	public String getClaimsReportingEnabled() {
		return claimsReportingEnabled;
	}

	public String getOrdersWildeUrl() {
		return ordersWildeUrl;
	}

	public String getWebinar() {
		return webinar;
	}

	public String getUserGuide() {
		return userGuide;
	}

	public String getMemberRegistrationGuide() {
		return memberRegistrationGuide;
	}

	public String getHomeContactUsUrl() {
		return homeContactUsUrl;
	}

	public String getPolicyFormsUrl() {
		return policyFormsUrl;
	}

	public String getGenericPolicyFormsUrl() {
		return genericPolicyFormsUrl;
	}

	public String getEmployerLogoutUrl() {
		return employerLogoutUrl;
	}

	public String getForEmployersHomeUrl() {
		return forEmployersHomeUrl;
	}

	public String getBoxiRequesterId() {
		return boxiRequesterId;
	}

	public String getBoxiRequesterToken() {
		return boxiRequesterToken;
	}

	public String getBoxiBusinessObjectsUserGroupId() {
		return boxiBusinessObjectsUserGroupId;
	}

	public String getBoxiLanguage() {
		return boxiLanguage;
	}

	public String getBoxiAuthenticationType() {
		return boxiAuthenticationType;
	}

	public String getBoxiAuthenticationServerUrl() {
		return boxiAuthenticationServerUrl;
	}

	public String getBoxiServerUrl() {
		return boxiServerUrl;
	}

	public String getBoxiReportHost() {
		return boxiReportHost;
	}

	public String getBoxiDisabilityClaimsReportObjectId() {
		return boxiDisabilityClaimsReportObjectId;
	}

	public Integer getBoxiMonitorScheduledRequestWaitTime() {
		return boxiMonitorScheduledRequestWaitTime;
	}

	public Integer getBoxiMonitorScheduledRequestRepetitions() {
		return boxiMonitorScheduledRequestRepetitions;
	}

	public String getBrokerLandingUrlId() {
		return brokerLandingUrlId;
	}

	public String getBrokerMessageUrlId() {
		return brokerMessageUrlId;
	}

	public String getReCaptchaSiteKey() {
		return reCaptchaSiteKey;
	}

	public String getDocumentOutputServiceUrl() {
		return documentOutputServiceUrl;
	}

	public String getIocDentalIdCardsDisplayable() {
		return iocDentalIdCardsDisplayable;
	}

	public String getXpressionUser() {
		return xpressionUser;
	}

	public String getXpressionPassword() {
		return xpressionPassword;
	}

	public Integer getBenefitInfoPageRequestLimit() {
		return benefitInfoPageRequestLimit;
	}

	public String getStaticContentPath() {
		return staticContentPath;
	}

	public String getEmployerStaticContentPath() {
		return employerStaticContentPath;
	}

	public String getBannerNewsPath() {
		return bannerNewsPath;
	}

	public String getJndiBoxiBillingReportObjectId() {
		return jndiBoxiBillingReportObjectId;
	}

	public RequestBuilderFactory getBoxiRequestBuilderFactory() {
		return boxiRequestBuilderFactory;
	}

	public ResponseBuilderFactory getResponseBuilderFactory() {
		return responseBuilderFactory;
	}

	public ReportHelper getReportHelper() {
		return reportHelper;
	}


	public String getUnleashAppName() {
		return unleashAppName;
	}

	public void setUnleashAppName(String unleashAppName) {
		this.unleashAppName = unleashAppName;
	}

	public String getUnleashInstanceId() {
		return unleashInstanceId;
	}

	public void setUnleashInstanceId(String unleashInstanceId) {
		this.unleashInstanceId = unleashInstanceId;
	}

	public String getUnleashEnvironment() {
		return unleashEnvironment;
	}

	public void setUnleashEnvironment(String unleashEnvironment) {
		this.unleashEnvironment = unleashEnvironment;
	}

	public String getUnleashApiUrl() {
		return unleashApiUrl;
	}

	public void setUnleashApiUrl(String unleashApiUrl) {
		this.unleashApiUrl = unleashApiUrl;
	}

	public String getUnleashAuthHeader() {
		return unleashAuthHeader;
	}

	public void setUnleashAuthHeader(String unleashAuthHeader) {
		this.unleashAuthHeader = unleashAuthHeader;
	}
