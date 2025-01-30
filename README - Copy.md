public void validate(AjaxBehaviorEvent event) {
        UIComponent component = event.getComponent().findComponent("payAmount");
        Object value = component.getAttributes().get("value");

        if(value == null) {
            FacesContext.getCurrentInstance().addMessage(component.getClientId(), new FacesMessage(systemAlertService.retrieveErrorMessageByNumber(SystemAlertService.ERROR_MESSAGE_ERR544)));
        } else {
            final BigDecimal paymentAmount = (BigDecimal)value;

            if(paymentAmount.compareTo(BigDecimal.ZERO) < 0) {  // Negative amount
                FacesContext.getCurrentInstance().addMessage(component.getClientId(), new FacesMessage(systemAlertService.retrieveErrorMessageByNumber(SystemAlertService.ERROR_MESSAGE_ERR124)));
            }
        }
    }

import static org.mockito.Mockito.*;

import java.math.BigDecimal;

import jakarta.faces.application.FacesMessage;
import jakarta.faces.component.UIComponent;
import jakarta.faces.component.UIInput;
import jakarta.faces.context.FacesContext;
import jakarta.faces.event.AjaxBehaviorEvent;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.mockito.junit.jupiter.MockitoExtension;
import org.omnifaces.util.Faces;

@ExtendWith(MockitoExtension.class)
class PaymentValidatorTest {

    @InjectMocks
    private PaymentValidator paymentValidator;  // Assuming this is your class name

    @Mock
    private SystemAlertService systemAlertService;

    @Mock
    private AjaxBehaviorEvent event;

    @Mock
    private UIComponent component;

    @Mock
    private FacesContext facesContext;

    private static final String ERROR_MSG_544 = "Payment amount is required";
    private static final String ERROR_MSG_124 = "Negative payment amount is not allowed";

    @BeforeEach
    void setUp() {
        Faces.setContext(facesContext); // Required for unit testing FacesContext
        when(systemAlertService.retrieveErrorMessageByNumber(SystemAlertService.ERROR_MESSAGE_ERR544))
                .thenReturn(ERROR_MSG_544);
        when(systemAlertService.retrieveErrorMessageByNumber(SystemAlertService.ERROR_MESSAGE_ERR124))
                .thenReturn(ERROR_MSG_124);
    }

    @Test
    void testValidate_NullValue_ShouldShowErrorMessage() {
        UIInput payAmountInput = mock(UIInput.class);
        when(payAmountInput.getAttributes()).thenReturn(Mockito.mock(UIInput.class).getAttributes());
        when(payAmountInput.getAttributes().get("value")).thenReturn(null);

        when(event.getComponent()).thenReturn(component);
        when(component.findComponent("payAmount")).thenReturn(payAmountInput);

        paymentValidator.validate(event);

        verify(facesContext).addMessage(eq(payAmountInput.getClientId()), argThat(msg ->
                msg.getDetail().equals(ERROR_MSG_544)
        ));
    }

    @Test
    void testValidate_NegativeValue_ShouldShowErrorMessage() {
        UIInput payAmountInput = mock(UIInput.class);
        when(payAmountInput.getAttributes()).thenReturn(Mockito.mock(UIInput.class).getAttributes());
        when(payAmountInput.getAttributes().get("value")).thenReturn(new BigDecimal("-1"));

        when(event.getComponent()).thenReturn(component);
        when(component.findComponent("payAmount")).thenReturn(payAmountInput);

        paymentValidator.validate(event);

        verify(facesContext).addMessage(eq(payAmountInput.getClientId()), argThat(msg ->
                msg.getDetail().equals(ERROR_MSG_124)
        ));
    }

    @Test
    void testValidate_ValidValue_ShouldNotShowErrorMessage() {
        UIInput payAmountInput = mock(UIInput.class);
        when(payAmountInput.getAttributes()).thenReturn(Mockito.mock(UIInput.class).getAttributes());
        when(payAmountInput.getAttributes().get("value")).thenReturn(new BigDecimal("100"));

        when(event.getComponent()).thenReturn(component);
        when(component.findComponent("payAmount")).thenReturn(payAmountInput);

        paymentValidator.validate(event);

        verify(facesContext, never()).addMessage(anyString(), any(FacesMessage.class));
    }
}
