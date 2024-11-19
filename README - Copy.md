<ui:composition xmlns="http://www.w3.org/1999/xhtml"
                xmlns:f="http://xmlns.jcp.org/jsf/core"
                xmlns:h="http://xmlns.jcp.org/jsf/html"
                xmlns:p="http://primefaces.org/ui"
                xmlns:ui="http://xmlns.jcp.org/jsf/facelets">

    <h:head>
        <h:outputStylesheet library="css" name="homeOfficePayments.css" />
        <script src="https://kit.fontawesome.com/ca50ac5870.js" crossorigin="anonymous"></script>
    </h:head>

    <div id="makePaymentPoliciesMarketingContainer">
        <h:outputText value="#{staticContentDataBean.makePaymentEbillBelowTabURL}" escape="false" />
    </div>
    <h:form id="homeOfficePolicyFilterForm" styleClass="homeOfficePolicyFilterForm CQ">
        <h:panelGroup layout="block" id="eBillHoPolicySelection">
            <h:outputLabel value="Policy" for="homeOfficeFilterPolicyList" style="margin-right: 5px;"/>
            <h:selectOneMenu id="homeOfficeFilterPolicyList" value="#{homeOfficeBillListDataBean.policyNumberSelection}">
                <f:selectItem itemLabel="All policies" itemValue="ALL" />
                <f:selectItems itemLabel="#{policyNumber}" itemValue="#{policyNumber}" var="policyNumber" value="#{sessionValueDataBean.getPolicyNumberSelectionList(3)}" />
                <p:ajax listener="#{paymentsHelperBean.filterPaymentInfo(homeOfficeBillListDataBean.policyNumberSelection)}"
                        update="makePaymentTabView:homeOfficePolicyForm" />
            </h:selectOneMenu>
        </h:panelGroup>
    </h:form>

    <h:panelGroup layout="block" styleClass="makePaymentSelfBillLapsedPageLevelError" rendered="#{commonUtils.hasLapsedOasisEBillPolicy(sessionValueDataBean.getPolicyNumberSelectionList(3))}">
        <i class="fas fa-exclamation-triangle warningIcon lapsedWarningIcon" ></i>
        <div class="makePaymentSelfBillLapsedPageLevelMessage">
            <h:outputText value="#{homeOfficeBillListActionListener.error519}" />
        </div>
    </h:panelGroup>

    <h:panelGroup layout="block" styleClass="makePaymentSelfBillLapsedPageLevelError" rendered="#{commonUtils.hasPreLapsedHOPolicy(sessionValueDataBean.getPolicyNumberSelectionList(3))}">
        <i class="fas fa-exclamation-triangle warningIcon lapsedWarningIcon" ></i>
        <div class="makePaymentSelfBillLapsedPageLevelMessage">
            <h:outputText value="#{homeOfficeBillListActionListener.error609}" />
        </div>
    </h:panelGroup>
    <!-- ============================================================================== -->
    <!-- page level application messages    											-->
    <!-- ============================================================================== -->


    <!-- ============================================================================== -->
    <!-- page level Faces (system and/or application) messages							-->
    <!-- ============================================================================== -->
    <h:messages id="errPageLevelMessage" globalOnly="true" layout="table" styleClass="errcb2 makePaymentPagelevelError" />

    <h:form id="homeOfficePolicyForm">

        <ui:include src="eBillPayments.xhtml" />

        <ui:include src="homeOfficePayments.xhtml" />

        <!-- ============================================================================== -->
        <!-- button controls 																-->
        <!-- ============================================================================== -->
        <div id="makePaymentFooterContainer paymtStyleClassStaticMsg">
            <!-- notification -->
            <div class="makePaymentFooterMessage">
                NOTE: You will have an opportunity to review your payment details before submitting your payment.
            </div>

            <!-- Continue button -->
            <div class="makePaymentButtonContainer" id="makePaymentButtons">
                <div id="makePaymentContinueButton">
                    <div class="button submitBtn" style="margin-right:10px;">
                        <div class="left" />
                        <p:commandButton value="Continue" action="#{homeOfficeBillListActionListener.confirmContinue}"
                                         process="@this makePaymentTabView:errPageLevelMessage frmDialogYN"
                                         update="makePaymentTabView:errPageLevelMessage frmDialogYN"
                                         onstart="showPopup();"
                                         oncomplete="hidePopup();"
                                         styleClass="makePaymentContStyle"  >
                        </p:commandButton>
                        <div class="right" />
                    </div>
                </div>
            </div>
        </div>

    </h:form>

    <h:outputScript library="js" name="makePaymentEbill.js" />
</ui:composition>