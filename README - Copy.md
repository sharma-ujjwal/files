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
