```
<script type="text/javascript">
    function setDepartmentValue(deptName) {
        const dropdown = document.getElementById('bodyForm:tabPanel:addUserForm:departmentListbox');
        for (let i = 0; i < dropdown.options.length; i++) {
            if (dropdown.options[i].text.trim() === deptName.trim()) {
                dropdown.selectedIndex = i;
                dropdown.dispatchEvent(new Event('change'));
                break;
            }
        }
    }

    function setDivisionValue(divisionName) {
        const dropdown = document.getElementById('bodyForm:tabPanel:addUserForm:divisionListbox');
        for (let i = 0; i < dropdown.options.length; i++) {
            if (dropdown.options[i].text.trim() === divisionName.trim()) {
                dropdown.selectedIndex = i;
                dropdown.dispatchEvent(new Event('change'));
                break;
            }
        }
    }

    function setBusinessSegValue(businessSeg) {
        const dropdown = document.getElementById('bodyForm:tabPanel:addUserForm:bsnssSgmntListbox');
        for (let i = 0; i < dropdown.options.length; i++) {
            if (dropdown.options[i].text.trim() === businessSeg.trim()) {
                dropdown.selectedIndex = i;
                dropdown.dispatchEvent(new Event('change'));
                break;
            }
        }
    }

    function setJobTitleValue(jobTitle) {
        const dropdown = document.getElementById('bodyForm:tabPanel:addUserForm:jobTitleListbox');
        for (let i = 0; i < dropdown.options.length; i++) {
            if (dropdown.options[i].text.trim() === jobTitle.trim()) {
                dropdown.selectedIndex = i;
                dropdown.dispatchEvent(new Event('change'));
                break;
            }
        }
    }

    function setLocationValue(location) {
        const dropdown = document.getElementById('bodyForm:tabPanel:addUserForm:locationListbox');
        for (let i = 0; i < dropdown.options.length; i++) {
            if (dropdown.options[i].text.trim() === location.trim()) {
                dropdown.selectedIndex = i;
                dropdown.dispatchEvent(new Event('change'));
                break;
            }
        }
    }
</script>
