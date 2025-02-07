
```
Imports System.Runtime.InteropServices
Imports Microsoft.Office.Interop.Access ' ✅ Add Access Interop

<ComVisible(True)>
<Guid("0ABBE17C-CD4E-4B6C-B5B7-A9E70E6845E1")>
<ClassInterface(ClassInterfaceType.None)>
Public Class FlexGridWrapper
    Implements IFlexGridWrapper

    Private accessApp As Application ' ✅ Declare Access application

    ' Constructor to attach to the running Access instance
    Public Sub New()
        Try
            ' Attach to the currently open Access instance
            accessApp = Marshal.GetActiveObject("Access.Application")
        Catch ex As Exception
            ' Handle the case where Access is not running
            Throw New Exception("Could not find an open instance of MS Access.")
        End Try
    End Sub

    ' Double-click event handler
    Private Sub flexGrid_DoubleClick(sender As Object, e As EventArgs) Handles flexGrid.DoubleClick
        Dim selectedRow As Integer = flexGrid.Row
        Dim rowData As String = ""

        ' Fetch row data
        For col As Integer = 0 To flexGrid.Cols - 1
            rowData &= flexGrid.get_TextMatrix(selectedRow, col).ToString() & ";"
        Next

        ' Open the MS Access form and pass the row data
        OpenAccessForm("frmBatchDialog", rowData)
    End Sub

    ' Function to open MS Access form
    Private Sub OpenAccessForm(ByVal formName As String, ByVal rowData As String)
        Try
            ' Open the form
            accessApp.DoCmd.OpenForm(formName, Microsoft.Office.Interop.Access.AcFormView.acNormal)

            ' Set the value of txtRowData in the opened form
            Dim accessForm As Form = accessApp.Forms(formName)
            accessForm.Controls("txtRowData").Value = rowData

        Catch ex As Exception
            Throw New Exception("Error opening MS Access form: " & ex.Message)
        End Try
    End Sub
End Class
