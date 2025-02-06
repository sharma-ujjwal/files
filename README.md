```
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


Let me know if you need further
modifications!



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



```
