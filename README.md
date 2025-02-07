Option Compare Database
Option Explicit

Private WithEvents m_oFormEventHandler As FormEventHandler
Private WithEvents m_oBatchStepEventHandler As GridEventHandler
Private WithEvents m_oBatchLogEventHandler As GridEventHandler
Private WithEvents m_oBatchStepLogEventHandler As GridEventHandler



Public Property Get EventHandler() As FormEventHandler
10        If IsNothing(m_oFormEventHandler) Then
20            Set m_oFormEventHandler = New FormEventHandler
30            m_oFormEventHandler.Initialize SourceForm:=Me.Form, EntityCode:=ENTITYCODE_BATCH_MAINTENANCE, HighlightChanges:=False, InitialFocusControl:=Me.tabGeneral, CloseButton:=Me.cmdClose, FormTitleLabel:=Me.lblFormTitle
40            m_oFormEventHandler.BindFormTitle imageControl:=Me.imgFormTitle, imageLabel:=Me.lblFormTitle
50            m_oFormEventHandler.BindFormCommand imageControl:=Me.imgGeneralCommands, imageLabel:=Me.lblGeneralCommands
60            m_oFormEventHandler.BindFormCommand imageControl:=Me.imgHistoryCommands, imageLabel:=Me.lblHistoryCommands
70            m_oFormEventHandler.BindFormCommand imageControl:=Me.imgReportsCommands, imageLabel:=Me.lblReportsCommands
80        End If
90        Set EventHandler = m_oFormEventHandler
End Property
Public Sub Form_Load()
    Dim objFlexGrid As Object
    Set objFlexGrid = CreateObject("C1FlexGridInterOp.FlexGridWrapper")
End Sub

Public Property Get BatchStepEventHandler() As GridEventHandler
10        If IsNothing(m_oBatchStepEventHandler) Then
20            Set m_oBatchStepEventHandler = Me.EventHandler.GetGridEventHandler(Me.fgBatchStep)
30            m_oBatchStepEventHandler.BindControls GridCaption:="Batch Step"
40            m_oBatchStepEventHandler.BindControls SizeToControl:=Me.boxBatchStep
50            m_oBatchStepEventHandler.BindControls GotoFirstButton:=Me.cmdBatchStepGotoFirst
60            m_oBatchStepEventHandler.BindControls GotoPreviousButton:=Me.cmdBatchStepGotoPrevious
70            m_oBatchStepEventHandler.BindControls RecordInfoTextbox:=Me.txtBatchStepRecordInfo
80            m_oBatchStepEventHandler.BindControls GotoNextButton:=Me.cmdBatchStepGotoNext
90            m_oBatchStepEventHandler.BindControls GotoLastButton:=Me.cmdBatchStepGotoLast
100           m_oBatchStepEventHandler.BindControls RefreshButton:=Me.cmdBatchStepRefresh
110           m_oBatchStepEventHandler.BindControls ExportButton:=Me.cmdBatchStepExport
120           m_oBatchStepEventHandler.BindControls SettingsButton:=Me.cmdBatchStepGridSettings

130           m_oBatchStepEventHandler.ExplorerBar = flexExMove
140       End If
150       Set BatchStepEventHandler = m_oBatchStepEventHandler
End Property
Public Property Get BatchLogEventHandler() As GridEventHandler
10        If IsNothing(m_oBatchLogEventHandler) Then
20            Set m_oBatchLogEventHandler = Me.EventHandler.GetGridEventHandler(Me.fgBatchLog)
30            m_oBatchLogEventHandler.BindControls GridCaption:="Batch Log"
40            m_oBatchLogEventHandler.BindControls SizeToControl:=Me.boxBatchLog
50            m_oBatchLogEventHandler.BindControls GotoFirstButton:=Me.cmdBatchLogGotoFirst
60            m_oBatchLogEventHandler.BindControls GotoPreviousButton:=Me.cmdBatchLogGotoPrevious
70            m_oBatchLogEventHandler.BindControls RecordInfoTextbox:=Me.txtBatchLogRecordInfo
80            m_oBatchLogEventHandler.BindControls GotoNextButton:=Me.cmdBatchLogGotoNext
90            m_oBatchLogEventHandler.BindControls GotoLastButton:=Me.cmdBatchLogGotoLast
100           m_oBatchLogEventHandler.BindControls RefreshButton:=Me.cmdBatchLogRefresh
110           m_oBatchLogEventHandler.BindControls ExportButton:=Me.cmdBatchLogExport
120           m_oBatchLogEventHandler.BindControls SettingsButton:=Me.cmdBatchLogGridSettings

130           m_oBatchLogEventHandler.ExplorerBar = flexExMove
140       End If
150       Set BatchLogEventHandler = m_oBatchLogEventHandler
End Property
Public Property Get BatchStepLogEventHandler() As GridEventHandler
10        If IsNothing(m_oBatchStepLogEventHandler) Then
20            Set m_oBatchStepLogEventHandler = Me.EventHandler.GetGridEventHandler(Me.fgBatchStepLog)
30            m_oBatchStepLogEventHandler.BindControls GridCaption:="Batch Log"
40            m_oBatchStepLogEventHandler.BindControls SizeToControl:=Me.boxBatchStepLog
50            m_oBatchStepLogEventHandler.BindControls GotoFirstButton:=Me.cmdBatchStepLogGotoFirst
60            m_oBatchStepLogEventHandler.BindControls GotoPreviousButton:=Me.cmdBatchStepLogGotoPrevious
70            m_oBatchStepLogEventHandler.BindControls RecordInfoTextbox:=Me.txtBatchStepLogRecordInfo
80            m_oBatchStepLogEventHandler.BindControls GotoNextButton:=Me.cmdBatchStepLogGotoNext
90            m_oBatchStepLogEventHandler.BindControls GotoLastButton:=Me.cmdBatchStepLogGotoLast
100           m_oBatchStepLogEventHandler.BindControls RefreshButton:=Me.cmdBatchStepLogRefresh
110           m_oBatchStepLogEventHandler.BindControls ExportButton:=Me.cmdBatchStepLogExport
120           m_oBatchStepLogEventHandler.BindControls SettingsButton:=Me.cmdBatchStepLogGridSettings

130           m_oBatchStepLogEventHandler.ExplorerBar = flexExMove
140       End If
150       Set BatchStepLogEventHandler = m_oBatchStepLogEventHandler
End Property


Private Function fgBatchLog_GetGridSource() As String
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          Dim oSQL As New SQLBuilder
          
          '--Hard coding last 50 runs.
20        oSQL.Start 0, "SELECT TOP 50"
30        oSQL.Append 1, "BatchLogID"
40        oSQL.Append 1, ", RunDate AS [Started]"
50        oSQL.Append 1, ", StopDate AS [Stopped]"
60        oSQL.Append 1, ", RunBy AS [Run By]"
70        oSQL.Append 1, ", ScheduleDate AS [Schedule Date]"
80        oSQL.Append 1, ", StartStepNumber AS [Start #]"
90        oSQL.Append 1, ", StopStepNumber AS [Stop #]"
100       oSQL.Append 1, ", StepsExecuted AS [Executed]"
110       oSQL.Append 1, ", StepsFailed AS [Failed]"
120       oSQL.Append 1, ", Result AS [Result]"
130       oSQL.Append 1, ", LastStepExecuted AS [Last #]"
          
140       oSQL.Append 1, ", Name"
150       oSQL.Append 1, ", ForceRunActive"
160       oSQL.Append 1, ", ForceRunInactive"
          
170       oSQL.Append 0, "FROM"
180       oSQL.Append 1, "BatchLog"
190       oSQL.Append 0, "WHERE"
          
200       If IsNull(Me.cboBatchName) Then
210           oSQL.Append 1, "1=0"
220       Else
230           oSQL.Append 1, "Name=" & q(CleanSQL(Me.cboBatchName))
240       End If
          
250       oSQL.Append 0, "ORDER BY"
260       oSQL.Append 1, "RunDate DESC"

          '--Exit the method successfully.
270       fgBatchLog_GetGridSource = oSQL.ToString()
280       Exit Function
          
ErrorHandler:
          Dim lngErrNumber As Long
          Dim strErrDescription As String
290       lngErrNumber = Err.Number
300       strErrDescription = Err.Description
310       ErrBox Me.Name, "fgBatchLog_GetGridSource", strErrDescription, lngErrNumber, Erl, , , False
320       Err.Raise lngErrNumber, , strErrDescription
          
End Function


Private Sub fgBatchLog_PopulateGrid(Optional ByVal DefaultID As Variant = Null)
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          Dim strSQL As String
          Dim strSysCols As String
          
20        strSQL = fgBatchLog_GetGridSource()
30        strSysCols = "Name;ForceRunActive;ForceRunInactive"

          '--Populate the grid with data.
40        Me.BatchLogEventHandler.Populate GridSource:=strSQL, SysCols:=strSysCols
              
          '--Format individual columns in the grid.
50        Me.BatchLogEventHandler.ColProperty("Started").DataFormat = eGridDataFormat.evDateTimeShort
60        Me.BatchLogEventHandler.ColProperty("Stopped").DataFormat = eGridDataFormat.evDateTimeShort
                  
          '--Finalize the grid appearence.
70        Me.BatchLogEventHandler.Finalize Cond1Key:="BatchLogID", Cond1Value:=DefaultID
          
          '--Update controls related to the grid.
80        fgBatchLog_UpdateControls

          '--Exit the method successfully.
90        Exit Sub
          
ErrorHandler:
          Dim lngErrNumber As Long
          Dim strErrDescription As String
100       lngErrNumber = Err.Number
110       strErrDescription = Err.Description
120       ErrBox Me.Name, "fgBatchLog_PopulateGrid", strErrDescription, lngErrNumber, Erl, , , False
130       Err.Raise lngErrNumber, , strErrDescription
          
End Sub


Private Sub fgBatchLog_UpdateControls()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          Dim bRowIsValid As Boolean
          
20        bRowIsValid = Me.BatchLogEventHandler.RowIsValid()
          
30        Dim bRetryBatchLog As Boolean: bRetryBatchLog = False
40        Dim bResumeNextBatchLog As Boolean: bResumeNextBatchLog = False
50        Dim bLogReport As Boolean: bLogReport = Not IsExecuting() And bRowIsValid
                  
60        If bRowIsValid Then
70            Me.txtBatchLogID = Me.BatchLogEventHandler.RowDataDecode("BatchLogID")
              
              Dim strResult As String
80            strResult = Nz(Me.BatchLogEventHandler.RowDataDecode("Result"), "")
              
90            bRetryBatchLog = Not IsExecuting() And (strResult = RESULT_IN_PROGRESS Or strResult = RESULT_FAILED)
100           bResumeNextBatchLog = Not IsExecuting() And (strResult = RESULT_IN_PROGRESS Or strResult = RESULT_FAILED Or strResult = RESULT_STOPPED)
110       End If
          
120       Me.EventHandler.SetControlState c:=Me.cmdRetryBatchLog, Enable:=bRetryBatchLog, FocusControl:=Me.cmdBatchLogRefresh
130       Me.EventHandler.SetControlState c:=Me.cmdResumeNextBatchLog, Enable:=bResumeNextBatchLog, FocusControl:=Me.cmdBatchLogRefresh
140       Me.EventHandler.SetControlState c:=Me.cmdLogReport, Enable:=bLogReport, FocusControl:=Me.cmdBatchLogRefresh
          
          '--Exit the method successfully.
150       Exit Sub
          
ErrorHandler:
          Dim lngErrNumber As Long
          Dim strErrDescription As String
160       lngErrNumber = Err.Number
170       strErrDescription = Err.Description
180       ErrBox Me.Name, "fgBatchLog_UpdateControls", strErrDescription, lngErrNumber, Erl, , , False
190       Err.Raise lngErrNumber, , strErrDescription
          
End Sub


Private Function fgBatchStepLog_GetGridSource() As String
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          Dim oSQL As New SQLBuilder
          
20        oSQL.Start 0, "SELECT"
30        oSQL.Append 1, "BatchStepLogID"
40        oSQL.Append 1, ", RIGHT('000' + CAST(StepNumber AS VARCHAR), 3) AS [Step #]"
50        oSQL.Append 1, ", Name AS [Step Name]"
60        oSQL.Append 1, ", RunDate AS [Started]"
70        oSQL.Append 1, ", StopDate AS [Stopped]"
80        oSQL.Append 1, ", Owner AS [Owner]"
90        oSQL.Append 1, ", Attempts AS [Attempts]"
100       oSQL.Append 1, ", Result AS [Result]"
110       oSQL.Append 1, ", Message"
          
120       oSQL.Append 0, "FROM"
130       oSQL.Append 1, "BatchStepLog"
140       oSQL.Append 0, "WHERE"
          
150       If IsNull(Me.txtBatchLogID) Then
160           oSQL.Append 1, "1=0"
170       Else
180           oSQL.Append 1, "BatchLogID=" & q(GtoS(Me.txtBatchLogID.Value))
190       End If
          
200       oSQL.Append 0, "ORDER BY"
210       oSQL.Append 1, "StepNumber"
220       oSQL.Append 1, ", RunDate"

          '--Exit the method successfully.
230       fgBatchStepLog_GetGridSource = oSQL.ToString()
240       Exit Function
          
ErrorHandler:
          Dim lngErrNumber As Long
          Dim strErrDescription As String
250       lngErrNumber = Err.Number
260       strErrDescription = Err.Description
270       ErrBox Me.Name, "fgBatchStepLog_GetGridSource", strErrDescription, lngErrNumber, Erl, , , False
280       Err.Raise lngErrNumber, , strErrDescription
          
End Function


Private Sub fgBatchStepLog_PopulateGrid(Optional ByVal DefaultID As Variant = Null)
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          Dim strSQL As String
          Dim strSysCols As String
          
20        strSQL = fgBatchStepLog_GetGridSource()
30        strSysCols = "Message"
                    
          '--Populate the grid with data.
40        Me.BatchStepLogEventHandler.Populate GridSource:=strSQL, SysCols:=strSysCols
              
          '--Format individual columns in the grid.
50        Me.BatchStepLogEventHandler.ColProperty("Started").DataFormat = eGridDataFormat.evDateLong
60        Me.BatchStepLogEventHandler.ColProperty("Stopped").DataFormat = eGridDataFormat.evDateLong
                  
          '--Finalize the grid appearence.
70        Me.BatchStepLogEventHandler.Finalize Cond1Key:="BatchStepLogID", Cond1Value:=DefaultID
          
          '--Update controls related to the grid.
80        fgBatchStepLog_UpdateControls

          '--Exit the method successfully.
90        Exit Sub
          
ErrorHandler:
          Dim lngErrNumber As Long
          Dim strErrDescription As String
100       lngErrNumber = Err.Number
110       strErrDescription = Err.Description
120       ErrBox Me.Name, "fgBatchStepLog_PopulateGrid", strErrDescription, lngErrNumber, Erl, , , False
130       Err.Raise lngErrNumber, , strErrDescription
          
End Sub


Private Sub fgBatchStepLog_UpdateControls()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          Dim bRowIsValid As Boolean
          
20        bRowIsValid = Me.BatchStepLogEventHandler.RowIsValid()
          
30        Me.txtBatchStepLogMessage = Null
              
40        If bRowIsValid Then
50            Me.txtBatchStepLogID = Me.BatchStepLogEventHandler.RowDataDecode("BatchStepLogID")
60            Me.txtBatchStepLogMessage = Me.BatchStepLogEventHandler.RowDataDecode("Message")
70        End If
          
          '--Exit the method successfully.
80        Exit Sub
          
ErrorHandler:
          Dim lngErrNumber As Long
          Dim strErrDescription As String
90        lngErrNumber = Err.Number
100       strErrDescription = Err.Description
110       ErrBox Me.Name, "fgBatchStepLog_UpdateControls", strErrDescription, lngErrNumber, Erl, , , False
120       Err.Raise lngErrNumber, , strErrDescription
          
End Sub


Private Function fgBatchStep_GetGridSource() As String
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          Dim oSQL As New SQLBuilder

20        oSQL.Start 0, "SELECT"
30        oSQL.Append 1, "BatchID"
40        oSQL.Append 1, ", BatchStepID"
50        oSQL.Append 1, ", BatchStepNumber"
60        oSQL.Append 1, ", StepNumberDetail AS [Step #]"
70        oSQL.Append 1, ", Name AS [Step Name]"
80        oSQL.Append 1, ", Owner AS [Owner]"
90        oSQL.Append 1, ", Avg AS [Avg]"
100       oSQL.Append 1, ", StDev AS [StDev]"
110       oSQL.Append 1, ", Runs AS [Runs]"
120       oSQL.Append 1, ", NULL AS [Will Run]"
130       oSQL.Append 1, ", Duration AS [Duration]"
140       oSQL.Append 1, ", Result AS [Result]"
150       oSQL.Append 1, ", Method AS [Method]"
160       oSQL.Append 1, ", Args AS [Args]"
170       oSQL.Append 1, ", Schedule AS [Schedule]"
180       oSQL.Append 1, ", Comment AS [Comment]"
          
190       If IsNull(Me.txtBatchLogID) Then
200           oSQL.Append 0, "FROM dbo.fBatchStepSearch(NULL)"
210       Else
220           oSQL.Append 0, "FROM dbo.fBatchStepSearch(" & q(GtoS(Me.txtBatchLogID.Value)) & ")"
230       End If
              
240       oSQL.Append 0, "WHERE"
250       oSQL.Append 1, "BatchName = " & q(CleanSQL(Nz(Me.cboBatchName, "")))
260       If Not IsNull(Me.txtFilter) Then
270           oSQL.Append 1, "AND ("
280           oSQL.Append 2, "Name LIKE '%" & CleanSQL(Me.txtFilter) & "%'"
290           oSQL.Append 2, "OR Comment LIKE '%" & CleanSQL(Me.txtFilter) & "%'"
300           oSQL.Append 2, "OR Method LIKE '%" & CleanSQL(Me.txtFilter) & "%'"
310           oSQL.Append 2, "OR Args LIKE '%" & CleanSQL(Me.txtFilter) & "%'"
320           oSQL.Append 2, "OR StepNumberDetail LIKE '%" & CleanSQL(Me.txtFilter) & "%'"
330           oSQL.Append 1, ")"
340       End If
          
350       oSQL.Append 0, "ORDER BY"
360       oSQL.Append 1, "StepNumber"
          
          '--Exit the method successfully.
370       fgBatchStep_GetGridSource = oSQL.ToString()
380       Exit Function
          
ErrorHandler:
          Dim lngErrNumber As Long
          Dim strErrDescription As String
390       lngErrNumber = Err.Number
400       strErrDescription = Err.Description
410       ErrBox Me.Name, "fgBatchStep_GetGridSource", strErrDescription, lngErrNumber, Erl, , , False
420       Err.Raise lngErrNumber, , strErrDescription
          
End Function


Private Sub fgBatchStep_PopulateGrid(Optional ByVal DefaultID As Variant = Null)
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          Dim strSQL As String
          Dim strSysCols As String
          Dim strHideCols As String
          
20        strSQL = fgBatchStep_GetGridSource()
30        strSysCols = "Comment;BatchStepNumber"
40        strHideCols = "Method;Args;Schedule"
          
          '--Populate the grid with data.
50        Me.BatchStepEventHandler.Populate GridSource:=strSQL, SysCols:=strSysCols, HideCols:=strHideCols

          Dim lngRow As Long
          Dim lngCount As Long
          Dim lngBatchStepNumber As Long
          Dim bWillExecute As Boolean
          Dim bCurrentStepFound As Boolean
          
60        Me.txtEstimatedDurationSeconds = Null
70        Me.txtEstimatedRemainingDurationSeconds = Null
          
80        lngCount = Me.BatchStepEventHandler.RowCount()
90        For lngRow = 1 To lngCount
100           lngBatchStepNumber = Me.BatchStepEventHandler.RowDataDecode("BatchStepNumber", lngRow)
          
              '--Determine if this step will actually execute.
110           bWillExecute = BatchStepWillExecute(BatchStepNumber:=lngBatchStepNumber, ScheduleDate:=Me.txtScheduleDate, StartStepNumber:=Me.txtStartStepNumber, StopStepNumber:=Me.txtStopStepNumber, ForceRunActive:=Me.chkForceRunActive, ForceRunInactive:=Me.chkForceRunInactive)
              
120           If bWillExecute Then
130               Me.BatchStepEventHandler.CellTextSet CellText:="Yes", RowIndex:=lngRow, colname:="Will Run"
140           Else
150               Me.BatchStepEventHandler.CellTextSet CellText:="No", RowIndex:=lngRow, colname:="Will Run"
160               Me.BatchStepEventHandler.FontSetting ForeColor:=eColorConstant.GreyText, RowIndex:=lngRow
170           End If
              
180           If Me.chkHideSkipped And Not bWillExecute Then
190               Me.BatchStepEventHandler.RowHidden IsHidden:=True, Index:=lngRow
200           End If
              
              '--Only add the duration of steps that actually execute.
210           If bWillExecute Then
220               Me.txtEstimatedDurationSeconds = Nz(Me.txtEstimatedDurationSeconds, 0) + Nz(Me.BatchStepEventHandler.RowDataDecode("Avg", lngRow), 0)
230           End If
              
240           bCurrentStepFound = (Nz(DefaultID, "") = Me.BatchStepEventHandler.RowDataDecode("BatchStepID", lngRow))
              
              '--Update the estimated remaining duration.
250           If IsNull(Me.txtBatchStopDate) And (IsNull(DefaultID) Or bCurrentStepFound) And bWillExecute And IsExecuting() Then
260               Me.txtEstimatedRemainingDurationSeconds = Nz(Me.txtEstimatedRemainingDurationSeconds, 0) + Nz(Me.BatchStepEventHandler.RowDataDecode("Avg", lngRow), 0)
270           End If
                     
280       Next

290       Me.txtEstimatedDuration = FormatDuration(Nz(Me.txtEstimatedDurationSeconds, 0))
300       Me.txtEstimatedRemainingDuration = FormatDuration(Me.txtEstimatedRemainingDurationSeconds)
310       SetBatchDuration

          '--Finalize the grid appearence.
320       Me.BatchStepEventHandler.Finalize Cond1Key:="BatchStepID", Cond1Value:=DefaultID
              
          '--Update controls related to the grid.
330       fgBatchStep_UpdateControls

          '--Exit the method successfully.
340       Exit Sub
          
ErrorHandler:
          Dim lngErrNumber As Long
          Dim strErrDescription As String
350       lngErrNumber = Err.Number
360       strErrDescription = Err.Description
370       ErrBox Me.Name, "fgBatchStep_PopulateGrid", strErrDescription, lngErrNumber, Erl, , , False
380       Err.Raise lngErrNumber, , strErrDescription
          
End Sub


Private Sub fgBatchStep_UpdateControls()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          Dim bRowIsValid As Boolean
          
20        bRowIsValid = Me.BatchStepEventHandler.RowIsValid()
          
30        Me.txtStepComment = Null
              
40        If bRowIsValid Then
50            Me.txtBatchStepID = Me.BatchStepEventHandler.RowDataDecode("BatchStepID")
60            Me.txtStepComment = Stuff(vbCrLf & Me.BatchStepEventHandler.RowDataDecode("Comment") & vbCrLf & Me.BatchStepEventHandler.RowDataDecode("Method") & "(" & Nz(Me.BatchStepEventHandler.RowDataDecode("Args"), "") & ")", 1, 2, "")
70        End If
          
80        Me.EventHandler.SetControlState c:=Me.cmdEditStep, Enable:=bRowIsValid And Not IsExecuting(), FocusControl:=Me.tabGeneral
90        Me.EventHandler.SetControlState c:=Me.cmdExecuteStep, Enable:=bRowIsValid And Not IsExecuting() And CheckPermissionSecurity("BatchExecute"), FocusControl:=Me.tabGeneral

          '--Exit the method successfully.
100       Exit Sub
          
ErrorHandler:
          Dim lngErrNumber As Long
          Dim strErrDescription As String
110       lngErrNumber = Err.Number
120       strErrDescription = Err.Description
130       ErrBox Me.Name, "fgBatchStep_UpdateControls", strErrDescription, lngErrNumber, Erl, , , False
140       Err.Raise lngErrNumber, , strErrDescription
          
End Sub


Public Sub SetIsExecuting(ByVal IsExecuting As Boolean)
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

20        Me.txtIsExecuting = IsExecuting
          
30        If IsExecuting Then
40            Me.fraStop.Value = 0
50        Else
60            Me.fraStop.Value = 1
70        End If

          '--Exit the method successfully.
80        Exit Sub
          
ErrorHandler:
          Dim lngErrNumber As Long
          Dim strErrDescription As String
90        lngErrNumber = Err.Number
100       strErrDescription = Err.Description
110       ErrBox Me.Name, "SetIsExecuting", strErrDescription, lngErrNumber, Erl, , , False
120       Err.Raise lngErrNumber, , strErrDescription

End Sub


Public Function IsExecuting() As Boolean
10        IsExecuting = Nz(Me.txtIsExecuting, False)
End Function


Public Sub AddMessage(ByVal Message As String)
10        Me.txtBatchLog = Me.txtBatchLog & Message & vbCrLf
End Sub


Public Sub SetBatchName(ByVal BatchName As Variant)
10        Me.cboBatchName = BatchName
End Sub


Public Sub SetBatchResult(ByVal BatchResult As Variant)
10        Me.txtBatchResult = BatchResult
End Sub


Public Sub SetBatchRunDate(ByVal RunDate As Variant, ByVal StopDate As Variant)
10        Me.txtBatchRunDate = RunDate
20        Me.txtBatchStopDate = StopDate
30        Me.txtBatchStopDuration = Null
          
40        If Not IsNull(Me.txtBatchRunDate) And Not IsNull(Me.txtBatchStopDate) Then
50            Me.txtBatchStopDuration = FormatDuration(DateDiff("s", Me.txtBatchRunDate, Me.txtBatchStopDate))
60        End If
End Sub


Public Sub SetBatchDuration()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

20        Me.txtBatchDuration = Null
30        Me.txtEstimatedStopDate = Null
          
40        If Not IsNull(Me.txtBatchRunDate) Then
50            Me.txtBatchDuration = FormatDuration(DateDiff("s", Me.txtBatchRunDate, Nz(Me.txtBatchStopDate, Now())))
              
60            If Not IsNull(Me.txtEstimatedDurationSeconds) And IsExecuting() Then
70                Me.txtEstimatedStopDate = DateAdd("s", Nz(Me.txtEstimatedRemainingDurationSeconds, 0), Nz(Me.txtBatchStopDate, Now()))
80            End If
90        End If
          
          '--Exit the method successfully.
100       Exit Sub
          
ErrorHandler:
          Dim lngErrNumber As Long
          Dim strErrDescription As String
110       lngErrNumber = Err.Number
120       strErrDescription = Err.Description
130       ErrBox Me.Name, "SetBatchDuration", strErrDescription, lngErrNumber, Erl, , , False
140       Err.Raise lngErrNumber, , strErrDescription
          
End Sub


Public Sub SetBatchEstimatedStopDate(ByVal StopDate As Variant)
10        Me.txtEstimatedStopDate = StopDate
End Sub


Public Sub SetBatchLogID(ByVal BatchLogID As String)
10        Me.txtBatchLogID = BatchLogID
End Sub


Public Sub SetStartStepNumber(ByVal StepNumber As Long)
10        Me.txtStartStepNumber = StepNumber
End Sub


Public Sub SetStopStepNumber(ByVal StepNumber As Variant)
10        Me.txtStopStepNumber = StepNumber
End Sub


Public Sub SetForceRunActive(ByVal ForceRunActive As Boolean)
10        Me.chkForceRunActive = ForceRunActive
End Sub


Public Sub SetForceRuninActive(ByVal ForceRunInactive As Boolean)
10        Me.chkForceRunInactive = ForceRunInactive
End Sub


Public Function IsStopped() As Boolean
10        IsStopped = (Me.fraStop.Value = 1)
End Function


Public Sub ClearControls(Optional ByVal BatchStepID As Variant = Null)
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          '--Reset all values.
20        Me.txtBatchLogID = Null
30        SetBatchResult "Idle"
40        SetBatchRunDate Null, Null
50        Me.txtBatchLog = Null
60        Me.fraStop.Value = 0
70        SetIsExecuting False
          
          '--Refresh the screen so it is in sync.
80        UpdateControls BatchStepID:=BatchStepID
          
          '--Exit the method successfully.
90        Exit Sub
          
ErrorHandler:
          Dim lngErrNumber As Long
          Dim strErrDescription As String
100       lngErrNumber = Err.Number
110       strErrDescription = Err.Description
120       ErrBox Me.Name, "ClearControls", strErrDescription, lngErrNumber, Erl, , , False
130       Err.Raise lngErrNumber, , strErrDescription
          
End Sub


Public Sub UpdateControls(Optional ByVal BatchStepID As Variant = Null, Optional ByVal TabName As String = "")
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

20        FormSetTitle True

          '--General tab.
30        If (TabName = Me.tabGeneral.Name Or TabName = "") Then
40            fgBatchStep_PopulateGrid BatchStepID
50        End If
          
60        Me.EventHandler.SetControlState c:=Me.cmdEditBatch, Enable:=Not IsNull(Me.cboBatchName) And Not IsExecuting()
70        Me.EventHandler.SetControlState c:=Me.cmdNewBatch, Enable:=Not IsExecuting() And CheckPermissionSecurity("BatchUpdate")
80        Me.EventHandler.SetControlState c:=Me.cmdExecute, Enable:=Not IsNull(Me.cboBatchName) And Not IsExecuting() And CheckPermissionSecurity("BatchExecute")
90        Me.EventHandler.SetControlState c:=Me.cmdClear, Enable:=Not IsExecuting()
100       Me.EventHandler.SetControlState c:=Me.cboBatchName, Enable:=Not IsExecuting()
110       Me.EventHandler.SetControlState c:=Me.txtScheduleDate, Enable:=Not IsExecuting()
120       Me.EventHandler.SetControlState c:=Me.cmdSelectScheduleDate, Enable:=Not IsExecuting()
130       Me.EventHandler.SetControlState c:=Me.txtStartStepNumber, Enable:=Not IsExecuting()
140       Me.EventHandler.SetControlState c:=Me.txtStopStepNumber, Enable:=Not IsExecuting()
150       Me.EventHandler.SetControlState c:=Me.chkForceRunActive, Enable:=Not IsExecuting()
160       Me.EventHandler.SetControlState c:=Me.chkForceRunInactive, Enable:=Not IsExecuting()
170       Me.EventHandler.SetControlState c:=Me.txtFilter, Enable:=Not IsExecuting()
          
180       Me.EventHandler.SetControlState c:=Me.cmdBatchStepRefresh, Enable:=Not IsExecuting()
190       Me.EventHandler.SetControlState c:=Me.cmdBatchStepExport, Enable:=Not IsExecuting()
200       Me.EventHandler.SetControlState c:=Me.cmdBatchStepGridSettings, Enable:=Not IsExecuting()
          
          'Me.EventHandler.SetControlState c:=Me.tabHistory, Enable:=Not IsExecuting()
          'Me.EventHandler.SetControlState c:=Me.tabReport, Enable:=Not IsExecuting()
          
          '--History tab.
210       If (TabName = Me.tabHistory.Name Or TabName = "") Then
220           fgBatchLog_PopulateGrid DefaultID:=GtoS(Me.txtBatchLogID.Value)
230           fgBatchStepLog_PopulateGrid DefaultID:=GtoS(Me.txtBatchStepLogID.Value)
240       End If
          
          '--Reports tab.
250       If (TabName = Me.tabReport.Name Or TabName = "") Then
260           UpdateReportControls
270       End If

280       FormSetTitle IsExecuting()
              
          '--Exit the method successfully.
290       Exit Sub
          
ErrorHandler:
300       DoCmd.Hourglass False
          Dim lngErrNumber As Long
          Dim strErrDescription As String
310       lngErrNumber = Err.Number
320       strErrDescription = Err.Description
330       ErrBox Me.Name, "UpdateControls", strErrDescription, lngErrNumber, Erl, , , False
340       Err.Raise lngErrNumber, , strErrDescription

End Sub


Public Sub FormSetTitle(Optional ByVal IsLoading As Boolean = False)
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

          Dim strTitle As String
          
20        If Not IsNull(Me.cboBatchName) Then
30            strTitle = strTitle & " " & Me.cboBatchName
          
40            If Not IsNull(Me.txtScheduleDate) Then
50                strTitle = strTitle & "  " & Format(Me.txtScheduleDate, "mm/dd/yyyy")
60            End If
70        End If
          
          '--Set the title caption.
80        Me.EventHandler.Caption = "Batch Maintenance"
90        Me.EventHandler.FormTitle = strTitle

100       Me.EventHandler.FormSetLoading IsLoading:=IsLoading, EchoOff:=Not IsExecuting()
                      
          '--Exit the method successfully.
110       Exit Sub
          
ErrorHandler:
          Dim lngErrNumber As Long
          Dim strErrDescription As String
120       lngErrNumber = Err.Number
130       strErrDescription = Err.Description
140       ErrBox Me.Name, "FormSetTitle", strErrDescription, lngErrNumber, Erl, , , False
150       Err.Raise lngErrNumber, , strErrDescription
          
End Sub


Private Sub UpdateReportControls()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
20        Me.EventHandler.SetControlState c:=Me.txtEndDate, Enable:=Not IsNull(Me.txtEndDate)
30        Me.EventHandler.SetControlState c:=Me.cmdSelectEndDate, Enable:=Not IsNull(Me.txtEndDate)
40        Me.EventHandler.SetControlState c:=Me.cmdGenerateReport, Enable:=Not IsNull(Me.fraSelectReport) And Not IsNull(Me.fraEndDate)
          
          '--Exit the method successfully.
50        Exit Sub
          
ErrorHandler:
          Dim lngErrNumber As Long
          Dim strErrDescription As String
60        lngErrNumber = Err.Number
70        strErrDescription = Err.Description
80        ErrBox Me.Name, "UpdateReportControls", strErrDescription, lngErrNumber, Erl, , , False
90        Err.Raise lngErrNumber, , strErrDescription
          
End Sub


Private Sub cboBatchName_AfterUpdate()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

20        ClearControls
          ViewC1FlexGrid_Click
          '--Exit the method successfully.
30        Exit Sub

ErrorHandler:
40        ErrBox Me.Name, "cboBatchName_AfterUpdate", Err.Description, Err.Number, Erl
          
End Sub


Private Sub chkForceRunActive_AfterUpdate()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

20        Me.chkForceRunActive.Locked = True
30        ClearControls
40        Me.chkForceRunActive.Locked = False
              
          '--Exit the method successfully.
50        Exit Sub

ErrorHandler:
60        ErrBox Me.Name, "chkForceRunActive_AfterUpdate", Err.Description, Err.Number, Erl
          
End Sub


Private Sub chkForceRunInactive_AfterUpdate()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

20        Me.chkForceRunInactive.Locked = True
30        ClearControls
40        Me.chkForceRunInactive.Locked = False
              
          '--Exit the method successfully.
50        Exit Sub

ErrorHandler:
60        ErrBox Me.Name, "chkForceRunInactive_AfterUpdate", Err.Description, Err.Number, Erl
          
End Sub


Private Sub chkHideSkipped_AfterUpdate()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

          '--Bail out if we are doing something.
20        If IsExecuting() Then Exit Sub

30        Me.chkHideSkipped.Locked = True
40        UpdateControls
50        Me.chkHideSkipped.Locked = False
              
          '--Exit the method successfully.
60        Exit Sub

ErrorHandler:
70        ErrBox Me.Name, "chkHideSkipped_AfterUpdate", Err.Description, Err.Number, Erl
          
End Sub


Private Sub cmdSetGlueComplere_Click()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

          '--Set the focus to a neutral control.
20        Me.EventHandler.SetControlFocus c:=Me.fgBatchLog
          
30        Me.EventHandler.FormSetLoading True

          '--When the batch has to completed manually this will give the user a chance to set the batch glue process to complete
          '--If the glue is not set to complete then notifications will constantly be sent out saying it is still stuck.
40        BatchResetCheckpoint 1
          
50        Me.EventHandler.FormSetLoading False
              
          '--Exit the method successfully.
60        Exit Sub

ErrorHandler:
70        ErrBox Me.Name, "cmdSetGlueComplere_Click", Err.Description, Err.Number, Erl
80        Me.EventHandler.FormSetLoading False

End Sub


Private Sub Form_Close()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
              
          '--Clean up.
20        Set m_oBatchStepEventHandler = Nothing
30        Set m_oBatchLogEventHandler = Nothing
40        Set m_oBatchStepLogEventHandler = Nothing
50        Set m_oFormEventHandler = Nothing
          
          '--Exit the method successfully.
60        Exit Sub

ErrorHandler:
70        ErrBox Me.Name, "Form_Close", Err.Description, Err.Number, Erl
          
End Sub


Private Sub m_oBatchLogEventHandler_GridRefresh()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          '--Bail out if we are doing something.
20        If IsExecuting() Then Exit Sub
          
30        FormSetTitle True
40        UpdateControls BatchStepID:=GtoS(Me.txtBatchStepID.Value), TabName:=Me.tabHistory.Name
50        FormSetTitle False
          
          '--Exit the method successfully.
60        Exit Sub
          
ErrorHandler:
70        ErrBox Me.Name, "m_oBatchLogEventHandler_GridRefresh", Err.Description, Err.Number, Erl
80        FormSetTitle False

End Sub


Private Sub m_oBatchLogEventHandler_GridRowChange()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

          '--Bail out if we are doing something.
20        If IsExecuting() Then Exit Sub

30        fgBatchLog_UpdateControls
40        fgBatchStepLog_PopulateGrid GtoS(Me.txtBatchStepLogID.Value)
          
          '--Exit the method successfully.
50        Exit Sub
          
ErrorHandler:
60        ErrBox Me.Name, "m_oBatchLogEventHandler_GridRowChange", Err.Description, Err.Number, Erl

End Sub


Private Sub m_oBatchStepEventHandler_GridDblClick()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

20        If Me.cmdEditStep.Enabled Then
30            cmdEditStep_Click
40        End If

          '--Exit the method successfully.
50        Exit Sub

ErrorHandler:
60        ErrBox Me.Name, "m_oBatchStepEventHandler_GridDblClick", Err.Description, Err.Number, Erl

End Sub


Private Sub m_oBatchStepEventHandler_GridRefresh()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          '--Bail out if we are doing something.
20        If IsExecuting() Then Exit Sub
          
30        FormSetTitle True
40        UpdateControls BatchStepID:=GtoS(Me.txtBatchStepID.Value), TabName:=Me.tabGeneral.Name
50        FormSetTitle False
          
          '--Exit the method successfully.
60        Exit Sub
          
ErrorHandler:
70        ErrBox Me.Name, "cmdStepRefresh_Click", Err.Description, Err.Number, Erl
80        FormSetTitle False

End Sub


Private Sub m_oBatchStepEventHandler_GridRowChange()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          '--Bail out if we are doing something.
20        If IsExecuting() Then Exit Sub

30        fgBatchStep_UpdateControls
          
          '--Exit the method successfully.
40        Exit Sub
          
ErrorHandler:
50        ErrBox Me.Name, "m_oBatchStepEventHandler_GridRowChange", Err.Description, Err.Number, Erl

End Sub


Private Sub m_oBatchStepLogEventHandler_GridRefresh()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          '--Bail out if we are doing something.
20        If IsExecuting() Then Exit Sub
          
30        FormSetTitle True
40        UpdateControls BatchStepID:=GtoS(Me.txtBatchStepID.Value), TabName:=Me.tabHistory.Name
50        FormSetTitle False
          
          '--Exit the method successfully.
60        Exit Sub
          
ErrorHandler:
70        ErrBox Me.Name, "m_oBatchStepLogEventHandler_GridRefresh", Err.Description, Err.Number, Erl
80        FormSetTitle False

End Sub


Private Sub m_oBatchStepLogEventHandler_GridRowChange()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

          '--Bail out if we are doing something.
20        If IsExecuting() Then Exit Sub

30        fgBatchStepLog_UpdateControls
          
          '--Exit the method successfully.
40        Exit Sub
          
ErrorHandler:
50        ErrBox Me.Name, "m_oBatchStepLogEventHandler_GridRowChange", Err.Description, Err.Number, Erl

End Sub


Private Sub txtFilter_AfterUpdate()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

20        UpdateControls
              
          '--Exit the method successfully.
30        Exit Sub

ErrorHandler:
40        ErrBox Me.Name, "txtFilter_AfterUpdate", Err.Description, Err.Number, Erl

End Sub


Private Sub cmdClear_Click()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          '--Set focus to a neutral control.
20        Me.EventHandler.SetControlFocus c:=Me.fgBatchStep
          
          '--Force reset the start step number.
30        SetStartStepNumber 1
40        SetStopStepNumber Null
50        SetForceRunActive False
60        SetForceRuninActive False
70        Me.txtFilter = Null
          
          '--Clear the screen.
80        ClearControls
          
          '--Exit the method successfully.
90        Exit Sub
          
ErrorHandler:
100       ErrBox Me.Name, "cmdExecute_Click", Err.Description, Err.Number, Erl

End Sub


Private Sub cmdClose_Click()
10        On Error GoTo ErrorHandler

20        DoCmd.Close acForm, Me.Name
              
          '--Exit the method successfully.
30        Exit Sub

ErrorHandler:
40        FormCloseErrBox Me.Name, "cmdClose_Click", Err.Description, Err.Number, Erl
          
End Sub


Private Sub cmdEditBatch_Click()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

          '--Set the focus to a neutral control.
20        Me.EventHandler.SetControlFocus c:=Me.fgBatchStep
30        Me.EventHandler.FormSetLoading True
          
40        Me.txtBatchID = ADOLookup("BatchID", "Batch", "Name=" & q(CleanSQL(Nz(Me.cboBatchName, ""))))
50        BatchOpenForm Textbox:=Me.txtBatchID, DataMode:=acFormEdit

          '--Simply refresh the batch list.
60        Me.cboBatchName.Requery
          
          '--Set focus to a neutral control.
70        Me.EventHandler.SetControlFocus c:=Me.fgBatchStep
80        ClearControls
90        Me.EventHandler.FormSetLoading False
          
          '--Exit the method successfully.
100       Exit Sub

ErrorHandler:
110       ErrBox Me.Name, "cmdEditBatch_Click", Err.Description, Err.Number, Erl
120       Me.EventHandler.FormSetLoading False
          
End Sub


Private Sub cmdEditStep_Click()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

          '--Set the focus to a neutral control.
20        Me.EventHandler.SetControlFocus c:=Me.fgBatchStep
30        Me.EventHandler.FormSetLoading True
40        BatchStepOpenForm Textbox:=Me.txtBatchStepID, DataMode:=acFormEdit, BatchID:=GtoS(Me.txtBatchID.Value)
50        ClearControls GtoS(Me.txtBatchStepID.Value)
60        Me.EventHandler.FormSetLoading False
          
          '--Exit the method successfully.
70        Exit Sub

ErrorHandler:
80        ErrBox Me.Name, "cmdEditBatch_Click", Err.Description, Err.Number, Erl
90        Me.EventHandler.FormSetLoading False
          
End Sub


Private Sub cmdExecute_Click()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
             
          Dim intResult As VbMsgBoxResult
          
20        intResult = MessageBox("Are you sure you wish to execute the entire batch?  This could take a while.", vbQuestion + vbYesNo)
30        If intResult = vbNo Then
40            Exit Sub
50        End If
             
60        DoCmd.Hourglass True
          
          '--Set focus to a neutral control.
70        Me.EventHandler.SetControlFocus c:=Me.fgBatchStep
          
80        BatchRunByDate ScheduleDate:=Me.txtScheduleDate, Name:=Me.cboBatchName, OpenForm:=False, StartStepNumber:=Me.txtStartStepNumber, StopStepNumber:=Me.txtStopStepNumber, ForceRunActive:=Me.chkForceRunActive, ForceRunInactive:=Me.chkForceRunInactive
          
90        DoCmd.Hourglass False
          
100       MessageBox "Batch '" & Me.cboBatchName & "' run for scheduled date '" & Format(Me.txtScheduleDate, "mm/dd/yyyy") & "' finished.", vbInformation
          
          '--Exit the method successfully.
110       Exit Sub
          
ErrorHandler:
120       DoCmd.Hourglass False
130       ErrBox Me.Name, "cmdExecute_Click", Err.Description, Err.Number, Erl

End Sub


Private Sub cmdExecuteStep_Click()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          Dim strBatchStepID As String
          Dim strBatchStepName As String
          Dim lngStepNumber As String
          
20        DoCmd.Hourglass True
          
30        strBatchStepID = GtoS(Me.txtBatchStepID.Value)
40        strBatchStepName = ADOLookup("Name", "BatchStep", "BatchStepID='" & strBatchStepID & "'")
50        lngStepNumber = ADOLookup("StepNumber", "BatchStep", "BatchStepID='" & strBatchStepID & "'")
          
          '--Set focus to a neutral control.
60        Me.EventHandler.SetControlFocus c:=Me.fgBatchStep
          
70        BatchRunByDate ScheduleDate:=Me.txtScheduleDate, Name:=Me.cboBatchName, OpenForm:=False, StartStepNumber:=lngStepNumber, StopStepNumber:=lngStepNumber, ForceRunActive:=True, ForceRunInactive:=True
          
80        DoCmd.Hourglass False
          
90        MessageBox "Batch Step '" & strBatchStepName & "' run for scheduled date '" & Format(Me.txtScheduleDate, "mm/dd/yyyy") & "' finished.", vbInformation
          
          '--Exit the method successfully.
100       Exit Sub
          
ErrorHandler:
110       DoCmd.Hourglass False
120       ErrBox Me.Name, "cmdExecuteStep_Click", Err.Description, Err.Number, Erl

End Sub


Private Sub cmdGenerateReport_Click()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
             
20        Me.EventHandler.FormSetLoading True
             
30        Select Case Me.fraSelectReport.Value
          
              Case 1
40                BatchExecuteScheduleReport BatchName:=Me.cboSelectBatchName, StartDate:=Me.txtStartDate, EndDate:=Me.txtEndDate, EmailTo:=CurrentUserEmailAddress()
              
50            Case 2
60                BatchExecuteLogReport BatchName:=Me.cboSelectBatchName, StartDate:=Me.txtStartDate, EndDate:=Me.txtEndDate, EmailTo:=CurrentUserEmailAddress()
              
70        End Select

80        Me.EventHandler.FormSetLoading False
          
          '--Exit the method successfully.
90        Exit Sub
          
ErrorHandler:
100       ErrBox Me.Name, "cmdGenerateReport_Click", Err.Description, Err.Number, Erl
110       Me.EventHandler.FormSetLoading False
          
End Sub


Private Sub cmdLogReport_Click()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
            
          '--Set the focus to a neutral control.
20        Me.EventHandler.SetControlFocus c:=Me.fgBatchLog
          
30        Me.EventHandler.FormSetLoading True
40        BatchExecuteLogReport BatchLogID:=GtoS(Me.txtBatchLogID.Value), EmailTo:=CurrentUserEmailAddress()
50        Me.EventHandler.FormSetLoading False
          
          '--Exit the method successfully.
60        Exit Sub
          
ErrorHandler:
70        ErrBox Me.Name, "cmdLogReport_Click", Err.Description, Err.Number, Erl
80        Me.EventHandler.FormSetLoading False
          
End Sub


Private Sub cmdNewBatch_Click()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
             
          '--Set the focus to a neutral control.
20        Me.EventHandler.SetControlFocus c:=Me.fgBatchStep
          
30        Me.EventHandler.FormSetLoading True
40        Me.txtBatchID = Null
50        BatchOpenForm Textbox:=Me.txtBatchID, DataMode:=acFormAdd

          '--Simply refresh the batch list.
60        Me.cboBatchName.Requery
70        Me.EventHandler.FormSetLoading False
          
          '--Exit the method successfully.
80        Exit Sub
          
ErrorHandler:
90        ErrBox Me.Name, "cmdNewBatch_Click", Err.Description, Err.Number, Erl
100       Me.EventHandler.FormSetLoading False

End Sub


Private Sub cmdResumeNextBatchLog_Click()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          Dim strName As String
          Dim dtScheduleDate As Date
          Dim lngLastStepExecuted As Long
          Dim varStopStepNumber As Variant
          Dim bForceRunActive As Boolean
          Dim bForceRunInactive As Boolean
              
20        DoCmd.Hourglass True
                  
30        strName = Me.BatchLogEventHandler.RowDataDecode("Name")
40        lngLastStepExecuted = CLng(Me.BatchLogEventHandler.RowDataDecode("Last #"))
50        varStopStepNumber = Sn(Me.BatchLogEventHandler.RowDataDecode("Stop #"))
60        dtScheduleDate = CDate(Me.BatchLogEventHandler.RowDataDecode("Schedule Date"))
70        bForceRunActive = CBool(Me.BatchLogEventHandler.RowDataDecode("ForceRunActive"))
80        bForceRunInactive = CBool(Me.BatchLogEventHandler.RowDataDecode("ForceRunInactive"))
                  
          '--Clear the current screen.
90        cmdClear_Click
                  
100       BatchRunByDate ScheduleDate:=dtScheduleDate, Name:=strName, OpenForm:=False, StartStepNumber:=lngLastStepExecuted + 1, StopStepNumber:=varStopStepNumber, ForceRunActive:=bForceRunActive, ForceRunInactive:=bForceRunInactive
              
110       DoCmd.Hourglass False
120       MessageBox "Batch '" & strName & "' run for scheduled date '" & Format(dtScheduleDate, "mm/dd/yyyy") & "' finished.", vbInformation
          
          '--Exit the method successfully.
130       Exit Sub
          
ErrorHandler:
140       DoCmd.Hourglass False
150       ErrBox Me.Name, "cmdResumeNextBatchLog_Click", Err.Description, Err.Number, Erl

End Sub


Private Sub cmdRetryBatchLog_Click()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
          
          Dim strName As String
          Dim dtScheduleDate As Date
          Dim lngLastStepExecuted As Long
          Dim varStopStepNumber As Variant
          Dim bForceRunActive As Boolean
          Dim bForceRunInactive As Boolean
       
20        DoCmd.Hourglass True
          
30        strName = Me.BatchLogEventHandler.RowDataDecode("Name")
40        lngLastStepExecuted = CLng(Me.BatchLogEventHandler.RowDataDecode("Last #"))
50        varStopStepNumber = Sn(Me.BatchLogEventHandler.RowDataDecode("Stop #"))
60        dtScheduleDate = CDate(Me.BatchLogEventHandler.RowDataDecode("Schedule Date"))
70        bForceRunActive = CBool(Me.BatchLogEventHandler.RowDataDecode("ForceRunActive"))
80        bForceRunInactive = CBool(Me.BatchLogEventHandler.RowDataDecode("ForceRunInactive"))
          
          '--Clear the current screen.
90        cmdClear_Click
          
100       BatchRunByDate ScheduleDate:=dtScheduleDate, Name:=strName, OpenForm:=False, StartStepNumber:=lngLastStepExecuted, StopStepNumber:=varStopStepNumber, ForceRunActive:=bForceRunActive, ForceRunInactive:=bForceRunInactive
              
110       DoCmd.Hourglass False
120       MessageBox "Batch '" & strName & "' run for scheduled date '" & Format(dtScheduleDate, "mm/dd/yyyy") & "' finished.", vbInformation
          
          '--Exit the method successfully.
130       Exit Sub
          
ErrorHandler:
140       DoCmd.Hourglass False
150       ErrBox Me.Name, "cmdRetryBatchLog_Click", Err.Description, Err.Number, Erl

End Sub


Private Sub cmdSelectEndDate_Click()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
                  
20        If DatePicker(Textbox:=Me.txtEndDate, EventHandler:=Me.EventHandler, Caption:="End Date", AllowNull:=False) = vbOK Then
             '--Do nothing special.
30        End If
          
          '--Exit the method successfully.
40        Exit Sub

ErrorHandler:
50        ErrBox Me.Name, "cmdSelectEndDate_Click", Err.Description, Err.Number, Erl
          
End Sub


Private Sub cmdSelectScheduleDate_Click()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
                  
20        If DatePicker(Textbox:=Me.txtScheduleDate, EventHandler:=Me.EventHandler, Caption:="Schedule Date", AllowNull:=False) = vbOK Then
30            txtScheduleDate_AfterUpdate
40        End If
          
          '--Exit the method successfully.
50        Exit Sub

ErrorHandler:
60        ErrBox Me.Name, "cmdSelectScheduleDate_Click", Err.Description, Err.Number, Erl
          
End Sub


Private Sub cmdSelectStartDate_Click()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler
                  
20        If DatePicker(Textbox:=Me.txtStartDate, EventHandler:=Me.EventHandler, Caption:="Schedule Date", AllowNull:=False) = vbOK Then
             '--Do nothing special.
30        End If
          
          '--Exit the method successfully.
40        Exit Sub

ErrorHandler:
50        ErrBox Me.Name, "cmdSelectStartDate_Click", Err.Description, Err.Number, Erl
          
End Sub


Private Sub Form_Open(Cancel As Integer)
10        On Error GoTo ErrorHandler

20        EventHandler.Load
          
          '--Set focus to a neutral control.
30        Me.EventHandler.SetControlFocus c:=Me.fgBatchStep
40        ClearControls
              
          '--Exit the method successfully.
50        Exit Sub

ErrorHandler:
60        ErrBox Me.Name, "Form_Open", Err.Description, Err.Number, Erl

End Sub


Private Sub fraEndDate_AfterUpdate()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

20        If Me.fraEndDate = 1 Then
30            Me.txtEndDate = Date
40        Else
50            Me.txtEndDate = Null
60        End If
          
70        UpdateReportControls
          
          '--Exit the method successfully.
80        Exit Sub

ErrorHandler:
90        ErrBox Me.Name, "fraEndDate_AfterUpdate", Err.Description, Err.Number, Erl
          
End Sub


Private Sub txtBatchLog_Enter()
10        Me.txtBatchLog.SelLength = 0
End Sub


Private Sub txtScheduleDate_AfterUpdate()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

20        ClearControls
              
          '--Exit the method successfully.
30        Exit Sub

ErrorHandler:
40        ErrBox Me.Name, "txtScheduleDate_AfterUpdate", Err.Description, Err.Number, Erl
          
End Sub


Private Sub txtStartStepNumber_AfterUpdate()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

20        ClearControls
              
          '--Exit the method successfully.
30        Exit Sub

ErrorHandler:
40        ErrBox Me.Name, "txtStartStepNumber_AfterUpdate", Err.Description, Err.Number, Erl

End Sub


Private Sub txtStopStepNumber_AfterUpdate()
10        If ErrorHandlerEnable() Then On Error GoTo ErrorHandler

20        ClearControls
              
          '--Exit the method successfully.
30        Exit Sub

ErrorHandler:
40        ErrBox Me.Name, "txtStopStepNumber_AfterUpdate", Err.Description, Err.Number, Erl

End Sub

Private Sub ViewC1FlexGrid_Click()
' Create an instance of FlexGridWrapper
    Dim flexGridWrapper As Object
    Set flexGridWrapper = CreateObject("C1FlexGridInterOp.FlexGridWrapper")
    
    ' Clear the grid
  
    
    ' Define the SQL query
    Dim strSQL As String
    strSQL = "SELECT " & _
             "StepNumberDetail AS [Step #], " & _
             "Name AS [Step Name], " & _
             "Owner AS [Owner], " & _
             "Avg AS [Avg], " & _
             "StDev AS [StDev], " & _
             "Runs AS [Runs], " & _
             "NULL AS [Will Run], " & _
             "Duration AS [Duration], " & _
             "Result AS [Result] " & _
             "FROM dbo.fBatchStepSearch(NULL) " & _
             "WHERE BatchName = 'RealTime'"
    
    ' Fetch data from the database
    Dim oDAL As New DAL
    Dim rs As ADODB.Recordset
    Debug.Print strSQL
    oDAL.GetRecordsetOTD SQLQuery:=strSQL, Recordset:=rs
    ' Set the number of columns and rows
    flexGridWrapper.Cols = rs.Fields.Count
    flexGridWrapper.Rows = rs.RecordCount + 1 ' +1 for the header row
    
    ' Set the column names
    Dim i As Integer
    For i = 0 To rs.Fields.Count - 1
        flexGridWrapper.SetCellValue 0, i, rs.Fields(i).Name
    Next i
    
    ' Populate the grid with data
    Dim row As Integer
    row = 1
    Do While Not rs.EOF
        For i = 0 To rs.Fields.Count - 1
            flexGridWrapper.SetCellValue row, i, rs.Fields(i).Value
        Next i
        rs.MoveNext
        row = row + 1
    Loop
    
    ' Display the grid
    flexGridWrapper.ShowGrid
End Sub
 
Public Sub flexGridWrapper_CellChanged()
    ' Open a different form in MS Access
    DoCmd.OpenForm "frmBatchDialog"
End Sub
 
Public Sub flexGridWrapper_Click()
    ' Open a different form in MS Access
    DoCmd.OpenForm "frmBatchDialog"
End Sub

Private Sub flexGridWrapper_RowDoubleClick(ByVal rowData As String)
    DoCmd.OpenForm "frmBatchDialog", acNormal
    Forms("frmBatchDialog").Controls("txtRowData").Value = rowData
End Sub
