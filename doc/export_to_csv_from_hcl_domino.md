Below is a **clear, production-grade explanation of how to export data from an HCL Domino NSF database to CSV**, covering **all practical approaches**, from no-code to automation. This is aligned with enterprise Domino practices.

---

## 1. Using a Domino Agent (Recommended, No External Tools)

### When to use

* You **do not want a Notes client dependency**
* Need **repeatable / scheduled exports**
* Need **server-side automation**
* Want **control over data selection and format**

### Approach

Create a **LotusScript agent** that:

1. Opens a view
2. Iterates documents
3. Writes CSV using `Print #` or `NotesStream`

### Sample LotusScript Agent (CSV Export)

```lotusscript
Option Public
Option Declare

Sub Initialize
    Dim session As New NotesSession
    Dim db As NotesDatabase
    Dim view As NotesView
    Dim doc As NotesDocument
    Dim stream As NotesStream
    Dim filePath As String

    Set db = session.CurrentDatabase
    Set view = db.GetView("AllIssues")
    Set doc = view.GetFirstDocument()

    filePath = "c:\temp\issues.csv"
    Set stream = session.CreateStream
    Call stream.Open(filePath, "UTF-8")

    ' Header
    Call stream.WriteText("IssueID,Title,Status,Priority,AssignedTo" & Chr(13) & Chr(10))

    While Not doc Is Nothing
        Call stream.WriteText( _
            doc.GetItemValue("IssueID")(0) & "," & _
            doc.GetItemValue("Title")(0) & "," & _
            doc.GetItemValue("Status")(0) & "," & _
            doc.GetItemValue("Priority")(0) & "," & _
            doc.GetItemValue("AssignedTo")(0) & Chr(13) & Chr(10))

        Set doc = view.GetNextDocument(doc)
    Wend

    Call stream.Close
End Sub
```

### Pros

* Full control
* Works on server
* No client dependency

### Cons

* Requires LotusScript knowledge

---

## 2. Using `notes.ini` + `nexport` (Not Recommended)

### Why it’s discouraged

* `nexport` is **deprecated**
* Limited formatting control
* Not supported in modern Domino versions

Use only for legacy environments.

---

## 3. Using DXL Export + CSV Conversion

### When to use

* You already export design/data using DXL
* CI/CD pipeline
* Git-based workflow

### Steps

```bash
dxlexport issues.nsf data.xml -o
```

Then convert XML → CSV using:

* Python
* XSLT
* PowerShell

### Pros

* CI-friendly
* Complete data access

### Cons

* Requires post-processing

---

## 4. Using Domino REST API (Modern & Scalable)

### When to use

* Domino 12+ / 14+
* External systems (BI tools, ETL)
* Cloud integration

### Example REST Call

```http
GET /api/data/documents?form=IssueForm
```

### Convert JSON → CSV

Using Python:

```python
import csv, json
```

### Pros

* No Domino scripting
* Secure
* Scalable

### Cons

* REST configuration required

---

## 5. Using Notes Client UI (Manual)

### Steps

1. Open View
2. File → Export
3. Choose CSV

### Pros

* Quick
* No coding

### Cons

* Manual
* Not repeatable
* Client required

---

## Recommended Strategy (Decision Matrix)

| Use Case             | Best Option  |
| -------------------- | ------------ |
| One-time export      | Notes Client |
| Scheduled automation | Agent        |
| CI / Git pipeline    | DXL          |
| External analytics   | REST         |
| Legacy system        | neXport      |

---

## Best Practices

* Always export from **views**, not raw database
* Normalize multi-value fields
* Escape commas and quotes
* Use UTF-8 encoding
* Log export metadata
