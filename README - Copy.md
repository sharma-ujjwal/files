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
    Dim i As Integer, email As clsEmail

    For i = 1 To 100
        Dim subItems As Collection
        Set subItems = New Collection
        
        ' Create email objects for each subitem
        Dim j As Integer
        For j = 1 To 5
            Set email = New clsEmail
            email.SenderId = "sender" & j & "@example.com"
            email.ReceiverId = "receiver" & j & "@example.com"
            email.Subject = "Subject " & j & " for Item " & i
            email.EmailDate = DateAdd("d", -j, Date) ' Example date logic
            email.Body = "This is the body of SubItem " & j & " for Item " & i
            subItems.Add email
        Next j

        ' Add main item with subitems (email objects)
        dummyData.Add Array("Item " & i, subItems)
    Next i

    totalItems = dummyData.count
    totalPages = Int((totalItems - 1) / ITEMS_PER_PAGE) + 1
End Sub

Private Sub LoadData()
    ' Load data into the ListView control
    Dim tvw As Object
    Dim nodeItem As Object
    Dim startIndex As Integer
    Dim endIndex As Integer
    Dim i As Integer
    Dim email As clsEmail
    Dim subItems As Collection

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
        Set nodeItem = tvw.Nodes.Add(, , , dummyData(i)(0)) ' Main item (e.g., "Item 1")
        
        ' Retrieve the collection of subitems (clsEmail objects)
        Set subItems = dummyData(i)(1)

        ' Add each subitem (email details) under the main item
        For Each email In subItems
            tvw.Nodes.Add nodeItem, tvwChild, , _
                "Sender: " & email.SenderId & vbCrLf & _
                "Receiver: " & email.ReceiverId & vbCrLf & _
                "Subject: " & email.Subject & vbCrLf & _
                "Date: " & Format(email.EmailDate, "dd-mmm-yyyy") & vbCrLf & _
                "Body: " & email.Body
        Next email
    Next i
    
    Me.btnCollapseAll.Enabled = False
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
    Me.btnCollapseAll.Enabled = False
    Me.btnExpandAll.Enabled = True
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
    Me.btnCollapseAll.Enabled = False
    Me.btnExpandAll.Enabled = True
End Sub

Private Sub btnRefresh_Click()
    Me.btnCollapseAll.Enabled = False
    Me.btnExpandAll.Enabled = True

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
    Dim Node As Object

    Set tvw = Me.lvwData.Object

    For Each Node In tvw.Nodes
        If Node.Parent Is Nothing Then
            Node.Expanded = True
        End If
    Next Node

    Me.btnCollapseAll.Enabled = True
    Me.btnExpandAll.Enabled = False


    If tvw.Nodes.count > 0 Then
        Set tvw.SelectedItem = tvw.Nodes(1)
        tvw.Nodes(1).EnsureVisible
    End If
End Sub

Private Sub btnCollapseAll_Click()
    LoadData
    Me.txtSubItemDetails.Value = ""
    UpdatePaginationControls
    Me.btnExpandAll.Enabled = True
    Me.btnCollapseAll.Enabled = False
End Sub


Private Sub lvwData_NodeClick(ByVal Node As Object)
    If Node.Children > 0 Then
        Me.txtSubItemDetails.Value = "Selected Item: " & Node.Text
    Else
        Me.txtSubItemDetails.Value = "Selected SubItem: " & Node.Text
    End If
End Sub

Private Sub ExpandCollapseNodes(tvw As Object, expand As Boolean)
    Dim Node As Object
    For Each Node In tvw.Nodes
        If Node.Parent Is Nothing Then ' Only expand/collapse main items
            If expand Then
                Me.btnCollapseAll.Enabled = True
                Node.Expanded = True
            Else
                Node.Expanded = False
            End If
        End If
    Next Node
End Sub

Private Sub btnExportData_Click()
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
    filePath = Application.CurrentProject.Path & "\Excel_File.xlsx"
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

Private Sub lvwData_Updated(Code As Integer)

End Sub
