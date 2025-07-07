import org.junit.jupiter.api.Test;
import org.mockito.MockedStatic;

import javax.faces.component.UIComponent;
import javax.faces.event.ActionEvent;

import static org.mockito.Mockito.*;

public class YourBeanTest {

    private final YourBean bean = new YourBean(); // Replace with your actual bean class
    private final ReviewerUI reviewerUI = mock(ReviewerUI.class);
    private final ReviewDashboardUI reviewDashboardUI = mock(ReviewDashboardUI.class);
    private final ReviewersTable reviewersTable = mock(ReviewersTable.class); // or use HtmlDataTable or correct type

    @Test
    public void testDoSelectReviewer() {
        // Mocking ActionEvent and component tree
        ActionEvent event = mock(ActionEvent.class);
        UIComponent component = mock(UIComponent.class);

        when(event.getComponent()).thenReturn(component);
        when(component.findComponent("reviewersTable")).thenReturn(reviewersTable);
        when(reviewersTable.getRowData()).thenReturn(reviewerUI);

        // Mock the bean looked up from JSF
        ReviewUserDashboardBean reviewUserDashboardBean = mock(ReviewUserDashboardBean.class);

        // Mock NavigationHandler (assuming it's a separate class or static inner class)
        NavigationHandler navigationHandlerMock = mock(NavigationHandler.class);

        try (
                MockedStatic<JSFUtils> jsfUtilsMock = mockStatic(JSFUtils.class);
                MockedStatic<NavigationUtility> navUtilMock = mockStatic(NavigationUtility.class)
        ) {
            jsfUtilsMock.when(() -> JSFUtils.lookupBean("reviewUserDashboardBean"))
                    .thenReturn(reviewUserDashboardBean);

            navUtilMock.when(NavigationUtility::getNavigationHandlerInstance)
                    .thenReturn(navigationHandlerMock);

            navUtilMock.when(NavigationUtility::getFacesContextInstance)
                    .thenReturn(null); // Mock as needed or use a mock FacesContext

            // Run the method under test
            bean.setReviewDashboardUI(reviewDashboardUI); // assuming setter needed
            bean.doSelectReviewer(event);

            // Verify interaction
            verify(reviewUserDashboardBean).initReviewers(reviewerUI, reviewDashboardUI);
        }
    }
}
