---
layout: post
title: "GTD classification"
category: machine-learning
---

# Classifying GTD dataset with AdaBoost
- The [Global Terrorism Database (GTD)](https://www.start.umd.edu/gtd/) is compiled by the University of Maryland START program

## Import data from `xlsx` file
- Data types are obtained from `codebook.pdf`
- A total of 137 fields or features are documented in the database.
- A VBA script has been used to replace all carriage returns.
- The database has been exported to tab-delimited form.

The VBS script to remove carriage return has been adopted from an online [VBA script](https://www.ablebits.com/office-addins-blog/2013/12/03/remove-carriage-returns-excel/)
```vb
'Script to remove carriage returns. Carriage returns prevent proper import
Sub RemoveCarriageReturns()
    Dim MyRange As Range
    Application.ScreenUpdating = False
    Application.Calculation = xlCalculationManual

    For Each MyRange In ActiveSheet.UsedRange
        'Linux and OSX use vbLf for a carriage return
        If 0 < InStr(MyRange, vbLf) Then
            MyRange = Replace(MyRange, vbLf, " ")
        End If
        'Windows uses vbCrLf for a carriage return
        If 0 < InStr(MyRange, vbCrLf) Then
            MyRange = Replace(MyRange, vbCrLf, " ")
        End If
        'Remove possible carriage returns
        If 0 < InStr(MyRange, vbCr) Then
            MyRange = Replace(MyRange, vbCr, " ")
        End If
        'Remove extra characters (possible escape character)
        If 0 < InStr(MyRange, "\") Then
            MyRange = Replace(MyRange, "\", " ")
        End If
    Next

    Application.ScreenUpdating = True
    Application.Calculation = xlCalculationAutomatic
End Sub
```

## Feature selection

## Classification
