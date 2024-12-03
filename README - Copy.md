Option Compare Database

Private childItems As Collection

Private Sub btnCollapseAll_Click()

End Sub

Private Sub btnExpandAll_Click()

End Sub

Private Sub Form_Load()
    Set childItems = New Collection
    ConfigureListView
    LoadListViewData
End Sub

Private Sub AutoFitColumns()
    Dim i As Integer
    Dim columnCount As Integer
    Dim totalWidth As Integer
    
    totalWidth = Me.ListView1.Width
    columnCount = Me.ListView1.ColumnHeaders.count
    
    For i = 1 To columnCount
        Me.ListView1.ColumnHeaders(i).Width = totalWidth / columnCount - 20

    Next i
End Sub

Private Sub ConfigureListView()
    ' Clear existing columns
    Me.ListView1.ColumnHeaders.Clear

    ' Configure columns
    Dim colHeader As ColumnHeader
    Set colHeader = Me.ListView1.ColumnHeaders.Add(, , "Item", 100) ' Column 1
    Set colHeader = Me.ListView1.ColumnHeaders.Add(, , "Session Due", 100) ' Column 2
    Set colHeader = Me.ListView1.ColumnHeaders.Add(, , "Action Date", 100) ' Column 3
    Set colHeader = Me.ListView1.ColumnHeaders.Add(, , "Assigned To", 150) ' Column 4
    Set colHeader = Me.ListView1.ColumnHeaders.Add(, , "Activity Step", 200) ' Column 5

    ' Enable gridlines and full row select
    Me.ListView1.GridLines = True
    Me.ListView1.FullRowSelect = True
    Me.ListView1.View = lvwReport
    AutoFitColumns
End Sub

Private Sub LoadListViewData()
    ' Clear existing items
    Me.ListView1.ListItems.Clear
    Set childItems = New Collection

    ' Add parent node (Past Due)
    Dim parentItem As listItem
    Set parentItem = Me.ListView1.ListItems.Add(, "PastDue", "[+] Past Due")
    parentItem.Tag = "Collapsed" ' Mark as collapsed initially

    ' Add child rows under Past Due
    AddChildItem "PastDue", "1", "09/15/2023", "10/11/2024", "Martel, Tina D", "Unspecified Letter 1"
    AddChildItem "PastDue", "2", "09/15/2023", "10/11/2024", "Martel, Tina D", "Unspecified Letter 2"

    ' Add another parent node (Three Months)
    Set parentItem = Me.ListView1.ListItems.Add(, "ThreeMonths", "[+] Three Months")
    parentItem.Tag = "Collapsed" ' Mark as collapsed initially

    ' Add child rows under Three Months
    AddChildItem "ThreeMonths", "3", "12/13/2024", "12/12/2024", "Guillermo, Donald", "Peer Review Request"
    
    AutoFitColumns
End Sub

Private Sub ListView1_ItemClick(ByVal Item As Object)
    If InStr(Item.Text, "[+]") > 0 Then
        ExpandParent Item
    ElseIf InStr(Item.Text, "[-]") > 0 Then
        CollapseParent Item
    End If
End Sub

Private Sub ExpandParent(ByVal parentItem As listItem)
    Dim childData As Object
    Dim newItem As listItem
    Dim insertIndex As Integer

    ' Change text and tag to expanded state
    parentItem.Text = Replace(parentItem.Text, "[+]", "[-]")
    parentItem.Tag = "Expanded"

    ' Determine the index to insert child items
    insertIndex = parentItem.Index + 1

    ' Loop through childItems and add matching children
    For Each childData In childItems
        If childData("ParentKey") = parentItem.Key Then
            ' Add child item at the correct position
            Set newItem = Me.ListView1.ListItems.Add(insertIndex, childData("Key"), childData("Text"))
            newItem.subItems(1) = childData("SessionDue")
            newItem.subItems(2) = childData("ActionDate")
            newItem.subItems(3) = childData("AssignedTo")
            newItem.subItems(4) = childData("ActivityStep")
            newItem.ForeColor = childData("ForeColor")
            newItem.Tag = parentItem.Key

            ' Increment the insert index for the next child
            insertIndex = insertIndex + 1
        End If
    Next childData
End Sub

Private Sub CollapseParent(ByVal parentItem As listItem)
    Dim i As Integer

    ' Change the parent node text to show collapsed state
    parentItem.Text = Replace(parentItem.Text, "[-]", "[+]")
    parentItem.Tag = "Collapsed"

    ' Remove child items
    For i = Me.ListView1.ListItems.count To 1 Step -1
        If Me.ListView1.ListItems(i).Tag = parentItem.Key Then
            Me.ListView1.ListItems.Remove i
        End If
    Next i
End Sub

Private Sub AddChildItem(parentKey As String, Item As String, sessionDue As String, actionDate As String, assignedTo As String, activityStep As String)
    Dim childData As Object
    Set childData = CreateObject("Scripting.Dictionary")
    
    ' Store all child data
    childData("ParentKey") = parentKey
    childData("Key") = parentKey & "_" & Item
    childData("Text") = "   " & Item ' Indent for child items
    childData("SessionDue") = sessionDue
    childData("ActionDate") = actionDate
    childData("AssignedTo") = assignedTo
    childData("ActivityStep") = activityStep
    childData("ForeColor") = RGB(150, 150, 150)

    ' Add to collection
    childItems.Add childData, childData("Key")
End Sub

Private Sub ListView1_Updated(Code As Integer)

End Sub

Private Sub btnExpandAll_Click()
    Dim parentItems As Collection
    Dim i As Integer
    Dim parentItem As listItem

    ' Collect all parent items in a collection
    Set parentItems = New Collection
    For i = 1 To Me.ListView1.ListItems.Count
        Set parentItem = Me.ListView1.ListItems(i)
        If InStr(parentItem.Text, "[+]") > 0 Then
            parentItems.Add parentItem
        End If
    Next i

    ' Expand all parent items in the collection
    For Each parentItem In parentItems
        ExpandParent parentItem
    Next parentItem
End Sub

Private Sub btnCollapseAll_Click()
    Dim i As Integer
    Dim parentItem As listItem

    ' Loop through all items in the ListView in reverse order
    For i = Me.ListView1.ListItems.Count To 1 Step -1
        Set parentItem = Me.ListView1.ListItems(i)
        
        ' Collapse only parent items that are expanded
        If InStr(parentItem.Text, "[-]") > 0 Then
            CollapseParent parentItem
        End If
    Next i
End Sub



Private Sub LoadListViewData()
    ' Clear existing items
    Me.ListView1.ListItems.Clear
    Set childItems = New Collection

    ' Add parent node (Past Due)
    Dim parentItem As listItem
    Set parentItem = Me.ListView1.ListItems.Add(, "PastDue", "[+] Past Due")
    parentItem.Tag = "Collapsed" ' Mark as collapsed initially

    ' Add child rows under Past Due
    AddChildItem "PastDue", "1", "09/15/2023", "10/11/2024", "Martel, Tina D", "Unspecified Letter 1"
    AddChildItem "PastDue", "2", "09/15/2023", "10/11/2024", "Martel, Tina D", "Unspecified Letter 2"
    AddChildItem "PastDue", "3", "09/18/2023", "10/12/2024", "Smith, John", "Follow-up Reminder"

    ' Add another parent node (Three Months)
    Set parentItem = Me.ListView1.ListItems.Add(, "ThreeMonths", "[+] Three Months")
    parentItem.Tag = "Collapsed" ' Mark as collapsed initially

    ' Add child rows under Three Months
    AddChildItem "ThreeMonths", "4", "12/01/2024", "12/02/2024", "Doe, Jane", "Review Submission"
    AddChildItem "ThreeMonths", "5", "12/03/2024", "12/04/2024", "Guillermo, Donald", "Approval Needed"
    AddChildItem "ThreeMonths", "6", "12/05/2024", "12/06/2024", "Brown, Emily", "Peer Review Request"

    ' Add another parent node (Six Months)
    Set parentItem = Me.ListView1.ListItems.Add(, "SixMonths", "[+] Six Months")
    parentItem.Tag = "Collapsed" ' Mark as collapsed initially

    ' Add child rows under Six Months
    AddChildItem "SixMonths", "7", "01/10/2024", "01/15/2024", "Martel, Tina D", "Progress Update"
    AddChildItem "SixMonths", "8", "01/12/2024", "01/18/2024", "Doe, Jane", "Second Reminder"
    AddChildItem "SixMonths", "9", "01/15/2024", "01/20/2024", "Smith, John", "Final Follow-up"

    ' Add another parent node (Yearly)
    Set parentItem = Me.ListView1.ListItems.Add(, "Yearly", "[+] Yearly")
    parentItem.Tag = "Collapsed" ' Mark as collapsed initially

    ' Add child rows under Yearly
    AddChildItem "Yearly", "10", "03/15/2024", "03/20/2024", "Brown, Emily", "Annual Report"
    AddChildItem "Yearly", "11", "03/18/2024", "03/22/2024", "Guillermo, Donald", "Budget Review"
    AddChildItem "Yearly", "12", "03/20/2024", "03/25/2024", "Martel, Tina D", "Audit Preparation"

    ' Adjust column widths
    AutoFitColumns
End Sub
