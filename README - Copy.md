Private Sub SortListView(columnIndex As Integer, sortOrder As Boolean)
    Dim i As Integer, j As Integer
    Dim tempItem As String
    Dim tempSubItems() As String
    Dim lvw As Object
    Set lvw = Me.lvwData ' Replace lvwData with the actual name of your ListView control

    ' Bubble sort algorithm for sorting ListView items numerically
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
            
            ' Compare numerically if possible
            Dim comparisonResult As Boolean
            If IsNumeric(currentValue) And IsNumeric(nextValue) Then
                comparisonResult = (Val(currentValue) > Val(nextValue))
            Else
                comparisonResult = (currentValue > nextValue) ' Default to string comparison
            End If
            
            ' Check the sorting order
            If (sortOrder And comparisonResult) Or _
               (Not sortOrder And Not comparisonResult) Then
                ' Swap items
                tempItem = lvw.ListItems(i).Text
                lvw.ListItems(i).Text = lvw.ListItems(j).Text
                lvw.ListItems(j).Text = tempItem
                
                ' Swap subitems for all columns
                ReDim tempSubItems(1 To lvw.ColumnHeaders.Count - 1)
                Dim k As Integer
                For k = 1 To lvw.ColumnHeaders.Count - 1
                    tempSubItems(k) = lvw.ListItems(i).ListSubItems(k).Text
                    lvw.ListItems(i).ListSubItems(k).Text = lvw.ListItems(j).ListSubItems(k).Text
                    lvw.ListItems(j).ListSubItems(k).Text = tempSubItems(k)
                Next k
            End If
        Next j
    Next i
End Sub




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
