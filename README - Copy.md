Option Compare Database

Private childItems As Collection

Private Sub Form_Load()
    Set childItems = New Collection
    ConfigureListView
    LoadListViewData
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
End Sub

Private Sub AddChildItem(parentKey As String, Item As String, sessionDue As String, actionDate As String, assignedTo As String, activityStep As String)
    Dim childItem As listItem
    Set childItem = Me.ListView1.ListItems.Add(, , "   " & Item) ' Indent child items
    childItem.subItems(1) = sessionDue
    childItem.subItems(2) = actionDate
    childItem.subItems(3) = assignedTo
    childItem.subItems(4) = activityStep
    childItem.Tag = parentKey ' Associate child with parent
    childItems.Add childItem, parentKey & "_" & Item ' Store child item in collection
    childItem.ForeColor = RGB(150, 150, 150) ' Set child item text color to gray
End Sub

Private Sub ListView1_ItemClick(ByVal Item As Object)
    ' Declare child variable for loop
    Dim child As listItem
    
    If InStr(Item.Text, "[+]") > 0 Then
        ' Expand the parent by showing child items and changing [+] to [-]
        Item.Text = Replace(Item.Text, "[+]", "[-]") ' Change [+] to [-]
        Item.Tag = "Expanded" ' Mark as expanded
        
        ' Show child items
        For Each child In childItems
            If child.Tag = Item.Key Then
                ' Add the child item to the ListView
                Me.ListView1.ListItems.Add , , child.Text ' Add the child item to the ListView

                child.EnsureVisible ' Ensure the child item is visible
            End If
        Next child
    ElseIf InStr(Item.Text, "[-]") > 0 Then
        ' Collapse the parent by hiding child items and changing [-] to [+]
        Item.Text = Replace(Item.Text, "[-]", "[+]") ' Change [-] to [+]
        Item.Tag = "Collapsed" ' Mark as collapsed
        
        ' Remove child items from the ListView
        For Each child In childItems
            If child.Tag = Item.Key Then
                ' Remove the child item from the ListView by its Key
                Me.ListView1.ListItems.Remove child.Index
            End If
        Next child
    End If
End Sub
    


Private Sub ListView1_Updated(Code As Integer)

End Sub



Private Sub ListView1_ItemClick(ByVal Item As Object)
    Dim child As listItem
    Dim i As Integer

    If InStr(Item.Text, "[+]") > 0 Then
        ' Expand the parent by showing child items and changing [+] to [-]
        Item.Text = Replace(Item.Text, "[+]", "[-]") ' Change [+] to [-]
        Item.Tag = "Expanded" ' Mark as expanded
        
        ' Show child items
        For Each child In childItems
            If child.Tag = Item.Key Then
                ' Add child item if not already in ListView
                Dim exists As Boolean
                exists = False
                For i = 1 To Me.ListView1.ListItems.Count
                    If Me.ListView1.ListItems(i).Key = child.Key Then
                        exists = True
                        Exit For
                    End If
                Next i
                If Not exists Then
                    Dim newItem As listItem
                    Set newItem = Me.ListView1.ListItems.Add(, child.Key, child.Text)
                    newItem.subItems(1) = child.subItems(1)
                    newItem.subItems(2) = child.subItems(2)
                    newItem.subItems(3) = child.subItems(3)
                    newItem.subItems(4) = child.subItems(4)
                    newItem.ForeColor = child.ForeColor
                End If
            End If
        Next child

    ElseIf InStr(Item.Text, "[-]") > 0 Then
        ' Collapse the parent by hiding child items and changing [-] to [+]
        Item.Text = Replace(Item.Text, "[-]", "[+]") ' Change [-] to [+]
        Item.Tag = "Collapsed" ' Mark as collapsed
        
        ' Remove child items from the ListView
        For i = Me.ListView1.ListItems.Count To 1 Step -1
            If Me.ListView1.ListItems(i).Tag = Item.Key Then
                Me.ListView1.ListItems.Remove i
            End If
        Next i
    End If
End Sub


Private Sub ListView1_ItemClick(ByVal Item As Object)
    Dim child As listItem
    Dim i As Integer

    If InStr(Item.Text, "[+]") > 0 Then
        ' Expand the parent node
        ExpandParent Item
    ElseIf InStr(Item.Text, "[-]") > 0 Then
        ' Collapse the parent node
        CollapseParent Item
    End If
End Sub

Private Sub ExpandParent(ByVal parentItem As listItem)
    Dim child As listItem
    Dim newItem As listItem
    
    ' Change the parent node text to show expanded state
    parentItem.Text = Replace(parentItem.Text, "[+]", "[-]")
    parentItem.Tag = "Expanded" ' Mark the parent as expanded
    
    ' Add all associated child items from the childItems collection
    For Each child In childItems
        If child.Tag = parentItem.Key Then
            ' Add child item to the ListView
            Set newItem = Me.ListView1.ListItems.Add(, child.Key, child.Text)
            newItem.subItems(1) = child.subItems(1)
            newItem.subItems(2) = child.subItems(2)
            newItem.subItems(3) = child.subItems(3)
            newItem.subItems(4) = child.subItems(4)
            newItem.ForeColor = child.ForeColor
            newItem.Tag = parentItem.Key ' Link child to parent
        End If
    Next child
End Sub

Private Sub CollapseParent(ByVal parentItem As listItem)
    Dim i As Integer
    
    ' Change the parent node text to show collapsed state
    parentItem.Text = Replace(parentItem.Text, "[-]", "[+]")
    parentItem.Tag = "Collapsed" ' Mark the parent as collapsed
    
    ' Remove all child items associated with the parent from the ListView
    For i = Me.ListView1.ListItems.Count To 1 Step -1
        If Me.ListView1.ListItems(i).Tag = parentItem.Key Then
            Me.ListView1.ListItems.Remove i
        End If
    Next i
End Sub


____________________________________________________________________________________________________________________________________________________________________________________________________________________________
____________________________________________________________________________________________________________________________________________________________________________________________________________________________
____________________________________________________________________________________________________________________________________________________________________________________________________________________________
____________________________________________________________________________________________________________________________________________________________________________________________________________________________


Private Sub ListView1_ItemClick(ByVal Item As Object)
    If InStr(Item.Text, "[+]") > 0 Then
        ExpandParent Item
    ElseIf InStr(Item.Text, "[-]") > 0 Then
        CollapseParent Item
    End If
End Sub

Private Sub ExpandParent(ByVal parentItem As listItem)
    Dim keyPrefix As String
    Dim i As Integer

    ' Change the parent node text to show expanded state
    parentItem.Text = Replace(parentItem.Text, "[+]", "[-]")
    parentItem.Tag = "Expanded" ' Mark the parent as expanded

    ' Identify children by matching their keys
    keyPrefix = parentItem.Key & "_"
    For i = 1 To childItems.Count
        If Left(childItems(i)("Key"), Len(keyPrefix)) = keyPrefix Then
            Dim newItem As listItem
            Set newItem = Me.ListView1.ListItems.Add(, childItems(i)("Key"), childItems(i)("Text"))
            newItem.subItems(1) = childItems(i)("SessionDue")
            newItem.subItems(2) = childItems(i)("ActionDate")
            newItem.subItems(3) = childItems(i)("AssignedTo")
            newItem.subItems(4) = childItems(i)("ActivityStep")
            newItem.ForeColor = childItems(i)("ForeColor")
            newItem.Tag = parentItem.Key
        End If
    Next i
End Sub

Private Sub CollapseParent(ByVal parentItem As listItem)
    Dim i As Integer

    ' Change the parent node text to show collapsed state
    parentItem.Text = Replace(parentItem.Text, "[-]", "[+]")
    parentItem.Tag = "Collapsed" ' Mark the parent as collapsed

    ' Remove child items
    For i = Me.ListView1.ListItems.Count To 1 Step -1
        If Me.ListView1.ListItems(i).Tag = parentItem.Key Then
            Me.ListView1.ListItems.Remove i
        End If
    Next i
End Sub

Private Sub AddChildItem(parentKey As String, Item As String, sessionDue As String, actionDate As String, assignedTo As String, activityStep As String)
    Dim childData As Object
    Set childData = CreateObject("Scripting.Dictionary")
    
    ' Store child item data
    childData("Key") = parentKey & "_" & Item
    childData("Text") = "   " & Item ' Indent child items
    childData("SessionDue") = sessionDue
    childData("ActionDate") = actionDate
    childData("AssignedTo") = assignedTo
    childData("ActivityStep") = activityStep
    childData("ForeColor") = RGB(150, 150, 150)

    ' Add to childItems collection
    childItems.Add childData
End Sub


____________________________________________________________________________________________________________________________________________________________________________________________________________________________
____________________________________________________________________________________________________________________________________________________________________________________________________________________________
____________________________________________________________________________________________________________________________________________________________________________________________________________________________
____________________________________________________________________________________________________________________________________________________________________________________________________________________________

Private Sub ExpandParent(ByVal parentItem As listItem)
    Dim keyPrefix As String
    Dim childData As Object
    Dim newItem As listItem

    ' Change the parent node text to show expanded state
    parentItem.Text = Replace(parentItem.Text, "[+]", "[-]")
    parentItem.Tag = "Expanded" ' Mark the parent as expanded

    ' Filter and add only child items that belong to the selected parent
    keyPrefix = parentItem.Key & "_" ' Use the parent's key as a prefix
    For Each childData In childItems
        If Left(childData("Key"), Len(keyPrefix)) = keyPrefix Then
            ' Add the filtered child item to the ListView
            Set newItem = Me.ListView1.ListItems.Add(, childData("Key"), childData("Text"))
            newItem.subItems(1) = childData("SessionDue")
            newItem.subItems(2) = childData("ActionDate")
            newItem.subItems(3) = childData("AssignedTo")
            newItem.subItems(4) = childData("ActivityStep")
            newItem.ForeColor = childData("ForeColor")
            newItem.Tag = parentItem.Key ' Link child to parent
        End If
    Next childData
End Sub

Private Sub AddChildItem(parentKey As String, Item As String, sessionDue As String, actionDate As String, assignedTo As String, activityStep As String)
    Dim childData As Object
    Set childData = CreateObject("Scripting.Dictionary")
    
    ' Store child item data with a unique key
    childData("Key") = parentKey & "_" & Item
    childData("Text") = "   " & Item ' Indent child items
    childData("SessionDue") = sessionDue
    childData("ActionDate") = actionDate
    childData("AssignedTo") = assignedTo
    childData("ActivityStep") = activityStep
    childData("ForeColor") = RGB(150, 150, 150)

    ' Add to childItems collection
    childItems.Add childData
End Sub


______________________________________________________________________________________________________________________________________________________________________________________________________________________
______________________________________________________________________________________________________________________________________________________________________________________________________________________
______________________________________________________________________________________________________________________________________________________________________________________________________________________
______________________________________________________________________________________________________________________________________________________________________________________________________________________

Private Sub ExpandParent(ByVal parentItem As listItem)
    Dim keyPrefix As String
    Dim childData As Object
    Dim newItem As listItem

    ' Change the parent node text to show expanded state
    parentItem.Text = Replace(parentItem.Text, "[+]", "[-]")
    parentItem.Tag = "Expanded"

    ' Filter and add child items belonging to this parent
    keyPrefix = parentItem.Key & "_" ' Use the parent's key as a prefix
    For Each childData In childItems
        If Left(childData("Key"), Len(keyPrefix)) = keyPrefix Then
            Set newItem = Me.ListView1.ListItems.Add(, childData("Key"), childData("Text"))
            newItem.subItems(1) = childData("SessionDue")
            newItem.subItems(2) = childData("ActionDate")
            newItem.subItems(3) = childData("AssignedTo")
            newItem.subItems(4) = childData("ActivityStep")
            newItem.ForeColor = childData("ForeColor")
            newItem.Tag = parentItem.Key
        End If
    Next childData
End Sub

Private Sub CollapseParent(ByVal parentItem As listItem)
    Dim i As Integer

    ' Change the parent node text to show collapsed state
    parentItem.Text = Replace(parentItem.Text, "[-]", "[+]")
    parentItem.Tag = "Collapsed"

    ' Remove child items
    For i = Me.ListView1.ListItems.Count To 1 Step -1
        If Me.ListView1.ListItems(i).Tag = parentItem.Key Then
            Me.ListView1.ListItems.Remove i
        End If
    Next i
End Sub

Private Sub AddChildItem(parentKey As String, Item As String, sessionDue As String, actionDate As String, assignedTo As String, activityStep As String)
    Dim childData As Object
    Set childData = CreateObject("Scripting.Dictionary")
    
    ' Store child item data with a unique key linked to its parent
    childData("Key") = parentKey & "_" & Item
    childData("Text") = "   " & Item ' Indent child items
    childData("SessionDue") = sessionDue
    childData("ActionDate") = actionDate
    childData("AssignedTo") = assignedTo
    childData("ActivityStep") = activityStep
    childData("ForeColor") = RGB(150, 150, 150)

    ' Add to childItems collection
    childItems.Add childData
End Sub

______________


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

    ' Change text and tag to collapsed state
    parentItem.Text = Replace(parentItem.Text, "[-]", "[+]")
    parentItem.Tag = "Collapsed"

    ' Remove child items directly below the parent
    For i = Me.ListView1.ListItems.Count To 1 Step -1
        If Me.ListView1.ListItems(i).Tag = parentItem.Key Then
            Me.ListView1.ListItems.Remove i
        End If
    Next i
End Sub


Private Sub ListView1_ItemClick(ByVal Item As listItem)
    If InStr(Item.Text, "[+]") > 0 Then
        ' Expand parent
        ExpandParent Item
    ElseIf InStr(Item.Text, "[-]") > 0 Then
        ' Collapse parent
        CollapseParent Item
    End If
End Sub




