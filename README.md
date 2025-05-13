```
<p:dataTable
                        id="outstandingReviewsTable"
                        value="#{reviewManagementDashboardBean.outstandingReviews}"
                        var="review"
                        pagination="true"
                        paginator="true"
                        rowIndexVar="rowIndex"
                        rowKeyVar="rowKey"
                        paginatorPosition="bottom"
                        scrollable="true"
                        paginatorTemplate="{CurrentPageReport} {FirstPageLink} {PreviousPageLink} {PageLinks} {NextPageLink} {LastPageLink} {RowsPerPageDropdown}"
                        currentPageReportTemplate="{startRecord}-{endRecord} of {totalRecords} records"
                        rowsPerPageTemplate="10,25,50,100"
                        styleClass="defaultTableHeader"
                        rowStyleClasses="oddRow, evenRow"
                        rows="#{reviewManagementDashboardBean.displayAmountForOutstanding}"
                        style="width:100%" class="ui-datatable"
                        binding="#{reviewManagementDashboardBean.outstandingReviewsTable}">
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="Review Type" action="#{reviewManagementDashboardBean.doSort}"
                                           styleClass="headerSortLink">
                                <f:param name="column" value="type"/>
                            </p:commandLink>
                        </f:facet>
                        <h:outputText value="#{review.type}"/>
                    </p:column>
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="Review" action="#{reviewManagementDashboardBean.doSort}"
                                           styleClass="headerSortLink">
                                <f:param name="column" value="name"/>
                            </p:commandLink>
                        </f:facet>
                        <h:commandLink value="#{review.name}"
                                       action="#{reviewManagementDashboardBean.doSelectOutstandingReview}"/>
                    </p:column>
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="# Reports Distributed"
                                           action="#{reviewManagementDashboardBean.doSort}" styleClass="headerSortLink">
                                <f:param name="column" value="numberOfReportsDistributed"/>
                            </p:commandLink>
                        </f:facet>
                        <h:outputText value="#{review.numberOfReportsDistributed}"/>
                    </p:column>
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="# Reports Attested" action="#{reviewManagementDashboardBean.doSort}"
                                           styleClass="headerSortLink">
                                <f:param name="column" value="numberOfReportsAttested"/>
                            </p:commandLink>
                        </f:facet>
                        <h:outputText value="#{review.numberOfReportsAttested}"/>
                    </p:column>
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="% Reports Attested" action="#{reviewManagementDashboardBean.doSort}"
                                           styleClass="headerSortLink">
                                <f:param name="column" value="percentageOfReportsAttested"/>
                            </p:commandLink>
                        </f:facet>
                        <h:outputText value="#{review.percentageOfReportsAttested}"/>
                    </p:column>
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="# Users Included" action="#{reviewManagementDashboardBean.doSort}"
                                           styleClass="headerSortLink">
                                <f:param name="column" value="numberOfUsersIncluded"/>
                            </p:commandLink>
                        </f:facet>
                        <h:outputText value="#{review.numberOfUsersIncluded}"/>
                    </p:column>
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="# Users Attested" action="#{reviewManagementDashboardBean.doSort}"
                                           styleClass="headerSortLink">
                                <f:param name="column" value="numberOfUsersAttested"/>
                            </p:commandLink>
                        </f:facet>
                        <h:outputText value="#{review.numberOfUsersAttested}"/>
                    </p:column>
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="% Users Attested" action="#{reviewManagementDashboardBean.doSort}"
                                           styleClass="headerSortLink">
                                <f:param name="column" value="percentageOfUsersAttested"/>
                            </p:commandLink>
                        </f:facet>
                        <h:outputText value="#{review.percentageOfUsersAttested}"/>
                    </p:column>
                    <p:column>
                        <f:facet name="header">
                            <p:commandLink value="Review Created" action="#{reviewManagementDashboardBean.doSort}"
                                           styleClass="headerSortLink">
                                <f:param name="column" value="dateReviewCreated"/>
                            </p:commandLink>
                        </f:facet>
                        <h:outputText value="#{review.dateReviewCreated}">
                            <f:convertDateTime type="date" dateStyle="short" timeZone="#{applicationBean.timeZone}"/>
                        </h:outputText>
                    </p:column>
                </p:dataTable>
