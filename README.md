```
for given html, if Name value is longer, then, all the td length is adjuested to Name td value, can we make it independent so that all td width wont get affacted by a single td?
<table class="formtableDep dependentClass">
				<tr>
					<td class="rightLabelBold">Effective date:</td>
					<td><h:outputText value="#{dependent.effectiveDate}" /></td>
					<td></td>
					<td></td>
				</tr>
				<tr>
					<td class="rightLabelBold">Name:</td>
					<td><h:outputText value="#{dependent.formattedName}" /></td>
					<td></td>
					<td></td>
				</tr>
				<tr>
					<td class="rightLabelBold">Date of Birth:</td>
					<td><h:outputText value="#{dependent.dateOfBirth}" /></td>
					<td class="rightLabelBold">Gender:</td>
					<td><h:outputText value="#{dependent.gender}" /></td>
				</tr>
				<tr>
					<td class="rightLabelBold">Preferred Language:</td>
					<td><h:outputText value="#{dependent.preferredLanguage}" /></td>
					<td></td>
					<td></td>
				</tr>
				<tr>
					<td class="rightLabelBold">Facility ID:</td>
					<td><h:outputText value="#{dependent.facilityId}" /></td>
					<td><h:selectBooleanCheckbox value="#{dependent.establishedPatient}" /></td>
					<td class="leftLabelBold">Established Patient?</td>
				</tr>
			</table>
<img width="1903" height="365" alt="image" src="https://github.com/user-attachments/assets/4dc95abc-200e-436a-b5b7-b4cf9480e686" />
