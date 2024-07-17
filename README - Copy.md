private void parseArrearsMemberDetails(LoadBillDetailResponseDTO billDetailResponse){
		List<BillDepartment> mbrDepartmentList = new ArrayList<BillDepartment>();

		for(OnlineBillDepartmentDTO department : billDetailResponse.getOnlineBillDepartmentList()){

			for(OnlineBillDepartmentMembersDTO departmentMembers : department.getOnlineBillDepartmentMemberList()){
				List<BillMember> departmentMemberList = new ArrayList<BillMember>();

				//set department totals & name
				BillDepartment billDepartment = new BillDepartment();
				billDepartment.setMemberEmployeeTotal(departmentMembers.getDepartmentEmployeeTotal());
				billDepartment.setMemberEmployerTotal(departmentMembers.getDepartmentEmployerTotal());
				//billDepartment.setMemberPastDueAmountTotal(departmentMembers.getDepartmentPastDueTotal());
				billDepartment.setMemberBillAmountDueTotal(departmentMembers.getDepartmentTotalAmountDueTotal());
				billDepartment.setName(departmentMembers.getDisplayName());

				//set members
				int count=0;
				for(OnlineBillMemberDTO member : departmentMembers.getOnlineBillMemberList()){
					BillMember billMember = new BillMember();
					billMember.setCaseMemberKey(String.valueOf(member.getCaseMemberKey()));
					billMember.setFirstName(member.getFirstName());
					billMember.setLastName(member.getLastName());
					billMember.setCertNumber(member.getMemberNo());
					billMember.setWaivedMessage(member.getWaivedMessage());
					billMember.setCount(count++);
					billMember.setEmployeeTotal(member.getEmployeeTotal());
					billMember.setEmployerTotal(member.getEmployerTotal());
					//billMember.setPastDueAmountTotal(member.getPastDueAmountTotal());
					billMember.setBillAmountDueTotal(member.getBillAmountDueTotal());
					billMember.setAmountRemittedTotal(member.getBillAmountDueTotal());

					//load coverages specific to the member
					List<BillMemberCoverage> coverageList = new ArrayList<BillMemberCoverage>();
					for(OnlineBillCoverageDTO memberCoverage : member.getOnlineBillCoverageList()){
						BillMemberCoverage billMemberCoverage = new BillMemberCoverage();
						billMemberCoverage.setShortName(memberCoverage.getBenefitShortName());
						//billMemberCoverage.setPastDueAmount(memberCoverage.getPastDueAmount());
						billMemberCoverage.setCoverageAmount(memberCoverage.getBenefitAmount());
						billMemberCoverage.setEmployerContribution(memberCoverage.getEmployerContribution());
						billMemberCoverage.setEmployeeContribution(memberCoverage.getEmployeeContribution());
						billMemberCoverage.setBillAmountDue(memberCoverage.getBenefitTotal());
						billMemberCoverage.setAmountRemitted(memberCoverage.getBenefitTotal());
						billMemberCoverage.setAgeBand(memberCoverage.getAgeBand());
						billMemberCoverage.setRollupIndicator(memberCoverage.getRollupIndicator());
						billMemberCoverage.setPremiumAdjustmentReason("NA");
						coverageList.add(billMemberCoverage);
					}
					billMember.setCoverages(coverageList);

					//load the summarized benefits
					billMember.setSummarizedCoverageList(retrieveSummarizedCoverage(coverageList));

					//add the member to the current department
					departmentMemberList.add(billMember);
				}

				//we are done with the current department, add the list of members to the current department
				//and add the current department to the overall department list
				billDepartment.setMembers(departmentMemberList);
				mbrDepartmentList.add(billDepartment);
			}
		}
		
  
  
  getManageBillsDataBean().setTotalBillAmountDue(billDetailResponse.getTotalAmountDue());
		getManageBillsDataBean().setMemberList(mbrDepartmentList);

		logger.debug("**************************************************************************");
		logger.debug("ManageBillsHelperBean: parseArrearsMemberDetails: = {}", mbrDepartmentList.toString());
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
    public void testParseArrearsMemberDetails() {
        // Mock the response DTO
        LoadBillDetailResponseDTO mockBillDetailResponse = mock(LoadBillDetailResponseDTO.class);
        
        // Create a list of OnlineBillDepartmentDTO
        List<OnlineBillDepartmentDTO> departmentList = new ArrayList<>();
        OnlineBillDepartmentDTO departmentDTO = new OnlineBillDepartmentDTO();
        List<OnlineBillDepartmentMembersDTO> departmentMembersList = new ArrayList<>();
        OnlineBillDepartmentMembersDTO departmentMembersDTO = new OnlineBillDepartmentMembersDTO();

        // Create a list of OnlineBillMemberDTO
        List<OnlineBillMemberDTO> memberList = new ArrayList<>();
        OnlineBillMemberDTO memberDTO = new OnlineBillMemberDTO();
        List<OnlineBillCoverageDTO> coverageList = new ArrayList<>();
        OnlineBillCoverageDTO coverageDTO = new OnlineBillCoverageDTO();
        
        // Populate the coverage list
        coverageDTO.setBenefitShortName("Health");
        coverageDTO.setBenefitAmount(100.0);
        coverageDTO.setEmployerContribution(50.0);
        coverageDTO.setEmployeeContribution(50.0);
        coverageDTO.setBenefitTotal(100.0);
        coverageDTO.setAgeBand("30-40");
        coverageDTO.setRollupIndicator("N");
        coverageList.add(coverageDTO);
        
        // Populate the member DTO
        memberDTO.setCaseMemberKey(1);
        memberDTO.setFirstName("John");
        memberDTO.setLastName("Doe");
        memberDTO.setMemberNo("12345");
        memberDTO.setWaivedMessage("N/A");
        memberDTO.setEmployeeTotal(50.0);
        memberDTO.setEmployerTotal(50.0);
        memberDTO.setBillAmountDueTotal(100.0);
        memberDTO.setOnlineBillCoverageList(coverageList);
        memberList.add(memberDTO);
        
        // Populate the department members DTO
        departmentMembersDTO.setDepartmentEmployeeTotal(50.0);
        departmentMembersDTO.setDepartmentEmployerTotal(50.0);
        departmentMembersDTO.setDepartmentTotalAmountDueTotal(100.0);
        departmentMembersDTO.setDisplayName("IT Department");
        departmentMembersDTO.setOnlineBillMemberList(memberList);
        departmentMembersList.add(departmentMembersDTO);
        
        // Populate the department DTO
        departmentDTO.setOnlineBillDepartmentMemberList(departmentMembersList);
        departmentList.add(departmentDTO);
        
        // Set the online bill department list in the mock response DTO
        when(mockBillDetailResponse.getOnlineBillDepartmentList()).thenReturn(departmentList);
        when(mockBillDetailResponse.getTotalAmountDue()).thenReturn(100.0);
        
        // Call the method to be tested
        manageBillHelperBean.parseArrearsMemberDetails(mockBillDetailResponse);
        
        // Verify that the data bean has been populated correctly
        verify(manageBillsDataBean).setTotalBillAmountDue(100.0);
        assertNotNull(manageBillsDataBean.getMemberList());
        assertEquals(1, manageBillsDataBean.getMemberList().size());
        
        // Verify that the department details have been set correctly
        BillDepartment billDepartment = manageBillsDataBean.getMemberList().get(0);
        assertEquals("IT Department", billDepartment.getName());
        assertEquals(100.0, billDepartment.getMemberBillAmountDueTotal());
        
        // Verify that the member details have been set correctly
        BillMember billMember = billDepartment.getMembers().get(0);
        assertEquals("John", billMember.getFirstName());
        assertEquals("Doe", billMember.getLastName());
        assertEquals("12345", billMember.getCertNumber());
        assertEquals(100.0, billMember.getBillAmountDueTotal());
        
        // Verify that the coverage details have been set correctly
        BillMemberCoverage billMemberCoverage = billMember.getCoverages().get(0);
        assertEquals("Health", billMemberCoverage.getShortName());
        assertEquals(100.0, billMemberCoverage.getCoverageAmount());
    }
}
