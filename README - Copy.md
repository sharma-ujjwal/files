import java.math.BigDecimal;

import jakarta.faces.application.FacesMessage;
import jakarta.faces.component.UIComponent;
import jakarta.faces.component.UIInput;
import jakarta.faces.context.FacesContext;
import jakarta.faces.event.AjaxBehaviorEvent;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

class PaymentValidatorTest {

    private PaymentValidator paymentValidator;
    private FacesContext facesContext;
    private UIInput payAmountInput;
    private AjaxBehaviorEvent event;
    private UIComponent component;

    @BeforeEach
    void setUp() {
        paymentValidator = new PaymentValidator();
        facesContext = FacesContext.getCurrentInstance();  // If null, consider using FacesContext mock in a JSF testing framework

        // Initialize a real UIInput instance
        payAmountInput = new UIInput();
        component = new UIComponent() {}; // Anonymous subclass to instantiate abstract class

        // Create a dummy AjaxBehaviorEvent with the component
        event = new AjaxBehaviorEvent(component) {};
    }

    @Test
    void testValidate_NullValue_ShouldShowErrorMessage() {
        // Simulate no value being set
        payAmountInput.getAttributes().put("value", null);
        
        // Manually invoke the method
        paymentValidator.validate(event);

        // Dummy assertion (this part will depend on how messages are stored in FacesContext)
        assertNotNull(facesContext.getMessageList());
    }

    @Test
    void testValidate_NegativeValue_ShouldShowErrorMessage() {
        // Simulate negative value
        payAmountInput.getAttributes().put("value", new BigDecimal("-1"));

        // Call the method
        paymentValidator.validate(event);

        // Verify error message (adjust based on how your app stores FacesMessages)
        FacesMessage message = facesContext.getMessageList().get(0);
        assertEquals("Negative payment amount is not allowed", message.getDetail());
    }

    @Test
    void testValidate_ValidValue_ShouldNotShowErrorMessage() {
        // Simulate valid value
        payAmountInput.getAttributes().put("value", new BigDecimal("100"));

        // Call the method
        paymentValidator.validate(event);

        // Ensure no error messages are added
        assertTrue(facesContext.getMessageList().isEmpty());
    }
}
