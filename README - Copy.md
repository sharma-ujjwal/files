Private Sub lvwData_ColumnClick(ByVal ColumnHeader As Object)
    ' Export all data to Excel
    Dim xlApp As Object
    Dim xlWorkbook As Object
    Dim xlWorksheet As Object
    Dim rowIndex As Integer
    Dim colIndex As Integer
    Dim i As Integer
    Dim savePath As String

    ' Specify the file path where the Excel file will be saved
    savePath = "C:\Users\" & Environ("Username") & "\Documents\ExportedData.xlsx"

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
    xlWorkbook.SaveAs savePath
    xlWorkbook.Close
    xlApp.Quit
    
    ' Notify user
    MsgBox "Data exported successfully to: " & savePath, vbInformation

    ' Cleanup
    Set xlWorksheet = Nothing
    Set xlWorkbook = Nothing
    Set xlApp = Nothing
End Sub
