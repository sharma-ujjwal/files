I have a datatable, when i click on row's commandLink, a popup comes with an option to cancel aur navigate to next page. the catch is, when user chooses to nevigate to next page, that perticular row's data is being used in next page, how can I utilize that datatable row in next page. here the p:commandLink is <p:column headerText="Task">
				<h:commandLink id="#{idPrefix}TaskLink"
							 value="#{myAllTask.name}"
							 action="#{myTaskListBean.doWorkTask(myAllTask)}"/>
			</p:column> and the navigation one is 	<p:commandButton value="Break Lock" action="#{myTaskListBean.doBreakLock}" update="#{idPrefix}LockDialog" oncomplete="PF('lockDialog1').hide();" /> inside a p:dialog box
