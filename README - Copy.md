Private Sub InitializeDummyData()
    ' Initialize dummy data
    Set dummyData = New Collection
    Dim i As Integer
    For i = 1 To 100
        dummyData.Add Array("Item " & i, "SubItem " & i & "A", "SubItem " & i & "B", "SubItem " & i & "C", "SubItem " & i & "D", "SubItem " & i & "E")
    Next i
    totalItems = dummyData.Count
    totalPages = Int((totalItems - 1) / ITEMS_PER_PAGE) + 1
End Sub


Private Sub LoadData()
    ' Load data into the ListView control
    Dim itm As ListItem
    Dim startIndex As Integer
    Dim endIndex As Integer
    Dim i As Integer
    Dim subItemsConcat As String

    ' Clear the ListView
    Me.lvwData.ListItems.Clear
    Me.lvwData.AllowColumnReorder = True

    ' Calculate the range of items to display on the current page
    startIndex = (currentPage - 1) * ITEMS_PER_PAGE + 1
    endIndex = startIndex + ITEMS_PER_PAGE - 1
    If endIndex > totalItems Then endIndex = totalItems

    ' Add items to the ListView
    For i = startIndex To endIndex
        ' Combine subitems into a single string
        subItemsConcat = dummyData(i)(1) & ", " & dummyData(i)(2) & ", " & dummyData(i)(3) & ", " & dummyData(i)(4) & ", " & dummyData(i)(5)
        
        ' Add the main item and the combined subitems
        Set itm = Me.lvwData.ListItems.Add(, , dummyData(i)(0))
        itm.ListSubItems.Add , , subItemsConcat
    Next i
End Sub


Option Compare Database
Option Explicit

' Constants for pagination
Const ITEMS_PER_PAGE As Integer = 30

' Variables to track pagination state
Dim currentPage As Integer
Dim totalPages As Integer
Dim totalItems As Integer
Dim dummyData As Collection
Dim lastColumn As Integer
Dim sortOrder As Boolean

Private Sub Detail_Click()

End Sub

Private Sub Form_Load()
    ' Initialize pagination
    currentPage = 1
    InitializeDummyData
    SetupListView
    LoadData
    UpdatePaginationControls
End Sub

Private Sub InitializeDummyData()
    ' Initialize dummy data
    Set dummyData = New Collection
    Dim i As Integer
    For i = 1 To 100
        dummyData.Add Array("Item " & i, "SubItem " & i)
    Next i
    totalItems = dummyData.Count
    totalPages = Int((totalItems - 1) / ITEMS_PER_PAGE) + 1
End Sub

Private Sub SetupListView()
    ' Set up the ListView control with columns
    With Me.lvwData
        .View = lvwReport
        .FullRowSelect = True
        .GridLines = True
        .ColumnHeaders.Clear
        .ColumnHeaders.Add , , "Item", 1000
        .ColumnHeaders.Add , , "SubItem", 1000
    End With
End Sub

Private Sub LoadData()
    ' Load data into the ListView control
    Dim itm As ListItem
    Dim startIndex As Integer
    Dim endIndex As Integer
    Dim itemCount As Integer
    ' Clear the ListView
    Me.lvwData.ListItems.Clear
    Me.lvwData.AllowColumnReorder = True
    
    ' Calculate the range of items to display on the current page
    startIndex = (currentPage - 1) * ITEMS_PER_PAGE + 1
    endIndex = startIndex + ITEMS_PER_PAGE - 1
    If endIndex > totalItems Then endIndex = totalItems
    
    ' Add items to the ListView
    itemCount = 0
    Dim i As Integer
    For i = startIndex To endIndex
        Set itm = Me.lvwData.ListItems.Add(, , dummyData(i)(0))
        itm.ListSubItems.Add , , dummyData(i)(1)
        
        itemCount = itemCount + 1
    Next i
End Sub

Private Sub UpdatePaginationControls()
    ' Update the pagination controls
    Me.lblPageInfo.Caption = "Page " & currentPage & " of " & totalPages & " | " & Format(Now, "hh:nn AM/PM")
    Me.btnFirstPage.Enabled = (currentPage > 1)
    Me.btnPreviousPage.Enabled = (currentPage > 1)
    Me.btnNextPage.Enabled = (currentPage < totalPages)
    Me.btnLastPage.Enabled = (currentPage < totalPages)
End Sub

Private Sub btnFirstPage_Click()
    ' Navigate to the first page
    currentPage = 1
    LoadData
    UpdatePaginationControls
End Sub

Private Sub btnPreviousPage_Click()
    ' Navigate to the previous page
    If currentPage > 1 Then
        currentPage = currentPage - 1
        LoadData
        UpdatePaginationControls
    End If
End Sub

Private Sub btnNextPage_Click()
    ' Navigate to the next page
    If currentPage < totalPages Then
        currentPage = currentPage + 1
        LoadData
        UpdatePaginationControls
    End If
End Sub

Private Sub btnLastPage_Click()
    ' Navigate to the last page
    currentPage = totalPages
    LoadData
    UpdatePaginationControls
End Sub

Private Sub btnRefresh_Click()
    RefreshListView
End Sub
 
Private Sub RefreshListView()
    ' Reload the data into the ListView starting from the current page
    currentPage = 1
    LoadData
    UpdatePaginationControls
End Sub

Private Function ExtractNumber(inputStr As String) As Double
    ' Extracts the numeric part from a string
    Dim matches As Object
    Dim regex As Object
    Set regex = CreateObject("VBScript.RegExp")
    regex.Pattern = "\d+" ' Match numeric values
    regex.Global = False
    If regex.Test(inputStr) Then
        Set matches = regex.Execute(inputStr)
        ExtractNumber = CDbl(matches(0)) ' Convert the first match to a number
    Else
        ExtractNumber = 0 ' Default to 0 if no numeric value is found
    End If
End Function
 
Private Sub SortListView(columnIndex As Integer, sortOrder As Boolean)
    Dim lvw As Object
    Set lvw = Me.lvwData ' Replace lvwData with the actual name of your ListView control
 
    ' Call QuickSort on the entire range of ListView items
    QuickSort lvw, columnIndex, sortOrder, 1, lvw.ListItems.Count
End Sub
 
Private Sub QuickSort(lvw As Object, columnIndex As Integer, sortOrder As Boolean, low As Integer, high As Integer)
    If low < high Then
        Dim pivotIndex As Integer
        pivotIndex = Partition(lvw, columnIndex, sortOrder, low, high)
        QuickSort lvw, columnIndex, sortOrder, low, pivotIndex - 1
        QuickSort lvw, columnIndex, sortOrder, pivotIndex + 1, high
    End If
End Sub
 
Private Function Partition(lvw As Object, columnIndex As Integer, sortOrder As Boolean, low As Integer, high As Integer) As Integer
    Dim pivotValue As String
    Dim pivotNumber As Double
    Dim i As Integer, j As Integer
 
    ' Choose the pivot as the high element
    If columnIndex = 0 Then
        pivotValue = lvw.ListItems(high).Text
    Else
        pivotValue = lvw.ListItems(high).ListSubItems(columnIndex).Text
    End If
    pivotNumber = ExtractNumber(pivotValue)
 
    i = low - 1
    For j = low To high - 1
        Dim currentValue As String
        Dim currentNumber As Double
 
        If columnIndex = 0 Then
            currentValue = lvw.ListItems(j).Text
        Else
            currentValue = lvw.ListItems(j).ListSubItems(columnIndex).Text
        End If
        currentNumber = ExtractNumber(currentValue)
 
        Dim comparisonResult As Boolean
        comparisonResult = (currentNumber < pivotNumber)
 
        If (sortOrder And comparisonResult) Or _
           (Not sortOrder And Not comparisonResult) Then
            i = i + 1
            SwapListViewItems lvw, i, j
        End If
    Next j
    SwapListViewItems lvw, i + 1, high
    Partition = i + 1
End Function
 
Private Sub SwapListViewItems(lvw As Object, index1 As Integer, index2 As Integer)
    Dim tempItem As String
    Dim tempSubItems() As String
    Dim k As Integer
 
    ' Swap main items
    tempItem = lvw.ListItems(index1).Text
    lvw.ListItems(index1).Text = lvw.ListItems(index2).Text
    lvw.ListItems(index2).Text = tempItem
 
    ' Swap subitems for all columns
    ReDim tempSubItems(1 To lvw.ColumnHeaders.Count - 1)
    For k = 1 To lvw.ColumnHeaders.Count - 1
        tempSubItems(k) = lvw.ListItems(index1).ListSubItems(k).Text
        lvw.ListItems(index1).ListSubItems(k).Text = lvw.ListItems(index2).ListSubItems(k).Text
        lvw.ListItems(index2).ListSubItems(k).Text = tempSubItems(k)
    Next k
End Sub
 
Private Sub lvwData_ColumnClick(ByVal ColumnHeader As Object)
    ' Determine the sort order
    If lastColumn = ColumnHeader.Index - 1 Then
        sortOrder = Not sortOrder
    Else
        sortOrder = True
    End If
    ' Sort the ListView by the clicked column
    SortListView ColumnHeader.Index - 1, sortOrder
    ' Remember the last sorted column
    lastColumn = ColumnHeader.Index - 1
End Sub

Private Sub btnExport_Click()
    ' Export all data to Excel
    Dim xlApp As Object
    Dim xlWorkbook As Object
    Dim xlWorksheet As Object
    Dim rowIndex As Integer
    Dim colIndex As Integer
    Dim i As Integer
    Dim savePath As String
    Dim newSavePath As String
    Dim timestamp As String

    ' Specify the file path where the Excel file will be saved
    savePath = "C:\Users\fy84\Downloads\exported_data.xlsx"

    ' Check if the file already exists
    If Dir(savePath) <> "" Then
        ' Append timestamp to the filename
        timestamp = Format(Now, "yyyyMMdd_HHmmss")
        newSavePath = savePath & "_" & timestamp & ".xlsx"
    Else
        newSavePath = savePath
    End If

    ' Create Excel application instance
    Set xlApp = CreateObject("Excel.Application")
    xlApp.Visible = False ' Keep Excel invisible during processing
    Set xlWorkbook = xlApp.Workbooks.Add
    Set xlWorksheet = xlWorkbook.Sheets(1)
    
    ' Write column headers
    For colIndex = 1 To Me.lvwData.ColumnHeaders.Count
        xlWorksheet.Cells(1, colIndex).Value = Me.lvwData.ColumnHeaders(colIndex).Text
    Next colIndex
    
    ' Write data
    rowIndex = 2
    For i = 1 To dummyData.Count
        xlWorksheet.Cells(rowIndex, 1).Value = dummyData(i)(0) ' Item
        xlWorksheet.Cells(rowIndex, 2).Value = dummyData(i)(1) ' SubItem
        rowIndex = rowIndex + 1
    Next i
    
    ' Autofit columns
    xlWorksheet.Columns("A:B").EntireColumn.AutoFit
    
    ' Save the workbook
    xlWorkbook.SaveAs newSavePath
    xlWorkbook.Close
    xlApp.Quit
    
    ' Notify user
    MsgBox "Data exported successfully to: " & newSavePath, vbInformation

    ' Cleanup
    Set xlWorksheet = Nothing
    Set xlWorkbook = Nothing
    Set xlApp = Nothing
End Sub

