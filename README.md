```
@Component("tableMaintenanceBean")
@Scope("session")
public class TableMaintenanceBean {

    private String selectedTable = TableMaintenanceCode.DEPARTMENTS.name();
    private String tablePagePath = "/xhtml/admin/departmentSummary.xhtml";

    public String getSelectedTable() {
        return selectedTable;
    }

    public void setSelectedTable(String selectedTable) {
        this.selectedTable = selectedTable;
    }

    public String getTablePagePath() {
        return tablePagePath;
    }

    public void updateTablePagePath() {
        TableMaintenanceCode code = TableMaintenanceCode.valueOf(selectedTable);
        this.tablePagePath = switch (code) {
            case USERTABLE -> "/xhtml/admin/userSummary.xhtml";
            case USERTYPE -> "/xhtml/admin/userTypeSummary.xhtml";
            case APPLICATION -> "/xhtml/admin/applicationSummary.xhtml";
            default -> "/xhtml/admin/departmentSummary.xhtml";
        };
    }

    public void onTableDropdownChange() {
        updateTablePagePath(); // No recursion
    }

    public void resetToDefaultTable() {
        this.selectedTable = TableMaintenanceCode.DEPARTMENTS.name();
        updateTablePagePath();
    }
}
