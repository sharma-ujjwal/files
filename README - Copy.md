Option Compare Database

' Constants for pagination
Const ITEMS_PER_PAGE As Integer = 30

' Variables to track pagination state
Dim currentPage As Integer
Dim totalPages As Integer
Dim totalItems As Integer
Dim dummyData As Collection

Private Sub Detail_Click()

End Sub

Private Sub Form_Load()
    ' Initialize pagination
    currentPage = 1
    InitializeDummyData
    SetupTreeView
    LoadData
    UpdatePaginationControls
End Sub


Private Sub SetupTreeView()
    ' Set up the TreeView control
    With Me.lvwData
        .LineStyle = tvwRootLines
        .Style = tvwTreelinesPlusMinusText
        .Nodes.Clear
    End With

    ' Add root items and subitems
    AddTreeViewItem "Item 1", Array("SubItem 1", "SubItem 2", "SubItem 3", "SubItem 4", "SubItem 5")
    AddTreeViewItem "Item 2", Array("SubItem 1", "SubItem 2", "SubItem 3", "SubItem 4", "SubItem 5")
End Sub

Private Sub AddTreeViewItem(itemText As String, subItems As Variant)
    Dim tvw As Object
    Dim nodeItem As Object
    Set tvw = Me.lvwData.Object

    ' Add the main item
    Set nodeItem = tvw.Nodes.Add(, , , itemText)

    ' Add subitems under the main item
    Dim i As Integer
    For i = LBound(subItems) To UBound(subItems)
        tvw.Nodes.Add nodeItem, tvwChild, , subItems(i)
    Next i
End Sub

Private Sub InitializeDummyData()
    ' Initialize dummy data
    Set dummyData = New Collection
    Dim i As Integer
    For i = 1 To 100
        dummyData.Add Array("Item " & i, "SubItem " & i & " A", "SubItem " & i & " B", "SubItem " & i & " C", "SubItem " & i & " D", "SubItem " & i & " E")
    Next i
    totalItems = dummyData.Count
    totalPages = Int((totalItems - 1) / ITEMS_PER_PAGE) + 1
End Sub

Private Sub LoadData()
    Dim tvw As Object
    Dim nodeItem As Object
    Dim startIndex As Integer
    Dim endIndex As Integer
    Dim i As Integer
    Dim j As Integer

    ' Set the TreeView control
    Set tvw = Me.lvwData.Object

    ' Clear the TreeView
    tvw.Nodes.Clear

    ' Calculate the range of items to display on the current page
    startIndex = (currentPage - 1) * ITEMS_PER_PAGE + 1
    endIndex = startIndex + ITEMS_PER_PAGE - 1
    If endIndex > totalItems Then endIndex = totalItems

    ' Add items and subitems to the TreeView
    For i = startIndex To endIndex
        ' Add the main item
        Set nodeItem = tvw.Nodes.Add(, , , dummyData(i)(0)) ' Main item
        
        ' Add subitems under the main item
        For j = 1 To 5
            tvw.Nodes.Add nodeItem, tvwChild, , dummyData(i)(j)
        Next j
    Next i
End Sub

Private Sub UpdatePaginationControls()
    Me.lblPageInfo.Caption = "Page " & currentPage & " of " & totalPages & " | " & Format(Now, "hh:nn AM/PM")
    Me.btnFirstPage.Enabled = (currentPage > 1)
    Me.btnPreviousPage.Enabled = (currentPage > 1)
    Me.btnNextPage.Enabled = (currentPage < totalPages)
    Me.btnLastPage.Enabled = (currentPage < totalPages)
End Sub

Private Sub btnFirstPage_Click()
    currentPage = 1
    LoadData
    UpdatePaginationControls
End Sub

Private Sub btnPreviousPage_Click()
    If currentPage > 1 Then
        currentPage = currentPage - 1
        LoadData
        UpdatePaginationControls
    End If
End Sub

Private Sub btnNextPage_Click()
    If currentPage < totalPages Then
        currentPage = currentPage + 1
        LoadData
        UpdatePaginationControls
    End If
End Sub

Private Sub btnLastPage_Click()
    currentPage = totalPages
    LoadData
    UpdatePaginationControls
End Sub

Private Sub btnRefresh_Click()
    RefreshListView
End Sub
 
Private Sub RefreshListView()
    currentPage = 1
    LoadData
    UpdatePaginationControls
End Sub

Private Sub btnExpandAll_Click()
    Dim tvw As Object
    Set tvw = Me.lvwData.Object
    ExpandCollapseNodes tvw, True
End Sub

Private Sub btnCollapseAll_Click()
    Dim tvw As Object
    Set tvw = Me.lvwData.Object
    ExpandCollapseNodes tvw, False
End Sub

Private Sub ExpandCollapseNodes(tvw As Object, expand As Boolean)
    Dim Node As Object
    For Each Node In tvw.Nodes
        If Node.Parent Is Nothing Then ' Only expand/collapse main items
            If expand Then
                Node.Expanded = True
            Else
                Node.Expanded = False
            End If
        End If
    Next Node
End Sub

Private Sub lvwData_Updated(Code As Integer)

End Sub







To display details of a subitem in the textbox when you click on it in the TreeView, you need to handle the NodeClick event of the TreeView. Here's how you can modify your code:

Steps to Implement:

1. Add a TextBox control (e.g., txtSubItemDetails) to your form below the TreeView.


2. Handle the NodeClick event to capture the selected subitem and update the TextBox.



Updated Code:

Private Sub lvwData_NodeClick(ByVal Node As Object)
    ' Display the details of the clicked subitem in the textbox
    If Not Node Is Nothing Then
        Me.txtSubItemDetails.Value = "Selected Item: " & Node.Text
    End If
End Sub

Explanation:

The NodeClick event is triggered whenever a node in the TreeView is clicked.

The Node parameter represents the node that was clicked.

The code updates the txtSubItemDetails textbox with the text of the clicked node.


Full Integration:

Ensure that the txtSubItemDetails TextBox control is added to the form and its Name property is set to txtSubItemDetails.

Now, when you click on any item or subitem in the TreeView, the TextBox will display the corresponding text. You can further enhance this functionality to show additional det
ails if needed.




Private Sub AddTreeViewItem(itemText As String, subItems As Variant)
    Dim tvw As Object
    Dim nodeItem As Object
    Dim subItemCount As Integer
    Set tvw = Me.lvwData.Object

    ' Count the number of subitems
    subItemCount = UBound(subItems) - LBound(subItems) + 1

    ' Add the main item with subitem count
    Set nodeItem = tvw.Nodes.Add(, , , itemText & " (" & subItemCount & " SubItems)")

    ' Add subitems under the main item
    Dim i As Integer
    For i = LBound(subItems) To UBound(subItems)
        tvw.Nodes.Add nodeItem, tvwChild, , subItems(i)
    Next i
End Sub


Private Sub lvwData_NodeClick(ByVal Node As Object)
    If Node.Children > 0 Then
        Me.txtSubItemDetails.Value = "Selected Item: " & Node.Text & " (Parent Node)"
    Else
        Me.txtSubItemDetails.Value = "Selected SubItem: " & Node.Text
    End If
End Sub

=========================================================================================================================================================

Option Compare Database

' Constants for pagination
Const ITEMS_PER_PAGE As Integer = 30

' Variables to track pagination state
Dim currentPage As Integer
Dim totalPages As Integer
Dim totalItems As Integer
Dim dummyData As Collection

Private Sub Form_Load()
    ' Initialize pagination
    currentPage = 1
    InitializeDummyData
    LoadData
    UpdatePaginationControls
End Sub

Private Sub InitializeDummyData()
    ' Initialize dummy data
    Set dummyData = New Collection
    Dim i As Integer
    For i = 1 To 100
        dummyData.Add Array("Item " & i, "SubItem " & i & " A", "SubItem " & i & " B", "SubItem " & i & " C", "SubItem " & i & " D", "SubItem " & i & " E")
    Next i
    totalItems = dummyData.Count
    totalPages = Int((totalItems - 1) / ITEMS_PER_PAGE) + 1
End Sub

Private Sub LoadData()
    Dim tvw As Object
    Dim nodeItem As Object
    Dim startIndex As Integer
    Dim endIndex As Integer
    Dim i As Integer
    Dim j As Integer

    ' Set the TreeView control
    Set tvw = Me.lvwData.Object

    ' Clear the TreeView
    tvw.Nodes.Clear

    ' Calculate the range of items to display on the current page
    startIndex = (currentPage - 1) * ITEMS_PER_PAGE + 1
    endIndex = startIndex + ITEMS_PER_PAGE - 1
    If endIndex > totalItems Then endIndex = totalItems

    ' Add items and subitems to the TreeView
    For i = startIndex To endIndex
        ' Add the main item
        Set nodeItem = tvw.Nodes.Add(, , , dummyData(i)(0)) ' Main item
        
        ' Add subitems under the main item
        For j = 1 To 5
            tvw.Nodes.Add nodeItem, tvwChild, , dummyData(i)(j)
        Next j
    Next i
End Sub

Private Sub UpdatePaginationControls()
    Me.lblPageInfo.Caption = "Page " & currentPage & " of " & totalPages & " | " & Format(Now, "hh:nn AM/PM")
    Me.btnFirstPage.Enabled = (currentPage > 1)
    Me.btnPreviousPage.Enabled = (currentPage > 1)
    Me.btnNextPage.Enabled = (currentPage < totalPages)
    Me.btnLastPage.Enabled = (currentPage < totalPages)
End Sub

Private Sub btnFirstPage_Click()
    currentPage = 1
    LoadData
    UpdatePaginationControls
End Sub

Private Sub btnPreviousPage_Click()
    If currentPage > 1 Then
        currentPage = currentPage - 1
        LoadData
        UpdatePaginationControls
    End If
End Sub

Private Sub btnNextPage_Click()
    If currentPage < totalPages Then
        currentPage = currentPage + 1
        LoadData
        UpdatePaginationControls
    End If
End Sub

Private Sub btnLastPage_Click()
    currentPage = totalPages
    LoadData
    UpdatePaginationControls
End Sub

Private Sub btnRefresh_Click()
    RefreshListView
End Sub
 
Private Sub RefreshListView()
    currentPage = 1
    LoadData
    Me.txtSubItemDetails.Value = ""
    UpdatePaginationControls
End Sub

Private Sub btnExpandAll_Click()
    Dim tvw As Object
    Set tvw = Me.lvwData.Object
    ExpandCollapseNodes tvw, True
End Sub

Private Sub btnCollapseAll_Click()
    Dim tvw As Object
    Set tvw = Me.lvwData.Object
    ExpandCollapseNodes tvw, False
End Sub

Private Sub lvwData_NodeClick(ByVal Node As Object)
    If Node.Children > 0 Then
        Me.txtSubItemDetails.Value = "Selected Item: " & Node.Text & " (HEADLINE)"
    Else
        Me.txtSubItemDetails.Value = "Selected SubItem: " & Node.Text
    End If
End Sub

Private Sub ExpandCollapseNodes(tvw As Object, expand As Boolean)
    Dim Node As Object
    For Each Node In tvw.Nodes
        If Node.Parent Is Nothing Then ' Only expand/collapse main items
            If expand Then
                Node.Expanded = True
            Else
                Node.Expanded = False
            End If
        End If
    Next Node
End Sub

Private Sub lvwData_Updated(Code As Integer)

End Sub


______________________________________________________

Private Sub LoadData()
    Dim tvw As Object
    Dim nodeItem As Object
    Dim startIndex As Integer
    Dim endIndex As Integer
    Dim i As Integer
    Dim j As Integer
    Dim subItemCount As Integer

    ' Set the TreeView control
    Set tvw = Me.lvwData.Object

    ' Clear the TreeView
    tvw.Nodes.Clear

    ' Calculate the range of items to display on the current page
    startIndex = (currentPage - 1) * ITEMS_PER_PAGE + 1
    endIndex = startIndex + ITEMS_PER_PAGE - 1
    If endIndex > totalItems Then endIndex = totalItems

    ' Add items and subitems to the TreeView
    For i = startIndex To endIndex
        ' Dynamically calculate subitem count for each item
        subItemCount = UBound(dummyData(i)) ' Number of subitems = upper bound of array
        
        ' Add the main item with the subitem count
        Set nodeItem = tvw.Nodes.Add(, , , dummyData(i)(0) & " (" & subItemCount & " SubItems)")

        ' Add subitems under the main item
        For j = 1 To subItemCount
            tvw.Nodes.Add nodeItem, tvwChild, , dummyData(i)(j)
        Next j
    Next i
End Sub





Private Sub btnExportToExcel_Click()
    Dim excelApp As Object
    Dim workbook As Object
    Dim worksheet As Object
    Dim tvw As Object
    Dim Node As Object
    Dim row As Integer

    ' Set the TreeView control
    Set tvw = Me.lvwData.Object

    ' Create a new Excel application
    On Error Resume Next
    Set excelApp = CreateObject("Excel.Application")
    If excelApp Is Nothing Then
        MsgBox "Excel is not installed on this system.", vbExclamation
        Exit Sub
    End If
    On Error GoTo 0

    ' Add a new workbook
    Set workbook = excelApp.Workbooks.Add
    Set worksheet = workbook.Sheets(1)

    ' Add headers to the Excel sheet
    worksheet.Cells(1, 1).Value = "Item"
    worksheet.Cells(1, 2).Value = "SubItem"
    row = 2 ' Start populating from the second row

    ' Loop through the TreeView nodes
    For Each Node In tvw.Nodes
        If Node.Parent Is Nothing Then
            ' Main item
            worksheet.Cells(row, 1).Value = Node.Text
            row = row + 1
        Else
            ' Subitem
            worksheet.Cells(row, 2).Value = Node.Text
            row = row + 1
        End If
    Next Node

    ' Autofit the columns for better readability
    worksheet.Columns("A:B").AutoFit

    ' Save the file
    Dim filePath As String
    filePath = Application.CurrentProject.Path & "\TreeViewExport.xlsx"
    workbook.SaveAs filePath

    ' Inform the user and clean up
    MsgBox "Data exported to Excel successfully! File saved at: " & filePath, vbInformation
    workbook.Close
    excelApp.Quit

    ' Release Excel objects
    Set worksheet = Nothing
    Set workbook = Nothing
    Set excelApp = Nothing
End Sub




Private Sub btnExportAllToExcel_Click()
    Dim excelApp As Object
    Dim workbook As Object
    Dim worksheet As Object
    Dim row As Integer
    Dim i As Integer
    Dim j As Integer

    ' Create a new Excel application
    On Error Resume Next
    Set excelApp = CreateObject("Excel.Application")
    If excelApp Is Nothing Then
        MsgBox "Excel is not installed on this system.", vbExclamation
        Exit Sub
    End If
    On Error GoTo 0

    ' Add a new workbook
    Set workbook = excelApp.Workbooks.Add
    Set worksheet = workbook.Sheets(1)

    ' Add headers to the Excel sheet
    worksheet.Cells(1, 1).Value = "Main Item"
    worksheet.Cells(1, 2).Value = "SubItem"
    row = 2 ' Start populating from the second row

    ' Loop through the entire dummy data collection
    For i = 1 To dummyData.Count
        ' Write the main item
        worksheet.Cells(row, 1).Value = dummyData(i)(0)

        ' Write the subitems
        For j = 1 To UBound(dummyData(i))
            worksheet.Cells(row, 2).Value = dummyData(i)(j)
            row = row + 1
        Next j
    Next i

    ' Autofit the columns for better readability
    worksheet.Columns("A:B").AutoFit

    ' Save the file
    Dim filePath As String
    filePath = Application.CurrentProject.Path & "\FullListViewExport.xlsx"
    workbook.SaveAs filePath

    ' Inform the user and clean up
    MsgBox "All data exported to Excel successfully! File saved at: " & filePath, vbInformation
    workbook.Close
    excelApp.Quit

    ' Release Excel objects
    Set worksheet = Nothing
    Set workbook = Nothing
    Set excelApp = Nothing
End Sub

