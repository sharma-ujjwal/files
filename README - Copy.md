import java.math.BigDecimal;

import javax.faces.application.FacesMessage;
import javax.faces.component.UIComponent;
import javax.faces.component.UIInput;
import javax.faces.context.FacesContext;
import javax.faces.event.AjaxBehaviorEvent;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class PaymentValidatorTest {

    private PaymentValidator paymentValidator;
    private FacesContext facesContext;
    private UIInput payAmountInput;
    private UIComponent component;
    private AjaxBehaviorEvent event;

    @BeforeEach
    void setUp() {
        paymentValidator = new PaymentValidator();

        // Initialize FacesContext (Requires JSF Test Framework for full support)
        facesContext = FacesContext.getCurrentInstance();

        // Initialize a real UIInput instance
        payAmountInput = new UIInput();
        
        // Create a simple UIComponent instance
        component = new UIComponent() {}; // Anonymous subclass since UIComponent is abstract

        // Manually create an AjaxBehaviorEvent with the component
        event = new AjaxBehaviorEvent(component) {};
    }

    @Test
    void testValidate_NullValue_ShouldShowErrorMessage() {
        // Simulate a null value
        payAmountInput.getAttributes().put("value", null);

        // Invoke the method
        paymentValidator.validate(event);

        // Verify that an error message was added
        assertFalse(facesContext.getMessageList().isEmpty());

        // Check the specific error message (Modify based on actual implementation)
        FacesMessage message = facesContext.getMessageList().get(0);
        assertEquals("Error: Payment amount is required.", message.getDetail());
    }

    @Test
    void testValidate_NegativeValue_ShouldShowErrorMessage() {
        // Simulate a negative value
        payAmountInput.getAttributes().put("value", new BigDecimal("-1"));

        // Invoke the method
        paymentValidator.validate(event);

        // Verify that an error message was added
        assertFalse(facesContext.getMessageList().isEmpty());

        // Check the error message for negative values
        FacesMessage message = facesContext.getMessageList().get(0);
        assertEquals("Error: Negative payment amount is not allowed.", message.getDetail());
    }

    @Test
    void testValidate_ValidValue_ShouldNotShowErrorMessage() {
        // Simulate a valid payment amount
        payAmountInput.getAttributes().put("value", new BigDecimal("100"));

        // Invoke the method
        paymentValidator.validate(event);

        // Ensure no error messages are added
        assertTrue(facesContext.getMessageList().isEmpty());
    }
}

Public Sub BatchStepOpenForm(ByVal Textbox As Textbox, ByVal DataMode As AcFormOpenDataMode, Optional ByVal BatchID As Variant = Null)

```
Imports System.Runtime.InteropServices
Imports Microsoft.Office.Interop.Access

<ComVisible(True)>
<Guid("0ABBE17C-CD4E-4B6C-B5B7-A9E70E6845E1")>
<ClassInterface(ClassInterfaceType.None)>
Public Class FlexGridWrapper
    Implements IFlexGridWrapper

    ' Double-click event handler
    Private Sub flexGrid_DoubleClick(sender As Object, e As EventArgs) Handles flexGrid.DoubleClick
        Dim selectedRow As Integer = flexGrid.Row
        Dim batchID As String = flexGrid.get_TextMatrix(selectedRow, 0) ' Assuming BatchID is in the first column

        ' Call the VBA method
        CallVBAFunction("BatchStepOpenForm", batchID)
    End Sub

    ' Method to call a VBA function in MS Access
    Public Sub CallVBAFunction(ByVal functionName As String, ByVal batchID As String)
        Try
            ' Get the running Access instance
            Dim accessApp As Object = Marshal.GetActiveObject("Access.Application")

            ' Open a hidden Access form that contains the target textbox (Replace 'frmHiddenForm' with the actual form name)
            accessApp.DoCmd.OpenForm "frmHiddenForm", AcFormView.acDesign

            ' Get the textbox control reference
            Dim txtBox As Object = accessApp.Forms("frmHiddenForm").Controls("txtYourTextbox")

            ' Call the VBA method with parameters
            accessApp.Run(functionName, txtBox, AcFormOpenDataMode.acFormEdit, batchID)

        Catch ex As Exception
            Throw New Exception("Error calling VBA function: " & ex.Message)
        End Try
    End Sub
End Class


' Double-click event handler
    Private Sub flexGrid_DoubleClick(sender As Object, e As EventArgs) Handles flexGrid.DoubleClick
        ' Call the VBA method from OpenFormRoutines module
        CallVBAFunction("OpenFormRoutines.BatchStepOpenForm")
    End Sub

    ' Method to call a VBA function in MS Access
    Public Sub CallVBAFunction(ByVal functionName As String)
        Try
            ' Get the running Access instance
            Dim accessApp As Object = Marshal.GetActiveObject("Access.Application")

            ' Call the VBA method
            accessApp.Run(functionName)

        Catch ex As Exception
            Throw New Exception("Error calling VBA function: " & ex.Message)
        End Try
```
