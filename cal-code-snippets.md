Statusfenster / Progress Window
OnPreDataItem (DataItem muß fortschrittsrelevant sein)
QtyRecs := Recordref.COUNT;
ProgressWindow.OPEN := TCActRec;

OnAfterGetRecord
ActRunRecs += 1;
ProgressWindow.UPDATE(1,Record.Field);
ProgressWindow.UPDATE(2,ROUND(9999 / QtyRecs * ActRunRecs,1));

VAR
QtyRecs_lDec : Decimal
ActRunRecs_lDec : Decimal
ProgressWindow_lDec : Dialog

TextConstants
TCActRec :	Aktueller Datensatz: #1############### \ @2@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
	Actual Record: #1############### \ @2@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@

Zurück zur Übersicht

Aufrufen eines Reports aus einem Form durch ein Menu Item (mit Filterübergabe)
Form
Menu Button / Command Button
Menu Items
CaptionML vergeben (!ein „&“ vor einem Buchstaben bewirkt eine Alt+Button Weiterleitung!)
C/AL Code

On Push ()
RecordVariable.SETRANGE(Field,Value//from\\),Value//to\\);
RecordVariable.SETRANGE(Field,Value//from\\),Value//to\\);
REPORT.RUN(REPORT::ReportCaption,TRUE,TRUE,RecordVariable);

VAR
RecordVariable : Record 		Subtype : gewünschte Table

Zurück zur Übersicht

Abfrage und Ausgabe von Filtern aus dem Requestform
C/AL Code des „nackten“ Reports

OnPreReport
FiltersUsed := DataItem.GETFILTERS;
IF FiltersUsed = ’’ THEN
  FiltersUsed := TCNoFilters;

Ausgabe der Filter in einer Textbox in den Sections bzw. der ersten Section / dem ersten Header

VAR
FiltersUsed : Text	Length : 255 od. kleiner

TextConstants
TCDataItem/RecordFilters : DEU = DataItem / Record – Filter: ENU = DataItem / Record – Filters:
TCNoFilters : DEU = Keine Filter gesetzt. ENU = No filters in use.

Zurück zur Übersicht

Excel Export
Benötigt Tabelle: Excel Buffer

Es sollte immer auswählbar sein, ob ein Export nach Excel gewünscht wird.

Folgende Funktion wird benötigt:

EnterCell(
RowNo : Integer;
ColumnNo : Integer;
CellValue : Text[50];
Bold : Boolean;
Italic : Boolean;
UnderLine : Boolean)
TempExcelBuffer.INIT;
TempExcelBuffer.VALIDATE("Row No.",RowNo);
TempExcelBuffer.VALIDATE("Column No.",ColumnNo);
TempExcelBuffer."Cell Value as Text" := CellValue;
TempExcelBuffer.Formula := '';
TempExcelBuffer.Bold := Bold;
TempExcelBuffer.Italic := Italic;
TempExcelBuffer.Underline := UnderLine;
TempExcelBuffer.INSERT;

Sie wird z. B. als eigenständige Funktion in einem Report angelegt und so oft aufgerufen wie Werte nach Excel übergeben werden.

Zur Initialisierung des Excel-Exports sollte folgender Code verwendet werden.

TempExcelBuffer.DELETEALL;
CLEAR(TempExcelBuffer);
TempExcelBuffer.SetAutoFit(TRUE);

Um das Excel-Sheet zu erzeugen wird unten stehender Code benötigt.

OnPostReport()
//-EXC
//Creating a new ExcelSheet
IF Export THEN BEGIN
  TempExcelBuffer.CreateBook;
  TempExcelBuffer.CreateSheet('Lizenzabrechnung','',COMPANYNAME,USERID);
  TempExcelBuffer.GiveUserControl;
  CLEAR(TempExcelBuffer);
END;
//+EXC

VAR
TempExcelBuffer : Record : Excel Buffer

Ein Seitenumbruch funktioniert folgendermaßen:

TempExcelBuffer.Comment := 'Pagebreak';
Zurück zur Übersicht

Order auswählen (Dateisystem)
Siehe auch Codeunit 412
Bemerkungen:
Der Filterstring für die auswählbaren Dateien muss folgende Struktur aufweisen:
<Anzeigestring>|<Filterausdruck>|<Anzeigestring>|<Filterausdruck>|…
Bsp.: XML-Dateien (*.xml)|*.xml|Alle Dateien (*.*)|*.*


Name   DataType   Subtype   Length 
ShellControl   Automation   'Microsoft Shell Controls And Automation'.Shell    
Folder   Automation   'Microsoft Shell Controls And Automation'.Folder3    
FolderItems   Automation   'Microsoft Shell Controls And Automation'.FolderItems3    
FolderItem   Automation   'Microsoft Shell Controls And Automation'.FolderItem2    
Foldertxt   Text      1024 

if isclear(ShellControl) then 
  create(ShellControl); 

Folder := ShellControl.BrowseForFolder(0, 'Ordner auswählen', 0); 
FolderItems := Folder.Items(); 
FolderItem := FolderItems.Item; 
Foldertxt := format(FolderItem.Path); 
clear(ShellControl); 


ActiveWindow Automation 'CSideWindowCheck'.WindowCheck
or (if you don't have that Automation)
ActiveWindow Automation 'C/SIDE Utility Classes'.ActiveWindow

Code:
IF ISCLEAR(ShellControl) THEN
  CREATE(ShellControl);
IF ISCLEAR(ActiveWindow) THEN
  CREATE(ActiveWindow);

EXIT(FORMAT(ShellControl.BrowseForFolder(ActiveWindow.WindowHandle,Text000,0).Items().Item.Path));

When using codeunit 412, be sure to deploy Comdlg.oca and Comdlg32.ocx along with client installations (and register it too).

Zurück zur Übersicht

Test

Was ist die beabsichtigte Aussage?
Funktioniert das?

Country.SETFILTER(Name, '@'+'%1', 'DEUTSCHLAND');
Resultierender Filterstring: '@'
Country.SETFILTER(Name, '@%1', 'DEUTSCHLAND');
Resultierender Filterstring: '@'

Der String wird nicht richtig umgesetzt!
Dies kann mit STRSUBSTNO umgangen werden:
Country.SETFILTER(Name, STRSUBSTNO('@%1', 'DEUTSCHLAND'));

Country.SETFILTER(Name, '<>%1', 'DEUTSCHLAND'); funktioniert

Zurück zur Übersicht

Statusfenster mit rotierendem Balken (-\|/)
Stick_lTxt := '-\|/';
IF GUIALLOWED THEN BEGIN
  Progress_lDlg.OPEN(Dialog0001_lCtx);
END;


Cycle_lInt += 1;
IF Cycle_lInt > 4 THEN BEGIN
  Cycle_lInt := 1;
END;
Progress_lDlg.UPDATE(1, Stick_lTxt[Cycle_lInt]);



Modified Haken im Objektdesigner entfernen
clear(Object_lRec);
Object_lRec.setfilter(Type, '<>TableData&<>System&<>FieldNumber');
Object_lRec.setrange(Modified, true);
if Object_lRec.findset then begin
  repeat
    SkipThis_lBln := false;

    if Object_lRec.Type = Object_lRec.Type::Codeunit then begin
      if Object_lRec.ID in [5001998] then begin
        SkipThis_lBln := true;
      end;
    end;

    if not SkipThis_lBln then begin
      Object_lRec.Modified := false;
      Object_lRec.modify(false);
    end;
  until Object_lRec.next = 0;
end;


message('Job done!')

Zurück zur Übersicht

Windows Standarddialog: Ordnerauswahl
WindowsShell_gAut	Automation	'Microsoft Shell Controls And Automation'.Shell
Folder_gAut	Automation	'Microsoft Shell Controls And Automation'.Folder
OutputFilePath_gTxt	Text		1024

(Dieser Eintrag last sich leider nicht einfach kopieren. Die Variable muss selbst herausgesucht oder aus einem anderen Objekt kopiert werden. Dazu ist z.B. der Report zur Datenmigration aus der Function Library geeignet.)


IF ISCLEAR(WindowsShell_gAut) THEN BEGIN
  IF NOT CREATE(WindowsShell_gAut) THEN BEGIN
    EXIT;
  END;
END;

CLEAR(Folder_gAut);
Folder_gAut := WindowsShell_gAut.BrowseForFolder(0,'Output',0);
IF NOT ISCLEAR(Folder_gAut) THEN BEGIN
  OutputFilePath_gTxt := Folder_gAut.Items().Item.Path;
END;

 

Zurück zur Übersicht


DOS Shell aus Navision (ohne lästiges Nachfragen durch das System)
Variable: lAutShell	Automation	'Windows Script Host Object Model'.WshShell	

gFncDosShell(Command_lTxt : Text[1024]; WindowStyle_lInt : Integer; WaitForEndOfCommand_lBln : Boolean) ReturnValue_lInt : Integer
// DosShell 
//*** 109 
// Works like SHELL and HYPERLINK of Navision but without the annoying confirmation-request of Navision 4.00
//   (this function uses automation 'Windows Script Host Object Model'.WshShell)
// PARAMETERS:
//   ItxtCommand : command + parameters for the SHELL
//   IintWindowStyle : Type of window for command
//     0:Hides the window and activates another window. (=Doesn't even show a window, to kill the doscommand,
//             you have to do it with taskmanager=>Processes)
//     1:Activates and displays a window. If the window is minimized or maximized, the system restores
//       it to its original size and position. An application should specify this flag when displaying
//       the window for the first time. (=shows the window as normal window)
//     2:Activates the window and displays it as a minimized window. (=shows as minimized)
//     3:Activates the window and displays it as a maximized window. (=shows as maximized)
//     4:Displays a window in its most recent size and position. The active window remains active.
//     5:Activates the window and displays it in its current size and position.
//     6:Minimizes the specified window and activates the next top-level window in the Z order.
//     7:Displays the window as a minimized window. The active window remains active.
//     8:Displays the window in its current state. The active window remains active.
//     9:Activates and displays the window. If the window is minimized or maximized, the system
//       restores it to its original size and position. An application should specify this flag
//       when restoring a minimized window.
//     10:Sets the show-state based on the state of the program that started the application.
//   IblnWaitForEndOfCommand : TRUE : wait for the command to finish (and use the RETURN-VALUE)
//                             FALSE: Launch the command and return (RETURN-VALUE will be 0)
//   RETURN-VALUE : SHELL-command RETURN-VALUE

CREATE(lAutShell);
lIntReturnValue := lAutShell.Run(Command_lTxt, WindowStyle_lInt, WaitForEndOfCommand_lBln);
CLEAR(lAutShell);

Zurück zur Übersicht

Navision Nummernserie
Codeunit 396 NoSeriesManagement

Zentrale Funktion:
InitSeries
Param:

Var	Name	DataType	Subtype	Length
Nein	DefaultNoSeriesCode	Code		10
// Nummernseriencode aus Einrichtung
Nein	OldNoSeriesCode	Code		10
// xRec."No. Series" – Wird verwendet um zu speichern aus welcher Nummernserie die Nummer entnommen wurde.
Nein	NewDate	Date		
// z.B. Buchungsdatum – Wird wahrscheinlich verwendet um in der Nummernserie „Letztes Datum verwendet“ anzugeben
Ja	NewNo	Code		20
// Referenzparameter, normalerweise das Feld im Dokument in dem die neue Nummer aus der Nummernserie landet
Ja	NewNoSeriesCode	Code		10
// Referenzparameter, wieder Rec.“No. Series“; Wird verwendet um zu speichern aus welcher Nummernserie die Nummer entnommen wurde.

NoSeriesMgt.GetNextNo
Wird z.B. in der Codeunit 80 verwendet.

Es gibt “Sales Header”.“Shipping No. Series“, das schon auf der Ebene des Sales Headers den Nummernseriencode für die Lieferungen bereithält.
In der Codeunit 80 wird damit gearbeitet. “Sales Header”."Shipping No." bekommt in der C80 über NoSeriesMgt.GetNextNo("Shipping No. Series","Posting Date",TRUE) z.B. die nächste Lieferscheinnummer zugewiesen.
Zurück zur Übersicht

Eigene Controls auf der Dataport RequestForm platzieren

Startet man in Navision einen Dataport, so stehen einem die Felder Dateiname und Import standardmäßig zur Verfügung.
Schaut man sich die RequestForm jedoch im Designer an, so findet man diese Controls nicht.
Der Grund ist, dass Navision diese Felder automatisch einblendet, solange sich keine Controls auf der RequestForm befinden.
Fügt man jedoch ein Control (z. B. eine TextBox) dort ein, so werden beim Aufruf des Dataports die Felder Dateiname und Import nicht mehr angezeigt.

Um diese Felder wieder anzuzeigen, einfach folgende Anleitung befolgen:

   1. Eine neue globale Variable Dateiname vom Typ Text[250] anlegen
   2. Eine neue globale Variable Richtung vom Typ Option mit OptionString Import,Export anlegen
   3. Eine TextBox inkl. Label hinzufügen
      Properties:
          * Control-ID: 1
          * AssistEdit: Ja
          * SourceExpression: Dateiname
          * Parent Control ID des dazugehörigen Labels: 1
   4. Eine weitere TextBox inkl. Label hinzufügen
      Properties:
          * SourceExpression: Richtung
   5. Im Trigger OnPreDataport folgenden Code einfügen:

      Code: Alles auswählen
          CurrDataport.Filename := Dateiname;
          CurrDataport.Import := (Richtung = Richtung::Import);


Nun ist die Voraussetzung geschaffen, dass man weitere Controls auf der RequestForm platzieren kann und dennoch den Dateinamen und die Richtung auswählen kann.

Quelle: http://www.msdynamics.de/viewtopic.php?f=17&t=1274&start=0&hilit=dataport+control+id


Umgang mit Nummernserien

Zentrale Codeunit: "NoSeriesManagement"
Datei Öffnen/Speichern Dialog
Siehe Codeunit 412 Common Dialog Management
