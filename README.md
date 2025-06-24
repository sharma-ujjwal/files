```
function selectAllCheckBox(event, parentElementId) {
    event.preventDefault();
    var parentElement = document.getElementById(parentElementId);
    var inputs = parentElement.querySelectorAll('input[type="checkbox"]');
    
    // Get the current state from the first checkbox (if exists)
    if (inputs.length > 0) {
        checkedToggleValue = inputs[0].checked;
    }
    
    inputs.forEach(function(input) {
        input.checked = !checkedToggleValue;
        // Trigger PrimeFaces change event properly
        $(input).change();
    });
    
    checkedToggleValue = !checkedToggleValue;
}
