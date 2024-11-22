Private Sub SortListView(columnIndex As Integer, sortOrder As Boolean)
    Dim itemArray() As Variant
    Dim i As Integer
    Dim lvw As Object
    Set lvw = Me.lvwData

    ' Extract ListView data into an array
    ReDim itemArray(1 To lvw.ListItems.Count, 0 To lvw.ColumnHeaders.Count)
    For i = 1 To lvw.ListItems.Count
        itemArray(i, 0) = lvw.ListItems(i).Text ' Main column
        Dim j As Integer
        For j = 1 To lvw.ColumnHeaders.Count - 1
            itemArray(i, j) = lvw.ListItems(i).ListSubItems(j).Text ' Subitems
        Next j
    Next i

    ' Sort the array (numeric or string sorting based on content)
    QuickSort itemArray, 1, UBound(itemArray), columnIndex, sortOrder

    ' Update the ListView with the sorted data
    lvw.ListItems.Clear
    Dim itm As ListItem
    For i = 1 To UBound(itemArray)
        Set itm = lvw.ListItems.Add(, , itemArray(i, 0)) ' Main item
        For j = 1 To lvw.ColumnHeaders.Count - 1
            itm.ListSubItems.Add , , itemArray(i, j) ' Subitems
        Next j
    Next i
End Sub

Private Sub QuickSort(arr() As Variant, low As Long, high As Long, columnIndex As Integer, sortOrder As Boolean)
    If low < high Then
        Dim pivotIndex As Long
        pivotIndex = Partition(arr, low, high, columnIndex, sortOrder)
        QuickSort arr, low, pivotIndex - 1, columnIndex, sortOrder
        QuickSort arr, pivotIndex + 1, high, columnIndex, sortOrder
    End If
End Sub

Private Function Partition(arr() As Variant, low As Long, high As Long, columnIndex As Integer, sortOrder As Boolean) As Long
    Dim pivot As Variant
    Dim i As Long, j As Long
    Dim temp As Variant

    pivot = ExtractNumber(arr(high, columnIndex)) ' Use last element as pivot
    i = low - 1

    For j = low To high - 1
        Dim compareValue As Boolean
        compareValue = (ExtractNumber(arr(j, columnIndex)) <= pivot) ' Ascending
        If Not sortOrder Then compareValue = Not compareValue ' Descending

        If compareValue Then
            i = i + 1
            ' Swap rows in the array
            SwapRows arr, i, j
        End If
    Next j

    ' Swap pivot to correct position
    SwapRows arr, i + 1, high
    Partition = i + 1
End Function

Private Sub SwapRows(arr() As Variant, row1 As Long, row2 As Long)
    Dim temp() As Variant
    temp = arr(row1)
    arr(row1) = arr(row2)
    arr(row2) = temp
End Sub

Private Function ExtractNumber(inputStr As String) As Double
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



Option Compare Database
Option Explicit
 
' Constants for pagination
Const ITEMS_PER_PAGE As Integer = 10
 
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
        .ColumnHeaders.Add , , "Item", 100
        .ColumnHeaders.Add , , "SubItem", 100
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
    
    Me.lblPageInfo.Caption = "Page " & currentPage & " of " & totalPages
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

Private Function CompareNumeric(x As String, y As String) As Integer
    If Val(x) < Val(y) Then
        CompareNumeric = -1
    ElseIf Val(x) > Val(y) Then
        CompareNumeric = 1
    Else
        CompareNumeric = 0
    End If
End Function

Private Sub SortListView(columnIndex As Integer, sortOrder As Boolean)
    Dim i As Integer, j As Integer
    Dim tempItem As String
    Dim tempSubItem As String
    Dim lvw As Object
    Set lvw = Me.lvwData ' Replace lvwData with the actual name of your ListView control

    ' Bubble sort algorithm for sorting ListView items
    For i = 1 To lvw.ListItems.Count - 1
        For j = i + 1 To lvw.ListItems.Count
            If columnIndex <= lvw.ListItems(i).ListSubItems.Count And columnIndex <= lvw.ListItems(j).ListSubItems.Count Then
                If (sortOrder And Val(lvw.ListItems(i).ListSubItems(columnIndex).Text) > Val(lvw.ListItems(j).ListSubItems(columnIndex).Text)) Or _
                   (Not sortOrder And Val(lvw.ListItems(i).ListSubItems(columnIndex).Text) < Val(lvw.ListItems(j).ListSubItems(columnIndex).Text)) Then
                    ' Swap items
                    tempItem = lvw.ListItems(i).Text
                    tempSubItem = lvw.ListItems(i).ListSubItems(columnIndex).Text
                    lvw.ListItems(i).Text = lvw.ListItems(j).Text
                    lvw.ListItems(i).ListSubItems(columnIndex).Text = lvw.ListItems(j).ListSubItems(columnIndex).Text
                    lvw.ListItems(j).Text = tempItem
                    lvw.ListItems(j).ListSubItems(columnIndex).Text = tempSubItem
                End If
            End If
        Next j
    Next i
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




Private Sub SortListView(columnIndex As Integer, sortOrder As Boolean)
    Dim i As Integer, j As Integer
    Dim tempItem As String
    Dim tempSubItem As String
    Dim lvw As Object
    Set lvw = Me.lvwData ' Replace lvwData with the actual name of your ListView control

    ' Bubble sort algorithm for sorting ListView items
    For i = 1 To lvw.ListItems.Count - 1
        For j = i + 1 To lvw.ListItems.Count
            Dim currentValue As String, nextValue As String
            
            ' Get the column text to compare (main item or subitem)
            If columnIndex = 0 Then
                currentValue = lvw.ListItems(i).Text
                nextValue = lvw.ListItems(j).Text
            Else
                currentValue = lvw.ListItems(i).ListSubItems(columnIndex).Text
                nextValue = lvw.ListItems(j).ListSubItems(columnIndex).Text
            End If
            
            ' Check the sorting order
            If (sortOrder And currentValue > nextValue) Or _
               (Not sortOrder And currentValue < nextValue) Then
                ' Swap items
                tempItem = lvw.ListItems(i).Text
                lvw.ListItems(i).Text = lvw.ListItems(j).Text
                lvw.ListItems(j).Text = tempItem
                
                ' Swap subitems for all columns
                Dim k As Integer
                For k = 1 To lvw.ColumnHeaders.Count - 1
                    tempSubItem = lvw.ListItems(i).ListSubItems(k).Text
                    lvw.ListItems(i).ListSubItems(k).Text = lvw.ListItems(j).ListSubItems(k).Text
                    lvw.ListItems(j).ListSubItems(k).Text = tempSubItem
                Next k
            End If
        Next j
    Next i
End Sub
