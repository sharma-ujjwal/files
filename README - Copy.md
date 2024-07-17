public void determineBillGroupAvailability() {
		List<IPolicyAuthorization> policyAuthorizationList = getUserBean().getPolicyAuthorizations();
		
		Map<String,BillGroupDTO> activeBillGroupMap = getMemberCoverageStatementDataBean().getActiveBillgroupMap();
		if (activeBillGroupMap == null || activeBillGroupMap.isEmpty()) {
			loadActiveBillGroups(getMemberCoverageStatementDataBean().getReportDate());
			activeBillGroupMap = getMemberCoverageStatementDataBean().getActiveBillgroupMap();
		}
		
		memberCoverageStatementDataBean.setMatchedPolicyAuthorizations(new ArrayList<>());
		memberCoverageStatementDataBean.setExcludedPolicyAuthorizations(new ArrayList<>());
		
		final boolean POLICY_ON_AMENDMENT_HOLD = policyInfoDataBean.isAmendmentHold();

		Iterator<String> iter;
		String authorization;
		
		//	iterate across set of IPolicyAuthorizations - if it's a compass IPolicyAuthorization for the selected policy and has a bill group key then
		// 		iterate across the set of authorizations on the IPolicyAuthorization - if user has bill view or mgmt rights then
		//			determine if the IPolicyAuthorization is included in the available or excluded set
		for (IPolicyAuthorization policyAuthorization : policyAuthorizationList) {
			if(policyAuthorization != null && policyAuthorization instanceof CompassPolicyAuthorization &&
			   policyAuthorization.getPolicyNumber().equals(policyValueBean.getSelectedPolicyNumber()) && 
			   policyAuthorization.getBillGroupKey() != null &&
			   activeBillGroupMap.containsKey(policyAuthorization.getBillGroupKey())) {
				iter = policyAuthorization.getPrivilegeCollection().iterator();
				while (iter.hasNext()) {
					authorization = iter.next();
					if (IPolicyAuthorization.PRIVILEGE_BILL_VIEW.equalsIgnoreCase(authorization) ||
						IPolicyAuthorization.PRIVILEGE_BILL_MANAGEMENT.equalsIgnoreCase(authorization)) {
						
						// create collection of billgroup schedulers for determining availability
						List<BillGroupTransactionScheduler> billingSchedulerList = new ArrayList<>();
						for (BillGroupTransactionScheduler scheduler : policyAuthorization.getTransactionSchedulerList()) {
							if (SCHEDULER_BILLING_PRDF_NAME.equalsIgnoreCase(scheduler.getPrdfName())) {
								billingSchedulerList.add(scheduler);
							}
						}
												
						if (POLICY_ON_AMENDMENT_HOLD) {
							determineBillGroupAvailabilityForAmendmentHold(policyAuthorization, billingSchedulerList);
						}
						else {
							determineBillGroupAvailabilityNoAmendmentHold(policyAuthorization, billingSchedulerList);
						}
						
						break;		// prevent duplicate processing of billgroup in case user has both view & mgmt authorization	
					}										
				}
			}
		} 
		
		//sort matched policy authorizations alpha
		Collections.sort(memberCoverageStatementDataBean.getMatchedPolicyAuthorizations(), this);
		setShowBillGroupPanel(!memberCoverageStatementDataBean.getMatchedPolicyAuthorizations().isEmpty());
		
		//sort excluded policy authorizations alpha
		Collections.sort(memberCoverageStatementDataBean.getExcludedPolicyAuthorizations(), this);
		setShowExcludedBillGroupPanel(!memberCoverageStatementDataBean.getExcludedPolicyAuthorizations().isEmpty());
	}	


 import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

import java.util.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

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

    @InjectMocks
    private MemberCoverageStatementHelperBean memberCoverageStatementHelperBean;

    private List<IPolicyAuthorization> policyAuthorizationList;
    private Map<String, BillGroupDTO> activeBillGroupMap;

    @BeforeEach
    void setup() {
        policyAuthorizationList = new ArrayList<>();
        activeBillGroupMap = new HashMap<>();
        
        when(userBean.getPolicyAuthorizations()).thenReturn(policyAuthorizationList);
        when(memberCoverageStatementDataBean.getActiveBillgroupMap()).thenReturn(activeBillGroupMap);
    }

    @Test
    void testDetermineBillGroupAvailability_NoActiveBillGroups() {
        when(memberCoverageStatementDataBean.getReportDate()).thenReturn(new Date());
        doAnswer(invocation -> {
            activeBillGroupMap.put("key1", new BillGroupDTO());
            return null;
        }).when(memberCoverageStatementHelperBean).loadActiveBillGroups(any(Date.class));

        memberCoverageStatementHelperBean.determineBillGroupAvailability();

        verify(memberCoverageStatementHelperBean).loadActiveBillGroups(any(Date.class));
        verify(memberCoverageStatementDataBean).setMatchedPolicyAuthorizations(any());
        verify(memberCoverageStatementDataBean).setExcludedPolicyAuthorizations(any());
    }

    @Test
    void testDetermineBillGroupAvailability_WithActiveBillGroups() {
        activeBillGroupMap.put("key1", new BillGroupDTO());
        CompassPolicyAuthorization policyAuthorization = mock(CompassPolicyAuthorization.class);
        policyAuthorizationList.add(policyAuthorization);

        when(policyAuthorization.getPolicyNumber()).thenReturn("POL123");
        when(policyAuthorization.getBillGroupKey()).thenReturn("key1");
        when(policyAuthorization.getPrivilegeCollection()).thenReturn(Arrays.asList("PRIVILEGE_BILL_VIEW", "PRIVILEGE_BILL_MANAGEMENT"));

        when(policyValueBean.getSelectedPolicyNumber()).thenReturn("POL123");
        when(policyInfoDataBean.isAmendmentHold()).thenReturn(false);

        List<BillGroupTransactionScheduler> schedulerList = new ArrayList<>();
        BillGroupTransactionScheduler scheduler = mock(BillGroupTransactionScheduler.class);
        when(scheduler.getPrdfName()).thenReturn("SCHEDULER_BILLING_PRDF_NAME");
        schedulerList.add(scheduler);
        when(policyAuthorization.getTransactionSchedulerList()).thenReturn(schedulerList);

        memberCoverageStatementHelperBean.determineBillGroupAvailability();

        verify(memberCoverageStatementHelperBean).determineBillGroupAvailabilityNoAmendmentHold(eq(policyAuthorization), anyList());
        verify(memberCoverageStatementDataBean).setMatchedPolicyAuthorizations(any());
        verify(memberCoverageStatementDataBean).setExcludedPolicyAuthorizations(any());
    }

    @Test
    void testDetermineBillGroupAvailability_WithAmendmentHold() {
        activeBillGroupMap.put("key1", new BillGroupDTO());
        CompassPolicyAuthorization policyAuthorization = mock(CompassPolicyAuthorization.class);
        policyAuthorizationList.add(policyAuthorization);

        when(policyAuthorization.getPolicyNumber()).thenReturn("POL123");
        when(policyAuthorization.getBillGroupKey()).thenReturn("key1");
        when(policyAuthorization.getPrivilegeCollection()).thenReturn(Arrays.asList("PRIVILEGE_BILL_VIEW", "PRIVILEGE_BILL_MANAGEMENT"));

        when(policyValueBean.getSelectedPolicyNumber()).thenReturn("POL123");
        when(policyInfoDataBean.isAmendmentHold()).thenReturn(true);

        List<BillGroupTransactionScheduler> schedulerList = new ArrayList<>();
        BillGroupTransactionScheduler scheduler = mock(BillGroupTransactionScheduler.class);
        when(scheduler.getPrdfName()).thenReturn("SCHEDULER_BILLING_PRDF_NAME");
        schedulerList.add(scheduler);
        when(policyAuthorization.getTransactionSchedulerList()).thenReturn(schedulerList);

        memberCoverageStatementHelperBean.determineBillGroupAvailability();

        verify(memberCoverageStatementHelperBean).determineBillGroupAvailabilityForAmendmentHold(eq(policyAuthorization), anyList());
        verify(memberCoverageStatementDataBean).setMatchedPolicyAuthorizations(any());
        verify(memberCoverageStatementDataBean).setExcludedPolicyAuthorizations(any());
    }

    @Test
    void testDetermineBillGroupAvailability_NoPrivileges() {
        activeBillGroupMap.put("key1", new BillGroupDTO());
        CompassPolicyAuthorization policyAuthorization = mock(CompassPolicyAuthorization.class);
        policyAuthorizationList.add(policyAuthorization);

        when(policyAuthorization.getPolicyNumber()).thenReturn("POL123");
        when(policyAuthorization.getBillGroupKey()).thenReturn("key1");
        when(policyAuthorization.getPrivilegeCollection()).thenReturn(Collections.emptyList());

        when(policyValueBean.getSelectedPolicyNumber()).thenReturn("POL123");
        when(policyInfoDataBean.isAmendmentHold()).thenReturn(false);

        memberCoverageStatementHelperBean.determineBillGroupAvailability();

        verify(memberCoverageStatementDataBean).setMatchedPolicyAuthorizations(any());
        verify(memberCoverageStatementDataBean).setExcludedPolicyAuthorizations(any());
    }
}
