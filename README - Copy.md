<ui:composition xmlns="http://www.w3.org/1999/xhtml"
                xmlns:f="http://xmlns.jcp.org/jsf/core"
                xmlns:h="http://xmlns.jcp.org/jsf/html"
                xmlns:p="http://primefaces.org/ui"
                xmlns:ui="http://xmlns.jcp.org/jsf/facelets">


    <h:panelGroup id="makePaymentHomeOfficePolicyContainer" layout="block" styleClass="makePaymentHomeOfficePolicyContainer noPaymentDivStyleClass">
        <ui:repeat id="hoPolicyIter" value="#{homeOfficeBillListDataBean.getHomeOfficePaymentPolicyCustomDataBeans()}"
                   var="hoPolicy" varStatus="hoPolicyStat" >


            <h:panelGroup class="errcb2 makePaymentPagelevelError" styleClass="makePaymentPagelevelError"  layout="block" style="background-color: white;"
                          rendered="#{homeOfficeBillListActionListener.showAutoPaymentErrorMessage()}">
                <h:outputText value="#{homeOfficeBillListActionListener.getError543()}" styleClass="errcb2"></h:outputText>
            </h:panelGroup>


            <!-- ============================================================================== -->
            <!-- policy             															-->
            <!-- ============================================================================== -->
            <div class="makePaymentHomeOfficePolicyHeader">

                <div class="makePaymentHomeOfficePolicyHeaderPolicyNumber">
                    <!-- overdue tooltip -->
                    <h:panelGroup styleClass="makePaymentPolicyLapsed"
                                  rendered="#{commonUtils.isLapsedCompassPolicy(hoPolicy.polNum)}">
                        <h:outputText id="overdueAlertTooltip" styleClass="makePaymentPolicyOverdue" />
                        <p:tooltip for="overdueAlertTooltip" value="#{staticContentDataBean.makePaymentEbillPolicyOverdueMouseoverURL}" position="top" escape="false"/>
                    </h:panelGroup>

                    <!-- policy number -->
                    <div class="CH makePaymentHomeOfficePolicyNumberText">Policy #{hoPolicy.polNum}</div>

                    <!-- view bill -->
                    <div class="makePaymentHomeOfficePolicyViewBillContainer ">
                        <p:commandLink styleClass="makePaymentHomeOfficePolicyViewBill viewBillStyleClass" value="View bill" actionListener="#{homeOfficeBillListActionListener.showCompassViewBillWarning}" process="@this" update="frmDialogContinueCancel" onstart="showPopup();" oncomplete="hidePopup();">
                            <f:setPropertyActionListener value="makePayment" target="#{billTermsAndConditionsActionListener.previousPage}"/>
                            <f:setPropertyActionListener value="#{hoPolicy.polNum}" target="#{billTermsAndConditionsActionListener.policy}"/>
                        </p:commandLink>
                    </div>
                </div>

                <!-- due on -->
                <div class="makePaymentHomeOfficePolicyHeaderDueOn">
                    <!-- &#160; is hex notation for &nbsp; -->
                    &#160;
                </div>

                <!-- total due -->
                <div class="makePaymentHomeOfficePolicyHeaderTotal">
                    <span class="makePaymentHomeOfficePolicyHeaderSmallText">Total: </span>
                    <span class="CH">
                            <h:outputText value="#{hoPolicy.totalAmt}" >
                                <f:convertNumber currencyCode="USD" type="currency" pattern="$###,###,##0.00;-$###,###,##0.00" />
                            </h:outputText>
                        </span>
                </div>

            </div> <!-- end home office policy header -->

            <!-- ============================================================================== -->
            <!-- billgroups and rules														    -->
            <!-- ============================================================================== -->
            <div class="makePaymentHomeOfficePaymentContainer ebillTableCss">

                <!-- column headings -->
                <div class="makePaymentHomeOfficeBillGroupHeaderContainer makePaymentHomeOfficeColumnHeader tableFontClass">
                    <div class="makePaymentHomeOfficeBillGroupHeaderLeft">
                        Bill description
                        <h:outputText id="billDescriptionInfoTooltip" styleClass="paymentSettingsMoreInfo singleLineToolTip" />
                        <p:tooltip for="billDescriptionInfoTooltip" value="#{staticContentDataBean.makePaymentEbillBillDescriptionInfoMouseoverURL}" position="top" escape="false"/>
                    </div>

                    <div class="makePaymentHomeOfficeBillGroupHeaderDateDue">
                        Due date
                    </div>

                    <div class="makePaymentHomeOfficeBillGroupHeaderAmount">
                        Balance of last bill
                        <h:outputText id="lastBalanceInfoTooltip" styleClass="paymentSettingsMoreInfo doubleLineToolTip" />
                        <p:tooltip for="lastBalanceInfoTooltip" value="#{staticContentDataBean.makePaymentEbillLastBillBalanceInfoMouseoverURL}" position="top" escape="false"/>
                    </div>

                    <div class="makePaymentHomeOfficeBillGroupHeaderAmount" style="width: 83px">
                        Financial adjustments
                        <h:outputText id="financialAdjInfoTooltip" styleClass="paymentSettingsMoreInfo doubleLineToolTip" />
                        <p:tooltip for="financialAdjInfoTooltip" value="#{staticContentDataBean.makePaymentEbillFinancialAdjustmentsInfoMouseoverURL}" position="top" escape="false"/>
                    </div>

                    <div class="makePaymentHomeOfficeBillGroupHeaderAmount">
                        Recent payments
                        <h:outputText id="recentPaymentsInfoTooltip" styleClass="paymentSettingsMoreInfo doubleLineToolTip" />
                        <p:tooltip for="recentPaymentsInfoTooltip" value="#{staticContentDataBean.makePaymentEbillRecentPaymentsInfoMouseoverURL}" position="top" escape="false"/>
                    </div>

                    <div class="makePaymentHomeOfficeBillGroupHeaderDateDue">
                        Amount due
                    </div>

                    <div class="makePaymentHomeOfficeBillGroupHeaderRight">
                        Payment amount
                    </div>

                </div>

                <ui:repeat id="billgroupIter" value="#{hoPolicy.homeOfficeDTOs}" var="billgroup" varStatus="groupStat">

                    <div class="makePaymentHomeOfficeBillGroupContainer ebillTableCss">

                        <div class="makePaymentHomeOfficeBillGroupDetailRow">
                            <!-- billgroup -->
                            <div class="makePaymentHomeOfficeBillGroupDetailLeftColumn  billGrpTextStyle">
                                #{billgroup.locationCd} - #{billgroup.locationNm}
                            </div>
                            <div class="makePaymentHomeOfficeBillGroupRuleDateDue billTextColourClass">
                                <h:outputText value="&#160;"/>
                            </div>
                            <!-- total last bill balance -->
                            <div class="makePaymentHomeOfficeBillGroupDetailAmountColumn billTextColourClass">
                                <h:outputText value="#{billgroup.totalBalanceLastBill}">
                                    <f:convertNumber currencyCode="USD" type="currency" pattern="$###,###,##0.00;-$###,###,##0.00" />
                                </h:outputText>
                            </div>
                            <!-- total financial adjustments -->
                            <div class="makePaymentHomeOfficeBillGroupDetailAmountColumn billTextColourClass" style="width:84px;">
                                <h:outputText value="#{billgroup.totalFinAdjustments}" >
                                    <f:convertNumber currencyCode="USD" type="currency" pattern="$###,###,##0.00;-$###,###,##0.00" />
                                </h:outputText>
                            </div>
                            <!-- total recent payments -->
                            <div class="makePaymentHomeOfficeBillGroupDetailAmountColumn billTextColourClass">
                                <h:outputText value="#{billgroup.totalRecentPayments}" >
                                    <f:convertNumber currencyCode="USD" type="currency" pattern="$###,###,##0.00;-$###,###,##0.00" />
                                </h:outputText>
                            </div>
                            <!-- total amount due -->
                            <div class="makePaymentHomeOfficeBillGroupRuleDetailAmountDueColumn billTextColourClass">
                                    <span style="font-size:12px;">
                                        <h:outputText value="#{billgroup.totalAmountDue}" >
                                            <f:convertNumber currencyCode="USD" type="currency" pattern="$###,###,##0.00;-$###,###,##0.00" />
                                        </h:outputText>
                                    </span>
                            </div>
                            <!--Schedule a one time paymeny link-->
                            <div align="center">
                                <h:panelGroup id="scheduleAPaymentContainer" rendered="#{homeOfficeBillListActionListener.displayScheduleOneTimePaymentLink(billgroup)}">
                                            <span id="scheduleAPaymentLink" class="nl1" onclick="lnkScheduleOneTimePayment_homeoffice(this.parentElement.id,'#{groupStat.index}'); this.style.display = 'none';" style="cursor: pointer;">
                                                Schedule payment
                                            </span>
                                </h:panelGroup>
                            </div>
                        </div>

                        <ui:repeat id="ruleIter" value="#{billgroup.paymentsBillGroupRuleDTOs}" var="rule">
                            <div class="makePaymentHomeOfficeBillGroupRuleDetailRow ebillTableCss">
                                <!-- billgroup rule -->
                                <div class="makePaymentHomeOfficeBillGroupRuleDetailLeftColumn">
                                    <span style="float: left;">
                                        <p:commandLink value="#{rule.billGroupRuleName}"
                                                       styleClass="billDescLink"
                                                       action="#{homeOfficeBillListActionListener.showRecentActivity(hoPolicy.polNum,billgroup.locationCd,rule.billGrpRuleId)}"
                                                       process="@this slcDialogRecentActivity"
                                                       update="slcDialogRecentActivity"
                                                       onstart="showPopup();"
                                                       oncomplete="hidePopup();" />
                                        <sup><h:outputText value="#{homeOfficeBillListActionListener.getPaymentStatusIndex(rule)}">
                                        </h:outputText></sup>
                                    </span>
                                </div>
                                <div class="makePaymentHomeOfficeBillGroupRuleDateDue billTextColourClass">
                                    <h:outputText value="#{rule.billDueDate}" >
                                        <f:convertDateTime type="date" pattern="MM/dd/yyyy" />
                                    </h:outputText>
                                </div>
                                <!-- total last bill balance -->
                                <div class="makePaymentHomeOfficeBillGroupDetailAmountColumn billTextColourClass">
                                    <h:outputText value="#{rule.balanceLastBill}">
                                        <f:convertNumber currencyCode="USD" type="currency" pattern="$###,###,##0.00;-$###,###,##0.00" />
                                    </h:outputText>
                                </div>
                                <!-- financial adjustments -->
                                <div class="makePaymentHomeOfficeBillGroupRuleDetailAmountColumn billTextColourClass" style="width:84px;">
                                    <h:outputText value="#{rule.finAdjustments}" >
                                        <f:convertNumber currencyCode="USD" type="currency" pattern="$###,###,##0.00;-$###,###,##0.00" />
                                    </h:outputText>
                                </div>
                                <!-- recent payments -->
                                <div class="makePaymentHomeOfficeBillGroupDetailAmountColumn billTextColourClass">
                                    <h:outputText value="#{rule.recentPayments}" >
                                        <f:convertNumber currencyCode="USD" type="currency" pattern="$###,###,##0.00;-$###,###,##0.00" />
                                    </h:outputText>
                                </div>
                                <!-- amount due -->
                                <div class="makePaymentHomeOfficeBillGroupRuleDetailAmountDueColumn billTextColourClass">
                                    <span style="vertical-align:top;">
                                        <h:outputText value="#{rule.amountDue}" >
                                            <f:convertNumber currencyCode="USD" type="currency" pattern="$###,###,##0.00;-$###,###,##0.00" />
                                        </h:outputText>
                                    </span>
                                    <!-- over due warning -->
                                    <h:panelGroup rendered="#{homeOfficeBillListActionListener.isPaymentOverdue(rule)}">
                                        <h:outputText id="detailOverDueWarningTooltip" styleClass="makePaymentDetailOverdue" />
                                        <p:tooltip for="detailOverDueWarningTooltip" value="#{staticContentDataBean.makePaymentEbillLocationOverdueMouseoverURL}" position="top" escape="false"/>
                                    </h:panelGroup>
                                </div>

                                <!-- payment amount -->
                                <!-- note the use of a fake styleClass attribute; the class name is used in javascript as a jquery selector for component manipulation -->
                                <div class="makePaymentHomeOfficeBillGroupRuleDetailRightColumn">
                                    <div><h:message id="payAmountMsg" for="payAmount" styleClass="alert alert-danger"/></div>
                                    <h:inputText id="payAmount" process="@all" value="#{rule.paymentAmt}" styleClass="payAmount#{groupStat.index}" style="#{homeOfficeBillListActionListener.displayPaymentInputFields(billgroup)}">
                                        <f:convertNumber currencyCode="USD" minFractionDigits="2" />
                                        <f:attribute name="locationCd" value="#{billgroup.locationCd}" />
                                        <f:ajax event="click"
                                                listener="#{homeOfficeBillListActionListener.homeOfficePaymentAmountChanged}"
                                                render="makePaymentTabView:homeOfficePolicyForm:hoPolicyIter:billgroupIter:totalPaymentAmount"/>
                                        <f:validator binding="#{paymentAmountValidator}"/>
                                    </h:inputText>
                                </div>
                            </div>
                        </ui:repeat>
                    </div>

                    <!-- ============================================================================== -->
                    <!-- payments          															    -->
                    <!-- ============================================================================== -->

                    <h:panelGroup layout="block" styleClass="makePaymentHomeOfficeBillGroupPaymentContainer makePaymentHomeOfficeThickBorder">

                        <h:panelGroup id="makePaymentEbillLocationPaymentDetailContainer"
                                      styleClass="makePaymentHomeOfficeBillGroupPaymentDetailContainer"
                                      style="#{homeOfficeBillListActionListener.displayPaymentInputFields(billgroup)}">

                            <div class="makePaymentHomeOfficeBillGroupPaymentDetailErrorRow">
                                <div class="makePaymentHomeOfficeBillGroupPaymentDetailLeftColumn">
                                    <label id="errProfileId_906982_002" class="em3"></label>
                                </div>
                                <div class="makePaymentHomeOfficeBillGroupPaymentDetailCenterColumn">
                                    <label id="errPaymentScheduleDt_906982_002" class="em3"></label>
                                </div>
                                <div class="makePaymentHomeOfficeBillGroupPaymentDetailRightColumn">
                                    <label id="errPaymentAmt_906982_002" class="em3"></label>
                                </div>
                            </div>
                            <div class="makePaymentHomeOfficeBillGroupPaymentDetailRowOne">
                                <div class="makePaymentHomeOfficeBillGroupPaymentDetailLeftColumn payWithFontStyle">Pay with:</div>
                                <div class="makePaymentHomeOfficeBillGroupPaymentDetailCenterColumn payWithFontStyle">
                                    Schedule for:
                                    <h:outputText id="scheduleForTooltip" styleClass="paymentSettingsMoreInfo" />
                                    <p:tooltip for="scheduleForTooltip" value="#{staticContentDataBean.makePaymentScheduleForInfoMouseoverURL}" position="top" escape="false"/>
                                </div>
                                <div class="makePaymentHomeOfficeBillGroupPaymentDetailRightColumn payWithFontStyle">Total payment amount:</div>
                            </div>

                            <div class="makePaymentHomeOfficeBillGroupPaymentDetailRowTwo">
                                <!-- payment profile -->
                                <div class="makePaymentHomeOfficeBillGroupPaymentDetailLeftColumn">
                                    <div><h:message id="paymentProfileMsg" for="paymentProfile" styleClass="alert alert-danger"/></div>
                                    <h:selectOneMenu id="paymentProfile"
                                                     value="#{billgroup.paymentProfileId}">
                                        <f:selectItem itemLabel="Select or add a new profile" noSelectionOption="true" />
                                        <f:selectItems itemLabel="#{profile.bankAccNm}"
                                                       itemValue="#{profile.paymentProfileId}"
                                                       var="profile"
                                                       value="#{billgroup.billPaymentProfileDTOs}" />
                                        <f:ajax event="change" listener="#{homeOfficeBillListActionListener.homeOfficePaymentProfileChanged}" />
                                        <f:attribute name="paymentAmount" value="#{homeOfficeBillListDataBean.retrieveHomeOfficeTotalPaymentAmount(billgroup.locationCd)}" />
                                        <f:validator binding="#{requiredMakeAPaymentFieldsValidator}" />
                                    </h:selectOneMenu>
                                    <!-- &#160; is hex notation for &nbsp; -->
                                    &#160;&#160;
                                    <p:commandLink id="addProfileLink" action="#{paymentProfilesListActionListener.retrievePaymentProfiles}" value="Add new profile" styleClass="nl1" onstart="showPopup();" oncomplete="hidePopup();" />
                                    <h:outputText id="addProfileTooltip" styleClass="makePaymentMoreInfo" />
                                    <p:tooltip for="addProfileTooltip" value="#{staticContentDataBean.makePaymentAddProfileInfoMouseoverURL}" position="top" escape="false"/>
                                </div>
                                <!-- payment date -->
                                <div class="makePaymentHomeOfficeBillGroupPaymentDetailCenterColumn scheduleForStyingCls">
                                    <div><h:message id="scheduleForMsg" for="scheduleFor" styleClass="alert alert-danger"/></div>
                                    <p:calendar id="scheduleFor"
                                                styleClass="makePaymentHomeOfficePaymentCalendar"
                                                value="#{billgroup.paymentScheduleDt}"
                                                pattern="MM/dd/yyyy"
                                                mask="true" size="10">
                                        <f:ajax event="dateSelect" listener="#{homeOfficeBillListActionListener.homeOfficePaymentDateChanged}" />
                                        <f:ajax event="change" listener="#{homeOfficeBillListActionListener.homeOfficePaymentDateChanged}" />
                                        <f:attribute name="paymentAmount" value="#{homeOfficeBillListDataBean.retrieveHomeOfficeTotalPaymentAmount(billgroup.locationCd)}" />
                                        <f:validator binding="#{requiredMakeAPaymentFieldsValidator}" />
                                    </p:calendar>
                                    <!-- &#160; is hex notation for &nbsp; -->
                                    &#160;&#160;
                                    <p:commandLink action="#{autopaymentActionListener.retrieveAutopaymentsForLandingPage}" value="Schedule for autopay" styleClass="nl1" onstart="showPopup();" oncomplete="hidePopup();" />

                                </div>
                                <!-- total payment amount -->
                                <h:panelGroup id="totalPaymentAmount">
                                    <div class="makePaymentHomeOfficeBillGroupPaymentDetailRightColumn payWithFontStyle">
                                        <h:outputText value="#{homeOfficeBillListDataBean.retrieveHomeOfficeTotalPaymentAmount(billgroup.locationCd)}" >
                                            <f:convertNumber currencyCode="USD" type="currency" pattern="$###,###,##0.00;-$###,###,##0.00" />
                                        </h:outputText>
                                    </div>
                                </h:panelGroup>

                            </div>

                            <div class="makePaymentHomeOfficeLocationPaymentDetailRowThree">
                                <div class="makePaymentHomeOfficeBillGroupPaymentDetailLeftColumn">
                                    <!-- set as default payment profile link -->
                                    <h:commandLink action="#{homeOfficeBillListActionListener.confirmCancellation}" value="Set as default payment profile" styleClass="nl1" onclick="showPopup();" />
                                </div>
                                <div class="makePaymentHomeOfficeBillGroupPaymentDetailCenterColumn">
                                    <!-- &#160; is hex notation for &nbsp; -->
                                    &#160;
                                </div>
                                <div class="makePaymentHomeOfficeBillGroupPaymentDetailRightColumn">
                                    <!-- &#160; is hex notation for &nbsp; -->
                                    &#160;
                                </div>
                            </div>

                        </h:panelGroup>

                    </h:panelGroup>

                </ui:repeat>
                <div class="makePaymentHomeOfficeLocationAmountDueInfoRow" style="margin-bottom:10px;">
                    <!-- View Details link -->
                    <div class="makePaymentHomeOfficeLocationAmountDueInfoLeftColumn ebillTableCss">
                        <p:commandLink id="compassToViewDetails" action="#{paymentHistoryActionListener.retrievePaymentHistory}"
                                       value="View details" styleClass="nl1" onstart="showPopup();" oncomplete="hidePopup();" />
                    </div>
                </div>

                <!-- footnote status for the bill group rules (Autopayment/Payment processing/No payment required)-->
                <div class="makePaymentHomeOfficeLocationAmountDueInfoRow" style="margin-bottom:10px;">
                    <ui:repeat id="statusIter" value="#{homeOfficeBillListDataBean.getPaymentStatusList()}"
                               var="status" varStatus="rptStsVar">
                        <div class="makePaymentDetailLargeInfoMessage" style="margin-bottom:7px;">
                            <h:outputText value="#{rptStsVar.getIndex() + 1}" styleClass="paymentStatusLabel"></h:outputText>
                            <h:outputText value="#{homeOfficeBillListActionListener.getPaymentStatusLabel(status)}" />
                        </div>
                    </ui:repeat>
                </div>
            </div> <!-- end home office payment container -->

        </ui:repeat>

        <h:inputHidden id="agreedToTerms" value="#{sessionValueDataBean.agreedToBillTermsAndConditions}" />

    </h:panelGroup> <!-- end home office policy container -->

</ui:composition>


not rendering the total amount when user enter the amount in textbox
        render="@form:hoPolicyIter:billgroupIter:totalPaymentAmount"/>
