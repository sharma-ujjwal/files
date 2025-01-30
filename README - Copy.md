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
