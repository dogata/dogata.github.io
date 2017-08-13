---
layout: post
title: "Classifying GTD dataset with AdaBoost"
date: 2017-07-15 12:00:00 -0700
category: machine-learning
---

An attempt to use AdaBoost to classify the success of terrorist attacks documented in the [Global Terrorism Database (GTD)](https://www.start.umd.edu/gtd/).  The GTD is compiled and maintained by the University of Maryland START program

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

GraphLab has been used to create the dataframe. The column hints for the `graphlab.read_csv_with_errors()` function are:
```python
col_list=[str,int,int,int,str,int,str, # GTD ID and date
          int,str,int,str,str,str,float,float,int,int,str, # incident location
          str,int,int,int,int,int,str,int, # incident information
          int,int,int,str,int,str,int,str, # attack information
          int,str,int,str,str,str,int,str, # victim information 1
          int,str,int,str,str,str,int,str, # victim information 2
          int,str,int,str,str,str,int,str, # victim information 3
          str,str,str,str,str,int,int,int,str,str,int,int,int,int,int,int,int,str,int,int,str,int,int,str,int, # perpetrator information
          int,str,int,str,int,str,int,str,int,str,int,str,int,str,int,str,str, # weapon information
          int,int,int,int,int,int,int,int,str,float,str,int,int,int,int,int,str,str,str,float,float,float,float,str,int,str,int, # casualties and consequences
          str,str,str,str,str,int,int,int,int,str # additional information
         ]
```

The column hints are used to guess the data type in each column.
- Incomplete lines will be stored in `bad_lines`.
- The last column that supplies the additional information has been read with a `str` type since it contains multiple event IDs separated by commas (`,`).
```python
(gtd_data,bad_lines) = gl.SFrame.read_csv_with_errors(fpath,delimiter='\t', verbose=False,column_type_hints=col_list)
```

Column names that have been read in are:
```
['eventid', 'iyear', 'imonth', 'iday', 'approxdate', 'extended', 'resolution', 'country', 'country_txt', 'region', 'region_txt', 'provstate', 'city', 'latitude', 'longitude', 'specificity', 'vicinity', 'location', 'summary', 'crit1', 'crit2', 'crit3', 'doubtterr', 'alternative', 'alternative_txt', 'multiple', 'success', 'suicide', 'attacktype1', 'attacktype1_txt', 'attacktype2', 'attacktype2_txt', 'attacktype3', 'attacktype3_txt', 'targtype1', 'targtype1_txt', 'targsubtype1', 'targsubtype1_txt', 'corp1', 'target1', 'natlty1', 'natlty1_txt', 'targtype2', 'targtype2_txt', 'targsubtype2', 'targsubtype2_txt', 'corp2', 'target2', 'natlty2', 'natlty2_txt', 'targtype3', 'targtype3_txt', 'targsubtype3', 'targsubtype3_txt', 'corp3', 'target3', 'natlty3', 'natlty3_txt', 'gname', 'gsubname', 'gname2', 'gsubname2', 'gname3', 'ingroup', 'ingroup2', 'ingroup3', 'gsubname3', 'motive', 'guncertain1', 'guncertain2', 'guncertain3', 'nperps', 'nperpcap', 'claimed', 'claimmode', 'claimmode_txt', 'claim2', 'claimmode2', 'claimmode2_txt', 'claim3', 'claimmode3', 'claimmode3_txt', 'compclaim', 'weaptype1', 'weaptype1_txt', 'weapsubtype1', 'weapsubtype1_txt', 'weaptype2', 'weaptype2_txt', 'weapsubtype2', 'weapsubtype2_txt', 'weaptype3', 'weaptype3_txt', 'weapsubtype3', 'weapsubtype3_txt', 'weaptype4', 'weaptype4_txt', 'weapsubtype4', 'weapsubtype4_txt', 'weapdetail', 'nkill', 'nkillus', 'nkillter', 'nwound', 'nwoundus', 'nwoundte', 'property', 'propextent', 'propextent_txt', 'propvalue', 'propcomment', 'ishostkid', 'nhostkid', 'nhostkidus', 'nhours', 'ndays', 'divert', 'kidhijcountry', 'ransom', 'ransomamt', 'ransomamtus', 'ransompaid', 'ransompaidus', 'ransomnote', 'hostkidoutcome', 'hostkidoutcome_txt', 'nreleased', 'addnotes', 'scite1', 'scite2', 'scite3', 'dbsource', 'INT_LOG', 'INT_IDEO', 'INT_MISC', 'INT_ANY', 'related']
```

## Feature selection
As an exploratory exercise for classification, only certain features were selected to build the decision tree.  Some notable features are:
- `country`: the country where the attack took place in
- `suicide`: if it was a suicide attack
- `weaptype1`: type of weapons used
- `targtype1`: type of target

```python
features = ['country','suicide','crit1','crit2','crit3','attacktype1','weaptype1','targtype1']
```

The label is then the success of the attack.
```python
target = 'success'
```

## Subsampling
Due to the low number of attack successes, the training data needs to have approximately equal number of successes and failures.  As explained by the START team, the low number of successes of terrorist attacks do not match the perceived terrorist threat as being common.  This is confirmed by calculating the percentage of successes and failures.
```
Percentage of success                 : 0.0963883856811
Percentage of failure                 : 0.903611614319
```

After subsampling the data, this reduces the entire dataset from 156772 events to 30330 events for the training data.  This number is reduced further for the training data since some data needs to be retained as the test data.  In this exercise, the training data is about 80% of the subsampled dataset.

## Classification

### AdaBoost
After learning with AdaBoost, the classification error only decreased to about 34%.
![](/assets/adaboost_classification_error.png)

The first split is by target type (20 = Unknown). The second split is by the attack type (6 = Explosives/Bombs/Dynamites).

### Gradient boosted trees
With GraphLab's gradient boosted tree, the accuracy is about 72% for the training data.
![](/assets/gradient_boosted_tree.png)

Also, the most frequent value is the attach type (6 = Explosives/Bombs/Dynamites).
