
```
Imports System.Runtime.InteropServices

<ComVisible(True)>
<Guid("0ABBE17C-CD4E-4B6C-B5B7-A9E70E6845E1")>
<ClassInterface(ClassInterfaceType.None)>
Public Class FlexGridWrapper
    Implements IFlexGridWrapper

    ' Double-click event handler
    Private Sub flexGrid_DoubleClick(sender As Object, e As EventArgs) Handles flexGrid.DoubleClick
        Dim selectedRow As Integer = flexGrid.Row
        Dim rowData As String = ""

        ' Fetch row data
        For col As Integer = 0 To flexGrid.Cols - 1
            rowData &= flexGrid.get_TextMatrix(selectedRow, col).ToString() & ";"
        Next

        ' Open MS Access and pass data
        OpenAccessForm("frmBatchDialog", rowData)
    End Sub

    ' Open MS Access form without needing a COM reference
    Private Sub OpenAccessForm(ByVal formName As String, ByVal rowData As String)
        Try
            ' Get a reference to the running MS Access instance
            Dim accessApp As Object = Marshal.GetActiveObject("Access.Application")

            ' Open the form
            accessApp.DoCmd.OpenForm(formName, 0) ' 0 = acNormal view

            ' Set form field
            Dim accessForm As Object = accessApp.Forms(formName)
            accessForm.Controls("txtRowData").Value = rowData

        Catch ex As Exception
            Throw New Exception("Error opening MS Access form: " & ex.Message)
        End Try
    End Sub
End Class
