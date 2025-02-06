Imports System
Imports System.Drawing
Imports System.Runtime.InteropServices
Imports System.Windows.Forms
Imports C1.Win.C1FlexGrid.Classic
Imports C1.Win.C1FlexGrid


<ComVisible(True)>
<Guid("B2F88B13-7327-4AD4-8F39-281A6877B651")>
<InterfaceType(ComInterfaceType.InterfaceIsIDispatch)>
Public Interface IFlexGridWrapper
    Sub AddItem(ByVal item As String)

    Property CellChecked(ByVal row As Integer, ByVal col As Integer) As Boolean

    Event RowDoubleClick()

    Sub Clear()
    Sub ShowGrid()
    Property Rows As Integer
    Property Cols As Integer
    Function GetCellValue(ByVal row As Integer, ByVal col As Integer) As Object
    Sub SetCellValue(ByVal row As Integer, ByVal col As Integer, ByVal value As Object)
    Property FixedRows As Integer
    Property FixedCols As Integer
    Property AllowEditing As Boolean
    Sub AutoSizeCols()
    Property ThreeDLight As Boolean
    Sub AutoSizeRows()
    Sub RemoveItem(ByVal row As Integer)
    Sub Sort(ByVal col As Integer, ByVal ascending As Boolean)

    Sub DoubleClick(ByVal sender As Object, ByVal e As EventArgs)
    Function FindRow(ByVal text As String, ByVal startRow As Integer, ByVal col As Integer, ByVal wholeCell As Boolean) As Integer
    Property GridLines As Integer
    Property SelectionMode As Integer
    Property AllowSorting As Boolean
    Property AllowDragging As Boolean
    Property DataSource As Object
    Property DataMember As String
    Sub Redraw()
    Sub SaveGrid(ByVal fileName As String)
    Sub LoadGrid(ByVal fileName As String)
    Sub ApplySearch(ByVal searchText As String)
    Sub AutoSize()
    Sub Refresh()
    Sub Copy()

    Property MergedCells As Boolean

    Property ExtendLastCol As Boolean
    Property WordWrap As Boolean
    Property AllowSelection As Boolean
    Property AllowUserResizing As Boolean
    Property AutoResize As Boolean
    Property BackColorSel As System.Drawing.Color
    Property BackColor As System.Drawing.Color
    Property Ellipsis As Boolean
    Property FontName As String
    Property FontSize As Single
    Property ForeColorSel As System.Drawing.Color
    Property TabBehavior As String
    Property TopRow As Integer
    Property LeftCol As Integer
    Property ExplorerBar As Boolean

    ' New events
    Event AfterCollapse()
    Event AfterEdit()
    Event AfterRowColChange()
    Event KeyDown()
    Event KeyUp()
    Event AfterSort()
    Event AfterUserResize()
    Event BeforeEdit()
    Event BeforeMouseDown()
    Event BeforeMoveColumn()
    Event BeforeRowColChange()
    Event BeforeScroll()
    Event CellButtonClick()
    Event CellChanged()
    Event ChangeEdit()
    Event Click()
    Event ComboCloseUp()

    Event KeyDownEdit()
    Event KeyUpEdit()
    Event MouseDown()
    Event MouseUp()
    Event OLECompleteDrag()
    Event RowColChange()
    Event GridCheckboxChange()

    Property GridStyle As Integer

    Property BorderStyle As Integer

    Property PictureAlignment As Integer

    Property Collapsed(ByVal row As Integer) As Boolean

    'Sub DblClickEventHandler(sender As Object, e As EventArgs)

    Event ButtonClick(ByVal rowIndex As Integer)

End Interface

<ComVisible(True)>
<Guid("0ABBE17C-CD4E-4B6C-B5B7-A9E70E6845E1")>
<ClassInterface(ClassInterfaceType.None)>
Public Class FlexGridWrapper
    Implements IFlexGridWrapper

    Public WithEvents flexGrid As C1FlexGridClassic

    Private form As System.Windows.Forms.Form
    Private _explorerBar As ExplorerBarSettings ' Custom field

    Public Sub New()
        flexGrid = New C1FlexGridClassic()

        form = New System.Windows.Forms.Form()
        form.Controls.Add(flexGrid)
        flexGrid.Dock = System.Windows.Forms.DockStyle.Fill

    End Sub

    Public Sub AddItem(ByVal item As String) Implements IFlexGridWrapper.AddItem
        flexGrid.AddItem(item)
        AddButtonToRow(flexGrid.Rows - 1)
    End Sub

    Private Sub AddButtonToRow(ByVal rowIndex As Integer)
        Dim button As New System.Windows.Forms.Button()
        button.Text = "Click"
        button.Tag = rowIndex
        AddHandler button.Click, AddressOf OnButtonClick
        flexGrid.Controls.Add(button)
        button.Location = flexGrid.GetCellRect(rowIndex, flexGrid.Cols - 1).Location
    End Sub

    Private Sub OnButtonClick(sender As Object, e As EventArgs)
        Dim button As System.Windows.Forms.Button = CType(sender, System.Windows.Forms.Button)
        Dim rowIndex As Integer = CType(button.Tag, Integer)
        RaiseEvent ButtonClick(rowIndex)
    End Sub

    Public Event ButtonClick(ByVal rowIndex As Integer) Implements IFlexGridWrapper.ButtonClick

    Public Property Collapsed(ByVal row As Integer) As Boolean Implements IFlexGridWrapper.Collapsed
        Get
            Return True
        End Get
        Set(ByVal value As Boolean)

        End Set
    End Property

    Public Property PictureAlignment As Integer Implements IFlexGridWrapper.PictureAlignment
        Get
            Return 1
        End Get
        Set(ByVal value As Integer)

        End Set
    End Property

    Public Sub Clear() Implements IFlexGridWrapper.Clear
        flexGrid.Clear()
    End Sub

    Public Property CellChecked(ByVal row As Integer, ByVal col As Integer) As Boolean Implements IFlexGridWrapper.CellChecked
        Get
            Return flexGrid.get_Cell(CellPropertySettings.flexcpChecked, row, col)
        End Get
        Set(ByVal value As Boolean)
            flexGrid.set_Cell(CellPropertySettings.flexcpChecked, row, col, value)
        End Set
    End Property

    Public Property ThreeDLight As Boolean Implements IFlexGridWrapper.ThreeDLight
        Get
            Return True
        End Get
        Set(ByVal value As Boolean)

        End Set
    End Property



    Public Property GridStyle As Integer Implements IFlexGridWrapper.GridStyle
        Get
            Return flexGrid.GridLines
        End Get
        Set(ByVal value As Integer)
            flexGrid.GridLines = value
        End Set
    End Property

    Public Sub ShowGrid() Implements IFlexGridWrapper.ShowGrid
        form.ShowDialog()
    End Sub

    Public Property BorderStyle As Integer Implements IFlexGridWrapper.BorderStyle
        Get
            Return flexGrid.BorderStyle
        End Get
        Set(ByVal value As Integer)
            flexGrid.BorderStyle = value
        End Set
    End Property

    Public Property Rows As Integer Implements IFlexGridWrapper.Rows
        Get
            Return flexGrid.Rows
        End Get
        Set(ByVal value As Integer)
            flexGrid.Rows = value
        End Set
    End Property



    Public Property Cols As Integer Implements IFlexGridWrapper.Cols
        Get
            Return flexGrid.Cols
        End Get
        Set(ByVal value As Integer)
            flexGrid.Cols = value
        End Set
    End Property

    Public Function GetCellValue(ByVal row As Integer, ByVal col As Integer) As Object Implements IFlexGridWrapper.GetCellValue
        Return flexGrid.get_TextMatrix(row, col)
    End Function

    Public Sub SetCellValue(ByVal row As Integer, ByVal col As Integer, ByVal value As Object) Implements IFlexGridWrapper.SetCellValue
        flexGrid.set_TextMatrix(row, col, value.ToString())
    End Sub

    Public Property FixedRows As Integer Implements IFlexGridWrapper.FixedRows
        Get
            Return flexGrid.FixedRows
        End Get
        Set(ByVal value As Integer)
            flexGrid.FixedRows = value
        End Set
    End Property

    Public Property FixedCols As Integer Implements IFlexGridWrapper.FixedCols
        Get
            Return flexGrid.FixedCols
        End Get
        Set(ByVal value As Integer)
            flexGrid.FixedCols = value
        End Set
    End Property

    Public Sub TextMatrix(ByVal i As Integer, ByVal j As Integer)
        flexGrid.set_TextMatrix(i, j, "")
    End Sub

    Public Property AllowEditing As Boolean Implements IFlexGridWrapper.AllowEditing
        Get
            Return flexGrid.AllowEditing
        End Get
        Set(ByVal value As Boolean)
            flexGrid.AllowEditing = value
        End Set
    End Property

    Public Sub AutoSizeCols() Implements IFlexGridWrapper.AutoSizeCols
        flexGrid.AutoSizeCols()
    End Sub

    Public Sub AutoSizeRows() Implements IFlexGridWrapper.AutoSizeRows
        flexGrid.AutoSizeRows()
    End Sub

    Public Sub RemoveItem(ByVal row As Integer) Implements IFlexGridWrapper.RemoveItem
        flexGrid.RemoveItem(row)
    End Sub


    'Public Sub OnDoubleClick(ByVal e As EventArgs)
    '    'MessageBox.Show("Selected range")
    '    ' Use reflection to call the protected OnDoubleClick method
    '    Dim methodInfo = flexGrid.GetType().GetMethod("OnDoubleClick", Reflection.BindingFlags.Instance Or Reflection.BindingFlags.NonPublic)
    '    If methodInfo IsNot Nothing Then
    '        methodInfo.Invoke(flexGrid, New Object() {e})
    '    End If
    'End Sub


    Public Sub Sort(ByVal col As Integer, ByVal ascending As Boolean) Implements IFlexGridWrapper.Sort
        flexGrid.Sort = If(ascending, SortSettings.flexSortGenericAscending, SortSettings.flexSortGenericDescending)
    End Sub

    Public Sub _flex_BeforeDoubleClick(ByVal sender As Object, ByVal e As EventArgs) Implements IFlexGridWrapper.DoubleClick
        Dim cr As C1.Win.C1FlexGrid.CellRange
        cr = flexGrid.Selection
        MessageBox.Show("Selected range" & vbLf & cr.r1 & ":" & cr.c1 & " to " & cr.r2 & ":" & cr.c2)

        ' Perform an action to show it is working
        If cr.r1 = cr.r2 And cr.c1 = cr.c2 Then
            ' Highlight the entire row
            For col As Integer = 0 To flexGrid.Cols - 1
                flexGrid.Cell(cr.r1, col, CellPropertySettings.flexcpBackColor) = Color.Yellow
            Next
        End If
    End Sub

    Public Sub OnRowDoubleClick(sender As Object, e As EventArgs)
        RaiseEvent RowDoubleClick()
    End Sub

    Public Event RowDoubleClick() Implements IFlexGridWrapper.RowDoubleClick

    Public Function FindRow(ByVal text As String, ByVal startRow As Integer, ByVal col As Integer, ByVal wholeCell As Boolean) As Integer Implements IFlexGridWrapper.FindRow
        Return flexGrid.FindRow(text, startRow, col, wholeCell)
    End Function

    Public Property GridLines As Integer Implements IFlexGridWrapper.GridLines
        Get
            Return flexGrid.GridLines
        End Get
        Set(ByVal value As Integer)
            flexGrid.GridLines = value
        End Set
    End Property

    Public Property SelectionMode As Integer Implements IFlexGridWrapper.SelectionMode
        Get
            Return flexGrid.SelectionMode
        End Get
        Set(ByVal value As Integer)
            flexGrid.SelectionMode = value
        End Set
    End Property

    Public Property AllowSorting As Boolean Implements IFlexGridWrapper.AllowSorting
        Get
            Return flexGrid.AllowSorting
        End Get
        Set(ByVal value As Boolean)
            flexGrid.AllowSorting = value
        End Set
    End Property

    Public Sub SetAllColumnWidths(ByVal width As Integer)
        For col As Integer = 0 To flexGrid.Cols - 1
            flexGrid.set_ColWidth(col, width)
        Next
    End Sub

    Public Property AllowDragging As Boolean Implements IFlexGridWrapper.AllowDragging
        Get
            Return flexGrid.AllowDragging
        End Get
        Set(ByVal value As Boolean)
            flexGrid.AllowDragging = value
        End Set
    End Property

    Public Property DataSource As Object Implements IFlexGridWrapper.DataSource
        Get
            Return flexGrid.DataSource
        End Get
        Set(ByVal value As Object)
            flexGrid.DataSource = value
        End Set
    End Property

    Public Property DataMember As String Implements IFlexGridWrapper.DataMember
        Get
            Return flexGrid.DataMember
        End Get
        Set(ByVal value As String)
            flexGrid.DataMember = value
        End Set
    End Property

    Public Sub Redraw() Implements IFlexGridWrapper.Redraw
        flexGrid.Redraw = True
    End Sub

    Public Sub Refresh() Implements IFlexGridWrapper.Refresh
        flexGrid.Refresh()
    End Sub

    Public Sub SaveGrid(ByVal fileName As String) Implements IFlexGridWrapper.SaveGrid
        ' SaveGrid is not a member of C1FlexGridClassic, so this needs to be implemented differently
        Throw New NotImplementedException("SaveGrid is not implemented.")
    End Sub

    Public Sub LoadGrid(ByVal fileName As String) Implements IFlexGridWrapper.LoadGrid
        ' LoadGrid is not a member of C1FlexGridClassic, so this needs to be implemented differently
        Throw New NotImplementedException("LoadGrid is not implemented.")
    End Sub

    Public Sub ApplySearch(ByVal searchText As String) Implements IFlexGridWrapper.ApplySearch
        ' Clear previous search highlights
        For row As Integer = 1 To flexGrid.Rows - 1
            For col As Integer = 0 To flexGrid.Cols - 1
                flexGrid.Cell(row, col, CellPropertySettings.flexcpBackColor) = System.Drawing.Color.White
            Next
        Next

        ' Apply search and highlight matching cells
        For row As Integer = 1 To flexGrid.Rows - 1
            For col As Integer = 0 To flexGrid.Cols - 1
                If flexGrid.get_TextMatrix(row, col).ToString().Contains(searchText) Then
                    flexGrid.Cell(row, col, CellPropertySettings.flexcpBackColor) = System.Drawing.Color.Yellow
                End If
            Next
        Next
    End Sub

    Public Sub AutoSize() Implements IFlexGridWrapper.AutoSize
        flexGrid.AutoSizeCols()
        flexGrid.AutoSizeRows()
    End Sub

    ' New properties
    Public Property MergedCells As Boolean Implements IFlexGridWrapper.MergedCells
        Get
            Return flexGrid.MergeCells
        End Get
        Set(ByVal value As Boolean)
            flexGrid.MergeCells = value
        End Set
    End Property

    Public Property ExplorerBar As Boolean Implements IFlexGridWrapper.ExplorerBar
        Get
            Return flexGrid.ExplorerBar
        End Get
        Set(ByVal value As Boolean)
            flexGrid.ExplorerBar = value
        End Set
    End Property

    'Public Property ExplorerBar As ExplorerBarSettings Implements IFlexGridWrapper.ExplorerBar
    '    Get
    '        Return _explorerBar
    '    End Get
    '    Set(ByVal value As ExplorerBarSettings)
    '        _explorerBar = value
    '        ' Implement custom behavior for ExplorerBar here
    '        ' For example, you can change the appearance or behavior of the grid
    '        Select Case _explorerBar
    '            Case ExplorerBarSettings.flexExNone
    '                ' Implement behavior for flexExNone
    '            Case ExplorerBarSettings.flexExMove
    '                ' Implement behavior for flexExMove
    '            Case ExplorerBarSettings.flexExSortAndMove
    '                ' Implement behavior for flexExSortAndMove
    '            Case ExplorerBarSettings.flexExSortShowAndMove
    '                ' Implement behavior for flexExSortShowAndMove
    '        End Select
    '    End Set
    'End Property


    Public Property ExtendLastCol As Boolean Implements IFlexGridWrapper.ExtendLastCol
        Get
            Return flexGrid.ExtendLastCol
        End Get
        Set(ByVal value As Boolean)
            flexGrid.ExtendLastCol = value
        End Set
    End Property

    Public Property WordWrap As Boolean Implements IFlexGridWrapper.WordWrap
        Get
            Return flexGrid.WordWrap
        End Get
        Set(ByVal value As Boolean)
            flexGrid.WordWrap = value
        End Set
    End Property

    Public Property AllowSelection As Boolean Implements IFlexGridWrapper.AllowSelection
        Get
            Return flexGrid.AllowSelection
        End Get
        Set(ByVal value As Boolean)
            flexGrid.AllowSelection = value
        End Set
    End Property

    Public Property AllowUserResizing As Boolean Implements IFlexGridWrapper.AllowUserResizing
        Get
            Return flexGrid.AllowUserResizing
        End Get
        Set(ByVal value As Boolean)
            flexGrid.AllowUserResizing = value
        End Set
    End Property

    Public Property AutoResize As Boolean Implements IFlexGridWrapper.AutoResize
        Get
            Return flexGrid.AutoResize
        End Get
        Set(ByVal value As Boolean)
            flexGrid.AutoResize = value
        End Set
    End Property

    Public Property BackColorSel As System.Drawing.Color Implements IFlexGridWrapper.BackColorSel
        Get
            Return flexGrid.BackColorSel
        End Get
        Set(ByVal value As System.Drawing.Color)
            flexGrid.BackColorSel = value
        End Set
    End Property

    Public Property BackColor As System.Drawing.Color Implements IFlexGridWrapper.BackColor
        Get
            Return flexGrid.BackColor
        End Get
        Set(ByVal value As System.Drawing.Color)
            flexGrid.BackColor = value
        End Set
    End Property

    Public Property Ellipsis As Boolean Implements IFlexGridWrapper.Ellipsis
        Get
            Return flexGrid.Ellipsis
        End Get
        Set(ByVal value As Boolean)
            flexGrid.Ellipsis = value
        End Set
    End Property

    Public Property FontName As String Implements IFlexGridWrapper.FontName
        Get
            Return flexGrid.Font.Name
        End Get
        Set(ByVal value As String)
            flexGrid.Font = New System.Drawing.Font(value, flexGrid.Font.Size)
        End Set
    End Property

    Public Property FontSize As Single Implements IFlexGridWrapper.FontSize
        Get
            Return flexGrid.Font.Size
        End Get
        Set(ByVal value As Single)
            flexGrid.Font = New System.Drawing.Font(flexGrid.Font.Name, value)
        End Set
    End Property

    Public Property ForeColorSel As System.Drawing.Color Implements IFlexGridWrapper.ForeColorSel
        Get
            Return flexGrid.ForeColorSel
        End Get
        Set(ByVal value As System.Drawing.Color)
            flexGrid.ForeColorSel = value
        End Set
    End Property

    Public Property TabBehavior As String Implements IFlexGridWrapper.TabBehavior
        Get
            Return flexGrid.TabBehavior
        End Get
        Set(ByVal value As String)
            flexGrid.TabBehavior = value
        End Set
    End Property

    Public Property TopRow As Integer Implements IFlexGridWrapper.TopRow
        Get
            Return flexGrid.TopRow
        End Get
        Set(ByVal value As Integer)
            flexGrid.TopRow = value
        End Set
    End Property

    Public Property LeftCol As Integer Implements IFlexGridWrapper.LeftCol
        Get
            Return flexGrid.LeftCol
        End Get
        Set(ByVal value As Integer)
            flexGrid.LeftCol = value
        End Set
    End Property

    ' New events
    Public Event AfterCollapse() Implements IFlexGridWrapper.AfterCollapse
    Public Event AfterEdit() Implements IFlexGridWrapper.AfterEdit
    Public Event AfterRowColChange() Implements IFlexGridWrapper.AfterRowColChange
    Public Event KeyDown() Implements IFlexGridWrapper.KeyDown
    Public Event KeyUp() Implements IFlexGridWrapper.KeyUp
    Public Event AfterSort() Implements IFlexGridWrapper.AfterSort
    Public Event AfterUserResize() Implements IFlexGridWrapper.AfterUserResize
    Public Event BeforeEdit() Implements IFlexGridWrapper.BeforeEdit
    Public Event BeforeMouseDown() Implements IFlexGridWrapper.BeforeMouseDown
    Public Event BeforeMoveColumn() Implements IFlexGridWrapper.BeforeMoveColumn
    Public Event BeforeRowColChange() Implements IFlexGridWrapper.BeforeRowColChange
    Public Event BeforeScroll() Implements IFlexGridWrapper.BeforeScroll
    Public Event CellButtonClick() Implements IFlexGridWrapper.CellButtonClick
    Public Event CellChanged() Implements IFlexGridWrapper.CellChanged
    Public Event ChangeEdit() Implements IFlexGridWrapper.ChangeEdit
    Public Event Click() Implements IFlexGridWrapper.Click
    Public Event ComboCloseUp() Implements IFlexGridWrapper.ComboCloseUp
    Public Event KeyDownEdit() Implements IFlexGridWrapper.KeyDownEdit
    Public Event KeyUpEdit() Implements IFlexGridWrapper.KeyUpEdit
    Public Event MouseDown() Implements IFlexGridWrapper.MouseDown
    Public Event MouseUp() Implements IFlexGridWrapper.MouseUp
    Public Event OLECompleteDrag() Implements IFlexGridWrapper.OLECompleteDrag
    Public Event RowColChange() Implements IFlexGridWrapper.RowColChange
    Public Event GridCheckboxChange() Implements IFlexGridWrapper.GridCheckboxChange

    ' Event handlers
    Private Sub OnAfterCollapse()
        RaiseEvent AfterCollapse()
    End Sub

    Private Sub OnAfterEdit()
        RaiseEvent AfterEdit()
    End Sub

    Private Sub OnAfterRowColChange()
        RaiseEvent AfterRowColChange()
    End Sub

    Private Sub OnKeyDown()
        RaiseEvent KeyDown()
    End Sub

    Private Sub OnKeyUp()
        RaiseEvent KeyUp()
    End Sub

    Private Sub OnAfterSort()
        RaiseEvent AfterSort()
    End Sub

    Private Sub OnAfterUserResize()
        RaiseEvent AfterUserResize()
    End Sub

    Private Sub OnBeforeEdit()
        RaiseEvent BeforeEdit()
    End Sub

    Private Sub OnBeforeMouseDown()
        RaiseEvent BeforeMouseDown()
    End Sub

    Private Sub OnBeforeMoveColumn()
        RaiseEvent BeforeMoveColumn()
    End Sub

    Private Sub OnBeforeRowColChange()
        RaiseEvent BeforeRowColChange()
    End Sub

    Private Sub OnBeforeScroll()
        RaiseEvent BeforeScroll()
    End Sub

    Private Sub OnCellButtonClick()
        RaiseEvent CellButtonClick()
    End Sub

    Private Sub OnCellChanged()
        RaiseEvent CellChanged()
    End Sub

    Private Sub OnChangeEdit()
        RaiseEvent ChangeEdit()
    End Sub

    Private Sub OnClick()
        RaiseEvent Click()
    End Sub




    Public Sub Copy() Implements IFlexGridWrapper.Copy
        Dim selectedText As New System.Text.StringBuilder()
        Dim rowStart As Integer = Math.Min(flexGrid.Row, flexGrid.RowSel)
        Dim rowEnd As Integer = Math.Max(flexGrid.Row, flexGrid.RowSel)
        Dim colStart As Integer = Math.Min(flexGrid.Col, flexGrid.ColSel)
        Dim colEnd As Integer = Math.Max(flexGrid.Col, flexGrid.ColSel)

        For row As Integer = rowStart To rowEnd
            For col As Integer = colStart To colEnd
                selectedText.Append(flexGrid.get_TextMatrix(row, col))
                If col < colEnd Then
                    selectedText.Append(vbTab)
                End If
            Next
            If row < rowEnd Then
                selectedText.AppendLine()
            End If
        Next
        Clipboard.SetText(selectedText.ToString())
    End Sub

    Public Sub PopulateGridData(ByVal GridSource As String, ByVal SysCols As String, ByVal HideCols As String)
        ' Clear the grid
        flexGrid.Clear()

        ' Split the SysCols and HideCols into arrays
        Dim sysColsArray() As String = SysCols.Split(";"c)
        Dim hideColsArray() As String = HideCols.Split(";"c)

        ' Set the number of columns based on SysCols
        flexGrid.Cols = sysColsArray.Length

        ' Set the column headers
        For i As Integer = 0 To sysColsArray.Length - 1
            flexGrid.set_TextMatrix(0, i, sysColsArray(i))
        Next

        ' Fetch data from the data source (GridSource)
        ' This is a placeholder for actual data fetching logic
        ' You need to replace this with your actual data fetching logic
        Dim data As DataTable = FetchData(GridSource)

        ' Populate the grid with data
        flexGrid.Rows = data.Rows.Count + 1 ' +1 for the header row
        For row As Integer = 0 To data.Rows.Count - 1
            For col As Integer = 0 To data.Columns.Count - 1
                flexGrid.set_TextMatrix(row + 1, col, data.Rows(row)(col).ToString())
            Next
        Next

        ' Hide specified columns
        For Each colName As String In hideColsArray
            For col As Integer = 0 To flexGrid.Cols - 1
                If flexGrid.get_TextMatrix(0, col) = colName Then
                    flexGrid.set_ColWidth(col, 0)
                    Exit For
                End If
            Next
        Next
    End Sub



    Private Function FetchData(ByVal query As String) As DataTable
        ' Placeholder function to fetch data from a data source
        ' Replace this with your actual data fetching logic
        Dim dt As New DataTable()
        ' Add columns
        dt.Columns.Add("Step #")
        dt.Columns.Add("Step Name")
        dt.Columns.Add("Owner")
        dt.Columns.Add("Avg")
        dt.Columns.Add("StDev")
        dt.Columns.Add("Runs")
        dt.Columns.Add("Will Run")
        dt.Columns.Add("Duration")
        dt.Columns.Add("Result")
        ' Add sample data
        dt.Rows.Add("001", "Sample Step", "John Doe", "5.0", "0.5", "10", "Yes", "50", "Success")
        Return dt
    End Function

    'Public Sub DblClickEventHandler(sender As Object, e As EventArgs) Implements IFlexGridWrapper.DblClickEventHandler
    '    Throw New NotImplementedException()
    'End Sub

End Class



It looks like you're using a C1FlexGridClassic control inside a wrapper class. You mentioned that row selection isn't responding to clicks, and you also want to display a custom box with row details.

Fix Row Selection Click Issue

From your code, it seems like row selection on click isn't explicitly handled. Try adding an event handler for the MouseDown or Click event of the grid to manually select the row:

Private Sub flexGrid_MouseDown(sender As Object, e As MouseEventArgs) Handles flexGrid.MouseDown
    Dim hitTest As C1.Win.C1FlexGrid.HitTestInfo = flexGrid.HitTest(e.X, e.Y)

    If hitTest.Type = C1.Win.C1FlexGrid.HitTestTypeEnum.Cell Then
        flexGrid.Row = hitTest.Row
        flexGrid.Select(hitTest.Row, 0, hitTest.Row, flexGrid.Cols - 1)
    End If
End Sub

This ensures that clicking on a row selects it.

Display Custom Message Box with Row Details

If you want to display a message box with row details when a row is double-clicked, modify the _flex_BeforeDoubleClick method:

Private Sub flexGrid_DoubleClick(sender As Object, e As EventArgs) Handles flexGrid.DoubleClick
    Dim selectedRow As Integer = flexGrid.Row
    Dim rowData As String = ""

    For col As Integer = 0 To flexGrid.Cols - 1
        rowData &= flexGrid.GetCellValue(selectedRow, col).ToString() & " | "
    Next

    MessageBox.Show("Row " & selectedRow & " Selected:" & vbCrLf & rowData, "Row Details", MessageBoxButtons.OK, MessageBoxIcon.Information)
End Sub

Summary

Row selection issue: Handled by explicitly setting the selected row in MouseDown.

Custom box with row details: Displays row data in a message box on double-click.


Let me know if you need further modifications!




To use your DLL (FlexGridWrapper) inside an MS Access form, follow these steps:


---

1. Register the DLL in Windows

Since your DLL is a .NET COM-visible component, you need to register it for use in Access.

1. Open Command Prompt as Administrator.


2. Navigate to the folder where your DLL is located:

cd C:\Path\To\Your\DLL


3. Register the DLL using RegAsm (for .NET Framework) or RegSvcs (for .NET Core):

regasm FlexGridWrapper.dll /codebase

If your DLL is built for .NET Core, use:

regsvcs FlexGridWrapper.dll


4. If needed, also register the Type Library (TLB):

regasm FlexGridWrapper.dll /tlb




---

2. Add Reference to DLL in MS Access

1. Open your MS Access database.


2. Press ALT + F11 to open VBA Editor.


3. Go to Tools → References.


4. Click Browse and select your FlexGridWrapper.dll or FlexGridWrapper.tlb.


5. Click OK.




---

3. Use the DLL in VBA

Now, you can use the DLL in your MS Access Form.

Example: Initialize and Show Grid

In your VBA module or form event, create an instance of the FlexGridWrapper class:

Dim objFlexGrid As Object

Sub LoadGrid()
    ' Create an instance of the DLL class
    Set objFlexGrid = CreateObject("FlexGridWrapper")
    
    ' Show the FlexGrid inside a Windows Form
    objFlexGrid.ShowGrid
End Sub

Example: Add a Row

Sub AddRowToGrid()
    If Not objFlexGrid Is Nothing Then
        objFlexGrid.AddItem "New Row Data"
    End If
End Sub

Example: Handle Row Click

To capture a row click and display details in a message box:

Sub HandleRowClick()
    Dim rowIndex As Integer
    rowIndex = objFlexGrid.Row ' Get the selected row index
    
    Dim rowData As String
    rowData = objFlexGrid.GetCellValue(rowIndex, 0) ' Get data from the first column
    
    MsgBox "Selected Row: " & rowIndex & vbCrLf & "Data: " & rowData, vbInformation, "Row Clicked"
End Sub


---

4. Attach to a Form Event in MS Access

If you want to trigger the grid when clicking a button in an Access form:

1. Open your MS Access Form in Design View.


2. Add a Button.


3. Open Properties → Click [Event Procedure] for "On Click".


4. Click [...] to open the VBA Editor.


5. Add this code:

Private Sub btnShowGrid_Click()
    LoadGrid
End Sub


6. Save & Run the form.




---

Summary

✅ Register the DLL using regasm.
✅ Add reference in MS Access VBA.
✅ Create an instance of the DLL in VBA.
✅ Call methods like ShowGrid, AddItem, GetCellValue, etc.
✅ Attach to a button click event in an Access form.

Now, your custom DLL should work inside an MS Access form! Let me know if you need help debugging any issues.

