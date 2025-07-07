```
@Test
    public void testDoSelectReviewer() {
        ActionEvent event = mock(ActionEvent.class);
        UIComponent component = mock(UIComponent.class);

        when(event.getComponent()).thenReturn(component);
        when(component.findComponent("reviewersTable")).thenReturn(reviewersTable);
        when(reviewersTable.getRowData()).thenReturn(reviewerUI);

        ReviewUserDashboardBean reviewUserDashboardBean = mock(ReviewUserDashboardBean.class);

        try (
                MockedStatic<JSFUtils> jsfUtilsMock = mockStatic(JSFUtils.class);
                MockedStatic<NavigationUtility> navUtilMock = mockStatic(NavigationUtility.class)
        ) {
            jsfUtilsMock.when(() -> JSFUtils.lookupBean("reviewUserDashboardBean"))
                    .thenReturn(reviewUserDashboardBean);

            // Correctly mock the inner class NavigationHandler
            NavigationUtility navigationHandlerMock = mock(NavigationUtility.class);

            navUtilMock.when(NavigationUtility::getNavigationHandlerInstance).thenReturn(navigationHandlerMock);
            navUtilMock.when(NavigationUtility::getFacesContextInstance).thenReturn(null);

            bean.doSelectReviewer(event);

            verify(reviewUserDashboardBean).initReviewers(reviewerUI, reviewDashboardUI);
        }
    }

org.mockito.exceptions.misusing.WrongTypeOfReturnValue: 
NavigationUtility cannot be returned by getNavigationHandlerInstance()
getNavigationHandlerInstance() should return NavigationHandler
***
If you're unsure why you're getting above error read on.
Due to the nature of the syntax above problem might occur because:
1. This exception *might* occur in wrongly written multi-threaded tests.
   Please refer to Mockito FAQ on limitations of concurrency testing.
2. A spy is stubbed using when(spy.foo()).then() syntax. It is safer to stub spies - 
   - with doReturn|Throw() family of methods. More in javadocs for Mockito.spy() method.
