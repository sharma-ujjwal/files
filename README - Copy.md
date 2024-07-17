private void populateAdjustedBillDepartmentList(List<BillDepartment> printBillsMemberList, List<BillDepartment> adjustedBillDepartmentList){

		for(BillDepartment billDepartment : printBillsMemberList){
			List<BillMember> billMemberList = new ArrayList<BillMember>();
			BillDepartment department = new BillDepartment();
			department.setName(billDepartment.getName());
			//department.setMemberPastDueAmountTotal(billDepartment.getMemberPastDueAmountTotal());
			department.setMemberEmployeeTotal(billDepartment.getMemberEmployeeTotal());
			department.setMemberEmployerTotal(billDepartment.getMemberEmployerTotal());
			department.setMemberBillAmountDueTotal(billDepartment.getMemberBillAmountDueTotal());
			int count=0;
			for(BillMember billMember : billDepartment.getMembers()){
				BillMember member = new BillMember();
				member.setFirstName(billMember.getFirstName());
				member.setLastName(billMember.getLastName());
				member.setCertNumber(billMember.getCertNumber());
				//member.setPastDueAmountTotal(billMember.getPastDueAmountTotal());
				member.setEmployerTotal(billMember.getEmployerTotal());
				member.setEmployeeTotal(billMember.getEmployeeTotal());
				member.setBillAmountDueTotal(billMember.getBillAmountDueTotal());
				member.setCount(count++);
				//employment status & waived message make up the waived/cobra column in the
				//member details download spreadsheet
				member.setEmploymentStatus(billMember.getEmploymentStatus());
				member.setWaivedMessage(billMember.getWaivedMessage());


				List<BillMemberCoverage> billMemberCoverageList = new ArrayList<BillMemberCoverage>();
				if(billMember.getCoverages() != null && billMember.getCoverages().size() > 0){
					billMemberCoverageList.addAll(billMember.getCoverages());
					member.setCoverages(billMemberCoverageList);
				}

				List<BillMemberAdjustment> billMemberAdjustmentList = new ArrayList<BillMemberAdjustment>();
				if(billMember.getAdjustments() != null && billMember.getAdjustments().size() > 0){
					billMemberAdjustmentList.addAll(billMember.getAdjustments());
					member.setAdjustments(billMemberAdjustmentList);
				}

				billMemberList.add(member);
			}

			department.setMembers(billMemberList);
			adjustedBillDepartmentList.add(department);
		}
	}


 import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import java.util.ArrayList;
import java.util.List;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.MockitoAnnotations;

public class ManageBillHelperBeanTest {

    @InjectMocks
    private ManageBillHelperBean manageBillHelperBean;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testPopulateAdjustedBillDepartmentList() {
        // Create the input list of BillDepartment
        List<BillDepartment> printBillsMemberList = new ArrayList<>();
        BillDepartment originalDepartment = new BillDepartment();
        originalDepartment.setName("Finance");
        originalDepartment.setMemberEmployeeTotal(500.0);
        originalDepartment.setMemberEmployerTotal(600.0);
        originalDepartment.setMemberBillAmountDueTotal(1100.0);
        
        List<BillMember> originalMembers = new ArrayList<>();
        BillMember originalMember = new BillMember();
        originalMember.setFirstName("Alice");
        originalMember.setLastName("Smith");
        originalMember.setCertNumber("A12345");
        originalMember.setEmployerTotal(300.0);
        originalMember.setEmployeeTotal(200.0);
        originalMember.setBillAmountDueTotal(500.0);
        originalMember.setEmploymentStatus("Active");
        originalMember.setWaivedMessage("N/A");
        
        List<BillMemberCoverage> originalCoverages = new ArrayList<>();
        BillMemberCoverage originalCoverage = new BillMemberCoverage();
        originalCoverage.setShortName("Health");
        originalCoverages.add(originalCoverage);
        originalMember.setCoverages(originalCoverages);
        
        List<BillMemberAdjustment> originalAdjustments = new ArrayList<>();
        BillMemberAdjustment originalAdjustment = new BillMemberAdjustment();
        originalAdjustment.setDescription("Adjustment");
        originalAdjustments.add(originalAdjustment);
        originalMember.setAdjustments(originalAdjustments);
        
        originalMembers.add(originalMember);
        originalDepartment.setMembers(originalMembers);
        printBillsMemberList.add(originalDepartment);
        
        // Create the output list
        List<BillDepartment> adjustedBillDepartmentList = new ArrayList<>();
        
        // Call the method to be tested
        manageBillHelperBean.populateAdjustedBillDepartmentList(printBillsMemberList, adjustedBillDepartmentList);
        
        // Verify the output list
        assertEquals(1, adjustedBillDepartmentList.size());
        
        BillDepartment adjustedDepartment = adjustedBillDepartmentList.get(0);
        assertEquals("Finance", adjustedDepartment.getName());
        assertEquals(500.0, adjustedDepartment.getMemberEmployeeTotal());
        assertEquals(600.0, adjustedDepartment.getMemberEmployerTotal());
        assertEquals(1100.0, adjustedDepartment.getMemberBillAmountDueTotal());
        
        List<BillMember> adjustedMembers = adjustedDepartment.getMembers();
        assertEquals(1, adjustedMembers.size());
        
        BillMember adjustedMember = adjustedMembers.get(0);
        assertEquals("Alice", adjustedMember.getFirstName());
        assertEquals("Smith", adjustedMember.getLastName());
        assertEquals("A12345", adjustedMember.getCertNumber());
        assertEquals(300.0, adjustedMember.getEmployerTotal());
        assertEquals(200.0, adjustedMember.getEmployeeTotal());
        assertEquals(500.0, adjustedMember.getBillAmountDueTotal());
        assertEquals("Active", adjustedMember.getEmploymentStatus());
        assertEquals("N/A", adjustedMember.getWaivedMessage());
        
        List<BillMemberCoverage> adjustedCoverages = adjustedMember.getCoverages();
        assertEquals(1, adjustedCoverages.size());
        assertEquals("Health", adjustedCoverages.get(0).getShortName());
        
        List<BillMemberAdjustment> adjustedAdjustments = adjustedMember.getAdjustments();
        assertEquals(1, adjustedAdjustments.size());
        assertEquals("Adjustment", adjustedAdjustments.get(0).getDescription());
    }
}
