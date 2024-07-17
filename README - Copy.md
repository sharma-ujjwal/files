private void parseArrearsMemberAdjustments(LoadBillDetailResponseDTO billDetailResponse){
		List<BillDepartment> adjustedMemberList = new ArrayList<BillDepartment>();

		for(OnlineBillDepartmentDTO department : billDetailResponse.getOnlineBillDepartmentList()){
			for(OnlineBillMemberAdjustmentsDTO memberAdjustments : department.getOnlineBillMemberAdjustmentsList()){
				List<BillMember> adjustedDepartmentList = new ArrayList<BillMember>();

				//set department totals & name
				BillDepartment billDepartment = new BillDepartment();
				billDepartment.setAdjustedPayrollTotal(formatBillCurrencyDollarSign(String.valueOf(memberAdjustments.getAdjustmentsPayrollTotal())));
				billDepartment.setAdjustedTotal(formatBillCurrencyDollarSign(String.valueOf(memberAdjustments.getAdjustmentsTotal())));
				billDepartment.setAdjustedPremiumTotal(formatBillCurrencyDollarSign(String.valueOf(memberAdjustments.getAdjustmentsPremiumTotal())));
				billDepartment.setName(memberAdjustments.getDisplayName());

				//set members
				int count = 0;
				for(OnlineBillMemberDTO member : memberAdjustments.getOnlineBillMemberList()){
					BillMember billMember = new BillMember();
					billMember.setCaseMemberKey(String.valueOf(member.getCaseMemberKey()));
					billMember.setFirstName(member.getFirstName());
					billMember.setLastName(member.getLastName());
					billMember.setCertNumber(member.getMemberNo());
					billMember.setCount(count++);
					billMember.setAdjustedPayrollDeductionSubtotal(formatBillCurrencyDollarSign(String.valueOf(member.getPayrollTotal())));
					billMember.setAdjustedPremiumSubtotal(formatBillCurrencyDollarSign(String.valueOf(member.getPremiumTotal())));
					billMember.setAdjustedTotal(formatBillCurrencyDollarSign(String.valueOf(member.getTotalAdjustments())));

					//load coverages specific to the member
					List<BillMemberAdjustment> adjustmentList = new ArrayList<BillMemberAdjustment>();
					for(OnlineBillCoverageDTO memberCoverage : member.getOnlineBillCoverageList()){
						BillMemberAdjustment memberAdjustment = new BillMemberAdjustment();
						memberAdjustment.setDescription(memberCoverage.getBenefitShortName());
						memberAdjustment.setPayrollDeduction(formatBillCurrency(String.valueOf(memberCoverage.getPayrollDeduction())));
						memberAdjustment.setPremium(formatBillCurrency(String.valueOf(memberCoverage.getPremiumFees())));
						memberAdjustment.setReason(memberCoverage.getAdjustmentReason());
						adjustmentList.add(memberAdjustment);
					}
					billMember.setAdjustments(adjustmentList);

					//add the member to the current department
					adjustedDepartmentList.add(billMember);
				}

				//check if we have payroll deductions and if so, set indicator that we have them
				if(!getManageBillsDataBean().isPayrollDeductions()){
					checkPayrollDeductions(adjustedDepartmentList);
				}

				//we are done with the current department, add the list of members to the current department
				//and add the current department to the overall department list
				billDepartment.setMembers(adjustedDepartmentList);
				adjustedMemberList.add(billDepartment);
			}
		}
		getManageBillsDataBean().setAdjustedMemberList(adjustedMemberList);
		calcBillAdjustmentTotal(billDetailResponse);

		logger.debug("**************************************************************************");
		logger.debug("ManageBillsHelperBean: parseArrearsMemberAdjustments: = {}", adjustedMemberList.toString());
		logger.debug("**************************************************************************");
	}


 import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import java.util.ArrayList;
import java.util.List;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.slf4j.Logger;

public class ManageBillHelperBeanTest {

    @InjectMocks
    private ManageBillHelperBean manageBillHelperBean;

    @Mock
    private ManageBillsDataBean manageBillsDataBean;

    @Mock
    private Logger logger;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testParseArrearsMemberAdjustments() {
        // Mock the response DTO
        LoadBillDetailResponseDTO mockBillDetailResponse = mock(LoadBillDetailResponseDTO.class);
        
        // Create a list of OnlineBillDepartmentDTO
        List<OnlineBillDepartmentDTO> departmentList = new ArrayList<>();
        OnlineBillDepartmentDTO departmentDTO = new OnlineBillDepartmentDTO();
        List<OnlineBillMemberAdjustmentsDTO> memberAdjustmentsList = new ArrayList<>();
        OnlineBillMemberAdjustmentsDTO memberAdjustmentsDTO = new OnlineBillMemberAdjustmentsDTO();

        // Create a list of OnlineBillMemberDTO
        List<OnlineBillMemberDTO> memberList = new ArrayList<>();
        OnlineBillMemberDTO memberDTO = new OnlineBillMemberDTO();
        List<OnlineBillCoverageDTO> coverageList = new ArrayList<>();
        OnlineBillCoverageDTO coverageDTO = new OnlineBillCoverageDTO();
        
        // Populate the coverage list
        coverageDTO.setBenefitShortName("Health");
        coverageDTO.setPayrollDeduction(20.0);
        coverageDTO.setPremiumFees(30.0);
        coverageDTO.setAdjustmentReason("Correction");
        coverageList.add(coverageDTO);
        
        // Populate the member DTO
        memberDTO.setCaseMemberKey(1);
        memberDTO.setFirstName("John");
        memberDTO.setLastName("Doe");
        memberDTO.setMemberNo("12345");
        memberDTO.setPayrollTotal(100.0);
        memberDTO.setPremiumTotal(150.0);
        memberDTO.setTotalAdjustments(250.0);
        memberDTO.setOnlineBillCoverageList(coverageList);
        memberList.add(memberDTO);
        
        // Populate the member adjustments DTO
        memberAdjustmentsDTO.setAdjustmentsPayrollTotal(100.0);
        memberAdjustmentsDTO.setAdjustmentsTotal(250.0);
        memberAdjustmentsDTO.setAdjustmentsPremiumTotal(150.0);
        memberAdjustmentsDTO.setDisplayName("IT Department");
        memberAdjustmentsDTO.setOnlineBillMemberList(memberList);
        memberAdjustmentsList.add(memberAdjustmentsDTO);
        
        // Populate the department DTO
        departmentDTO.setOnlineBillMemberAdjustmentsList(memberAdjustmentsList);
        departmentList.add(departmentDTO);
        
        // Set the online bill department list in the mock response DTO
        when(mockBillDetailResponse.getOnlineBillDepartmentList()).thenReturn(departmentList);
        
        // Call the method to be tested
        manageBillHelperBean.parseArrearsMemberAdjustments(mockBillDetailResponse);
        
        // Verify that the data bean has been populated correctly
        assertNotNull(manageBillsDataBean.getAdjustedMemberList());
        assertEquals(1, manageBillsDataBean.getAdjustedMemberList().size());
        
        // Verify that the department details have been set correctly
        BillDepartment billDepartment = manageBillsDataBean.getAdjustedMemberList().get(0);
        assertEquals("IT Department", billDepartment.getName());
        assertEquals("$100.00", billDepartment.getAdjustedPayrollTotal());
        assertEquals("$150.00", billDepartment.getAdjustedPremiumTotal());
        assertEquals("$250.00", billDepartment.getAdjustedTotal());
        
        // Verify that the member details have been set correctly
        BillMember billMember = billDepartment.getMembers().get(0);
        assertEquals("John", billMember.getFirstName());
        assertEquals("Doe", billMember.getLastName());
        assertEquals("12345", billMember.getCertNumber());
        assertEquals("$100.00", billMember.getAdjustedPayrollDeductionSubtotal());
        assertEquals("$150.00", billMember.getAdjustedPremiumSubtotal());
        assertEquals("$250.00", billMember.getAdjustedTotal());
        
        // Verify that the coverage details have been set correctly
        BillMemberAdjustment billMemberAdjustment = billMember.getAdjustments().get(0);
        assertEquals("Health", billMemberAdjustment.getDescription());
        assertEquals("$20.00", billMemberAdjustment.getPayrollDeduction());
        assertEquals("$30.00", billMemberAdjustment.getPremium());
        assertEquals("Correction", billMemberAdjustment.getReason());
        
        // Verify method calls for setting payroll deductions
        verify(manageBillsDataBean).isPayrollDeductions();
        verify(manageBillHelperBean).checkPayrollDeductions(anyList());
        
        // Verify the total calculation method is called
        verify(manageBillHelperBean).calcBillAdjustmentTotal(mockBillDetailResponse);
    }
}
