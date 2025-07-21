```
public class CustomExceptionHandler extends ExceptionHandlerWrapper {
    private final ExceptionHandler wrapped;

    public CustomExceptionHandler(ExceptionHandler wrapped) {
        this.wrapped = wrapped;
    }

    @Override
    public ExceptionHandler getWrapped() {
        return wrapped;
    }

    @Override
    public void handle() throws FacesException {
        for (Iterator<ExceptionQueuedEvent> i = getUnhandledExceptionQueuedEvents().iterator(); i.hasNext();) {
            Throwable t = i.next().getContext().getException();
            if (t instanceof ViewExpiredException) {
                try {
                    FacesContext context = FacesContext.getCurrentInstance();
                    context.getExternalContext().redirect("https://your-url.com");
                    context.responseComplete();
                } catch (IOException e) {
                    e.printStackTrace();
                } finally {
                    i.remove();
                }
            }
        }
        getWrapped().handle(); // Let others flow through
    }
}

public class CustomExceptionHandlerFactory extends ExceptionHandlerFactory {
    private final ExceptionHandlerFactory parent;

    public CustomExceptionHandlerFactory(ExceptionHandlerFactory parent) {
        this.parent = parent;
    }

    @Override
    public ExceptionHandler getExceptionHandler() {
        return new CustomExceptionHandler(parent.getExceptionHandler());
    }
}

<application>
  <exception-handler-factory>
    com.assurant.inc.sox.ar.exceptionhandling.CustomExceptionHandlerFactory
  </exception-handler-factory>
</application>
