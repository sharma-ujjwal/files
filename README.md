```
<ui:define name="head">
    <style>
        .custom-table.header-red thead th {
            background-color: #FFE5E6 !important;
            color: black !important;
        }

        .custom-table.header-blue thead th {
            background-color: #3d80df !important;
            color: white !important;
        }
    </style>
</ui:define>

<p:dataTable styleClass="custom-table #{accessListBean.headerColorClass}" ...>

public String getHeaderColorClass() {
    if (review != null && review.isSODReview()) {
        return "header-red";
    } else {
        return "header-blue";
    }
}
