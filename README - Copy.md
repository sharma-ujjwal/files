Private Sub Form_Load()
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

    Dim parentItem As ListItem
    Dim childItem As ListItem

    ' Add the parent node (Past Due)
    Set parentItem = Me.ListView1.ListItems.Add(, "PastDue", "Past Due")
    parentItem.ForeColor = RGB(255, 0, 0) ' Set parent row text color to red for emphasis

    ' Add child nodes under the parent
    Set childItem = Me.ListView1.ListItems.Add(, , "1") ' First child
    childItem.Indentation = 1 ' Indent to indicate it is a child
    childItem.SubItems(1) = "09/15/2023"
    childItem.SubItems(2) = "10/11/2024"
    childItem.SubItems(3) = "Martel, Tina D"
    childItem.SubItems(4) = "Unspecified Letter 1"

    Set childItem = Me.ListView1.ListItems.Add(, , "2") ' Second child
    childItem.Indentation = 1 ' Indent to indicate it is a child
    childItem.SubItems(1) = "09/15/2023"
    childItem.SubItems(2) = "10/11/2024"
    childItem.SubItems(3) = "Martel, Tina D"
    childItem.SubItems(4) = "Unspecified Letter 2"

    ' Add another parent node (Three Months)
    Set parentItem = Me.ListView1.ListItems.Add(, "ThreeMonths", "Three Months")
    parentItem.ForeColor = RGB(0, 0, 255) ' Set parent row text color to blue

    ' Add child nodes under this parent
    Set childItem = Me.ListView1.ListItems.Add(, , "3") ' First child under Three Months
    childItem.Indentation = 1
    childItem.SubItems(1) = "12/13/2024"
    childItem.SubItems(2) = "12/12/2024"
    childItem.SubItems(3) = "Guillermo, Donald"
    childItem.SubItems(4) = "Peer Review Request"
End Sub




Private Sub Form_Load()
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

    Dim parentItem As ListItem
    Dim childItem As ListItem

    ' Add the parent row (Past Due)
    Set parentItem = Me.ListView1.ListItems.Add(, "PastDue", "Past Due")
    parentItem.ForeColor = RGB(255, 0, 0) ' Set parent row text color to red for emphasis

    ' Add child rows under the parent with simulated indentation
    Set childItem = Me.ListView1.ListItems.Add(, , "   1") ' First child (prefix spaces for indentation)
    childItem.SubItems(1) = "09/15/2023"
    childItem.SubItems(2) = "10/11/2024"
    childItem.SubItems(3) = "Martel, Tina D"
    childItem.SubItems(4) = "Unspecified Letter 1"

    Set childItem = Me.ListView1.ListItems.Add(, , "   2") ' Second child (prefix spaces for indentation)
    childItem.SubItems(1) = "09/15/2023"
    childItem.SubItems(2) = "10/11/2024"
    childItem.SubItems(3) = "Martel, Tina D"
    childItem.SubItems(4) = "Unspecified Letter 2"

    ' Add another parent row (Three Months)
    Set parentItem = Me.ListView1.ListItems.Add(, "ThreeMonths", "Three Months")
    parentItem.ForeColor = RGB(0, 0, 255) ' Set parent row text color to blue

    ' Add child rows under this parent with simulated indentation
    Set childItem = Me.ListView1.ListItems.Add(, , "   3") ' First child under Three Months
    childItem.SubItems(1) = "12/13/2024"
    childItem.SubItems(2) = "12/12/2024"
    childItem.SubItems(3) = "Guillermo, Donald"
    childItem.SubItems(4) = "Peer Review Request"
End Sub
