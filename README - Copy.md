Private Sub Form_Load()

    ' Create the root item for the ListView (this will act as the parent)
    AddTreeItem "Root Node", "Root Description", "2024-11-29", 0, True  ' True means initially expanded

    ' Add a child node under the root node (collapsed by default)
    AddTreeItem "Child Node 1", "Child Description", "2024-11-29", 1, False  ' False means collapsed initially

    ' Add another child node under the root node (collapsed by default)
    AddTreeItem "Child Node 2", "Child Description", "2024-11-29", 2, False

End Sub

' Function to Add Tree Items to the ListView
Private Sub AddTreeItem(ItemText As String, ItemDesc As String, ItemDate As String, ItemID As Integer, IsExpanded As Boolean)
    Dim newItem As ListItem
    Set newItem = Me.ListView0.ListItems.Add(, , ItemText)
    
    ' Add subitems for ID, Description, Date
    newItem.SubItems(1) = ItemID
    newItem.SubItems(2) = ItemDesc
    newItem.SubItems(3) = ItemDate
    
    ' Set initial icon or text for expand/collapse (using a simple text approach here)
    If IsExpanded Then
        newItem.Text = ItemText & " [-]" ' Initially expanded with minus icon
    Else
        newItem.Text = ItemText & " [+]" ' Initially collapsed with plus icon
    End If
    
    ' Optionally, add more logic to manage child nodes
    If IsExpanded Then
        ' You can add child nodes under this item if it's expanded
        ' For example, adding child nodes under root:
        AddTreeItem "Child of " & ItemText, "Nested Child Description", "2024-11-29", ItemID + 1, False
    End If
End Sub

Private Sub ListView0_ItemClick(ByVal Item As Object)

    ' Handle the ItemClick event to toggle expand/collapse state
    If InStr(Item.Text, "[+]") > 0 Then
        ' If it's collapsed, expand it
        Item.Text = Replace(Item.Text, "[+]", "[-]") ' Change [+] to [-]
        ' Add child nodes (expand the item)
        AddTreeItem "Child of " & Item.Text, "Nested Child Description", "2024-11-29", 1, False
    ElseIf InStr(Item.Text, "[-]") > 0 Then
        ' If it's expanded, collapse it
        Item.Text = Replace(Item.Text, "[-]", "[+]") ' Change [-] to [+]
        ' Remove child nodes (collapse the item)
        ' Logic to hide or remove child nodes can be added here
    End If
End Sub






Private Sub Form_Load()

    ' Create reference to ImageList control (ensure it's named ImageList0)
    Dim imgList As Object
    Set imgList = Me.Controls("ImageList0") ' Accessing ImageList0 control using Me.Controls

    ' Clear any existing images in the ImageList
    imgList.ListImages.Clear

    ' Add images to the ImageList (ensure the paths are correct and images are the correct size)
    imgList.ListImages.Add , , LoadPicture("C:\Path\To\PlusIcon.ico")  ' "+" icon for collapsed state
    imgList.ListImages.Add , , LoadPicture("C:\Path\To\MinusIcon.ico") ' "-" icon for expanded state
    
    ' Assign the ImageList to the ListView control (make sure ListView0 is the correct name)
    Me.ListView0.SmallIconList = imgList
    Me.ListView0.LargeIconList = imgList
    
    ' Example of adding root item to the ListView
    AddTreeItem "Root Node", 1 ' Root node with "+" icon (collapsed)
    
End Sub

Private Sub AddTreeItem(ItemText As String, IconIndex As Integer)
    Dim newItem As ListItem
    ' Add a new item to the ListView with the given text and icon
    Set newItem = Me.ListView0.ListItems.Add(, , ItemText)
    
    ' Set the icon for the item (this should reference the ImageList)
    newItem.Icon = IconIndex ' Set the icon to "+" (1) or "-" (2)
    
    ' Add subitems to the ListView (ID, Name, Description, Date)
    newItem.SubItems(1) = "Sample ID"
    newItem.SubItems(2) = "Sample Name"
    newItem.SubItems(3) = "Sample Description"
    newItem.SubItems(4) = "Sample Date"
    
    ' Optionally, add subitems for nested/child nodes
    ' For example, adding a child item under the root node
    ' AddTreeItem "Child Node", 1 ' Uncomment to add a child node under the root node
End Sub

Private Sub ListView0_ItemClick(ByVal Item As Object)
    ' Handle the ItemClick event to toggle expand/collapse state
    Dim imgList As Object
    Set imgList = Me.Controls("ImageList0") ' Accessing ImageList0 control
    
    ' Toggle the icon for expand/collapse when the root item is clicked
    If Item.Icon = 1 Then ' If "+" icon (collapsed)
        Item.Icon = 2 ' Change to "-" icon (expanded)
        ' Add child nodes (expand the item)
        AddTreeItem "Child Node 1", 2 ' Example child node under expanded item
    ElseIf Item.Icon = 2 Then ' If "-" icon (expanded)
        Item.Icon = 1 ' Change to "+" icon (collapsed)
        ' Remove child nodes (collapse the item)
        ' Logic to remove child nodes or hide them can be added here
    End If
End Sub




Private Sub Form_Load()

    ' Create reference to ImageList control (ensure it's named ImageList0)
    Dim imgList As Object
    Set imgList = Me.Controls("ImageList0") ' Accessing ImageList0 control using Me.Controls

    ' Clear any existing images
    imgList.ListImages.Clear

    ' Add images to the ImageList (ensure the paths are correct)
    imgList.ListImages.Add , , LoadPicture("C:\Path\To\PlusIcon.ico")  ' "+" icon for collapsed state
    imgList.ListImages.Add , , LoadPicture("C:\Path\To\MinusIcon.ico") ' "-" icon for expanded state
    
    ' Optionally, set the icon size for ListView
    imgList.SmallIconSize = 16 ' Size in pixels
    imgList.LargeIconSize = 32 ' Size in pixels
    
    ' Assign the ImageList to the ListView control (make sure ListView0 is the correct name)
    Me.ListView0.SmallIconList = imgList
    Me.ListView0.LargeIconList = imgList
    
    ' Example of adding root item to the ListView
    AddTreeItem "Root Node", 1 ' Root node with "+" icon (collapsed)
    
End Sub

Private Sub AddTreeItem(ItemText As String, IconIndex As Integer)
    Dim newItem As ListItem
    ' Add a new item to the ListView with the given text and icon
    Set newItem = Me.ListView0.ListItems.Add(, , ItemText)
    newItem.Icon = IconIndex ' Set the icon to "+" (1) or "-" (2)
    
    ' Add subitems to the ListView (ID, Name, Description, Date)
    newItem.SubItems(1) = "Sample ID"
    newItem.SubItems(2) = "Sample Name"
    newItem.SubItems(3) = "Sample Description"
    newItem.SubItems(4) = "Sample Date"
    
    ' Optionally, add subitems for nested/child nodes
    ' For example, adding a child item under the root node
    ' AddTreeItem "Child Node", 1 ' Uncomment to add a child node under the root node
End Sub

Private Sub ListView0_ItemClick(ByVal Item As Object)
    ' Handle the ItemClick event to toggle expand/collapse state
    Dim imgList As Object
    Set imgList = Me.Controls("ImageList0") ' Accessing ImageList0 control
    
    ' Toggle the icon for expand/collapse when the root item is clicked
    If Item.Icon = 1 Then ' If "+" icon (collapsed)
        Item.Icon = 2 ' Change to "-" icon (expanded)
        ' Add child nodes (expand the item)
        AddTreeItem "Child Node 1", 2 ' Example child node under expanded item
    ElseIf Item.Icon = 2 Then ' If "-" icon (expanded)
        Item.Icon = 1 ' Change to "+" icon (collapsed)
        ' Remove child nodes (collapse the item)
        ' Logic to remove child nodes or hide them can be added here
    End If
End Sub





Private Sub Form_Load()
    ' Set up ListView columns
    With Me.ListView0
        ' Clear existing items
        .Clear
        
        ' Add columns (Table headers)
        .ColumnHeaders.Add , , "ID", 50
        .ColumnHeaders.Add , , "Name", 150
        .ColumnHeaders.Add , , "Description", 200
        .ColumnHeaders.Add , , "Date", 100
    End With
End Sub


Private Sub Form_Load()
    ' Set up ListView columns
    With Me.ListView0
        ' Clear existing items
        .Clear
        
        ' Add columns (Table headers)
        .ColumnHeaders.Add , , "ID", 50
        .ColumnHeaders.Add , , "Name", 150
        .ColumnHeaders.Add , , "Description", 200
        .ColumnHeaders.Add , , "Date", 100
        
        ' Add parent item (Root Node)
        Dim parentItem As ListItem
        Set parentItem = .Add(, , "1")
        parentItem.SubItems(1) = "Root Item"
        parentItem.SubItems(2) = "This is the root node."
        parentItem.SubItems(3) = "2024-11-29"
        parentItem.Icon = 1 ' Use a "+" icon for collapsed state

        ' Add child items under the parent (initially hidden)
        Dim childItem As ListItem
        Set childItem = .Add(, , "1.1")
        childItem.SubItems(1) = "Child Item 1"
        childItem.SubItems(2) = "This is a child of Root Item."
        childItem.SubItems(3) = "2024-11-30"
        childItem.ListSubItems(0).Visible = False ' Initially hide child

        Set childItem = .Add(, , "1.2")
        childItem.SubItems(1) = "Child Item 2"
        childItem.SubItems(2) = "Another child of Root Item."
        childItem.SubItems(3) = "2024-12-01"
        childItem.ListSubItems(0).Visible = False ' Initially hide child
    End With
End Sub

Private Sub Form_Load()

    ' Create reference to ImageList control
    Dim imgList As Object
    Set imgList = Me.ImageList0 ' Replace ImageList0 with your actual ImageList control name
    
    ' Clear any existing images
    imgList.ListImages.Clear
    
    ' Add images to the ImageList (ensure the paths are correct)
    ' Adding "+" and "-" icons for expand/collapse functionality
    imgList.ListImages.Add , , "C:\Path\To\PlusIcon.ico"  ' "+" icon for collapsed state
    imgList.ListImages.Add , , "C:\Path\To\MinusIcon.ico" ' "-" icon for expanded state
    
    ' Optionally, set the icon size for ListView
    imgList.SmallIconSize = 16 ' Size in pixels
    imgList.LargeIconSize = 32 ' Size in pixels
    
    ' Assign the ImageList to the ListView control
    Me.ListView0.SmallIconList = imgList
    Me.ListView0.LargeIconList = imgList
    
    ' Example of adding root item to the ListView
    AddTreeItem "Root Node", 1 ' Root node with "+" icon (collapsed)
    
End Sub

Private Sub AddTreeItem(ItemText As String, IconIndex As Integer)
    Dim newItem As ListItem
    ' Add a new item to the ListView with the given text and icon
    Set newItem = Me.ListView0.ListItems.Add(, , ItemText)
    newItem.Icon = IconIndex ' Set the icon to "+" (1) or "-" (2)
    
    ' Add subitems to the ListView (ID, Name, Description, Date)
    newItem.SubItems(1) = "Sample ID"
    newItem.SubItems(2) = "Sample Name"
    newItem.SubItems(3) = "Sample Description"
    newItem.SubItems(4) = "Sample Date"
    
    ' Optionally, add subitems for nested/child nodes
    ' For example, adding a child item under the root node
    ' You can add the child item conditionally if needed (e.g., based on state)
    ' AddTreeItem "Child Node", 1 ' Uncomment to add a child node under the root node
End Sub

Private Sub ListView0_ItemClick(ByVal Item As Object)
    ' Handle the ItemClick event to toggle expand/collapse state
    Dim imgList As Object
    Set imgList = Me.ImageList0 ' Reference to ImageList control
    
    ' Toggle the icon for expand/collapse when the root item is clicked
    If Item.Icon = 1 Then ' If "+" icon (collapsed)
        Item.Icon = 2 ' Change to "-" icon (expanded)
        ' Add child nodes (expand the item)
        AddTreeItem "Child Node 1", 2 ' Example child node under expanded item
    ElseIf Item.Icon = 2 Then ' If "-" icon (expanded)
        Item.Icon = 1 ' Change to "+" icon (collapsed)
        ' Remove child nodes (collapse the item)
        ' Logic to remove child nodes or hide them can be added here
    End If
End Sub



To simulate grid and table functionality with hierarchical data in a TreeView control while maintaining the tree structure, you can combine the tree’s hierarchical nature with the ability to display multiple columns (simulated via subitems). Although TreeView doesn’t natively support grid-like features like ListView, you can still replicate the behavior by organizing the data effectively within the tree and simulating the columns using node labels and subitems.
Key Concepts:

    Hierarchical Structure: The core feature of the TreeView is its ability to create a parent-child relationship between nodes.
    Simulating Grids (Multiple Columns): You can simulate a grid by using the Text property for the first column (node text) and the ListItems collection for additional columns (subitems).
    Interactivity: You can handle interactions with the tree nodes (like expanding/collapsing) and associate additional functionality (like sorting or filtering) using VBA.

Steps to Simulate TreeView with Grid-Like Functionality

    Use Node Text for the First Column: This will represent the primary value for the node (the label of each row).
    Use SubItems for Other Columns: You can simulate additional columns by using the SubItems property to add more data for each node.
    Maintain Hierarchy: The tree’s inherent hierarchy allows you to display data in parent-child relationships.

Example: Simulating a TreeView with Grid-like Structure

Let’s create a TreeView control that displays hierarchical data, with each node showing multiple columns, like a table.
1. Create the TreeView with Hierarchical Data

Private Sub Form_Load()
    ' Initialize TreeView control
    Dim rootNode As Object
    Dim childNode As Object
    Dim subChildNode As Object

    ' Clear existing nodes
    Me.TreeView0.Nodes.Clear
    
    ' Add the root node (first row) with simulated columns
    Set rootNode = Me.TreeView0.Nodes.Add(, , "RootNode", "Root Node" & vbTab & "Root Node Description" & vbTab & "Root Node Date")
    rootNode.EnsureVisible ' Ensure the root node is visible
    
    ' Add child nodes (second row)
    Set childNode = Me.TreeView0.Nodes.Add("RootNode", tvwChild, "ChildNode1", "Child Node 1" & vbTab & "Child Node 1 Description" & vbTab & "2024-11-29")
    childNode.EnsureVisible ' Ensure it is visible
    
    Set childNode = Me.TreeView0.Nodes.Add("RootNode", tvwChild, "ChildNode2", "Child Node 2" & vbTab & "Child Node 2 Description" & vbTab & "2024-11-30")
    childNode.EnsureVisible ' Ensure it is visible
    
    ' Add sub-child node (third row)
    Set subChildNode = Me.TreeView0.Nodes.Add("ChildNode1", tvwChild, "SubChildNode1", "Sub Child 1" & vbTab & "Sub Child 1 Description" & vbTab & "2024-12-01")
    subChildNode.EnsureVisible ' Ensure it is visible
    
    ' Optionally, you can add more levels or data for further nodes
End Sub


Explanation of the Code:

    Root Node: Represents the first "row" in the tree. The Text property contains the label for the node (first column), and the ListItems.Add method simulates additional columns (second and third columns).

    Child Nodes: The child nodes (ChildNode1, ChildNode2) are added as children of the root node. These nodes also have multiple columns added via the ListItems.Add method, simulating a table-like structure with several columns.

    Sub-child Node: A sub-node (SubChildNode1) is added under a child node (ChildNode1). This keeps the hierarchical structure intact.

Example Output Structure:

This will create the following structure:

Root Node                 | Root Node Description       | Root Node Date
   ├── Child Node 1       | Child Node 1 Description    | 2024-11-29
   └── Child Node 2       | Child Node 2 Description    | 2024-11-30
        └── Sub Child 1  | Sub Child 1 Description     | 2024-12-01

Key Points:

    Root Node contains the first column (Text), and subitems represent additional columns.
    Child and Sub-Child Nodes retain the hierarchical relationships (parent-child), and each node has its own set of subitems (additional columns).
    Ensuring Visibility: Use the EnsureVisible method to make sure that the nodes are visible when added to the TreeView.

Interactivity (Optional):

You can add events like NodeClick or NodeExpanded to interact with the TreeView and customize the grid-like behavior further. For example, you could use the NodeClick event to update other controls (such as a ListView or TextBox) when a node is selected.

Example of handling a node click:

Private Sub TreeView0_NodeClick(ByVal Node As Object)
    ' Split the node text by tab separator to extract column data
    Dim columns() As String
    columns = Split(Node.Text, vbTab)
    
    ' Display the data for the clicked node
    MsgBox "You selected: " & columns(0) & vbCrLf & _
           "Column 2: " & columns(1) & vbCrLf & _
           "Column 3: " & columns(2)
End Sub


Additional Customizations:

    Sorting: While the TreeView does not have native column sorting like a ListView, you can manually sort the nodes or perform a custom sort based on node text or subitem values.
    Node Icons: You can add icons to nodes for additional context, simulating different states or categories.
    Formatting: You can further customize the appearance of the TreeView, such as using different node styles or adding color formatting for better visual differentiation.

Limitations:

    The TreeView control is not designed for true grid functionality (like ListView), so it lacks column headers, sorting capabilities, and other grid-specific features.
    It’s primarily meant for hierarchical (parent-child) data, and the column-like behavior is simulated, which means it won't behave exactly like a traditional grid.

Summary:

By using subitems in the TreeView control, you can effectively simulate a grid-like structure with hierarchical data. This allows you to create a tree structure that also displays multiple columns, making it useful for scenarios where you need both hierarchical relationships and tabular data display. However, for complex grid functionality (such as sorting or filtering), you might still want to consider using the ListView control instead.


===============================




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
