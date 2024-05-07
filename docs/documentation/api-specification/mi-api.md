---
layout: default
title: MI API
parent: API Specification
grand_parent: Documentation
nav_order: 12
---

# MI API
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---
**Since**: `0.9.0`  
**Context**: XtendM3 API  
**Category**: Context Specific
---

## Description
The MI API allows for reading input parameters and writing output parameters sent into transactions. Supports both single and multiple transactions, custom logic and validation. It can for example be used to abort transaction based on own validation. Also Provides error management.

## Features

### mi.inData
This method allows to get value from data container and containes In values for Transactions. Contained values are already converted to the proper type.
Takes string key as a parameter and returns value associeted with the key.

Example:
```groovy
private String whlo;
private String itno;
private String whsl;

public void main() {
  whlo = mi.inData.get("WHLO").trim();
  itno = mi.inData.get("ITNO").trim();
  whsl = mi.inData.get("WHSL").trim();
}
```

### mi.outData
This method id used to put value into the data container. Takes key and associeted with it value as parameters, returns previous value.

Example 1:
```groovy
public void main() {
  mi.outData.put("WHLO", "This is WHLO Output#1 for QQS001")
  mi.outData.put("ITNO", "This is ITNO Output#2 for QQS002")
  mi.write() 
}
```
<br>

Example 2:
```groovy
void main() {
  mi.outData.put("WHLO", String.valueOf(getParameter("NUMB", Double.class).orElse(null)))
}
```
### mi.getDateFormat
This method checks date format and returns it as a string. Possible formats: YMD8, YMD6, MDY6, DMY6, YWD5.

Example:
```groovy
void main() {
  if (mi.getDateFormat(date1) == "YMD8"){
    return LocalDate.now().format(DateTimeFormatter.ofPattern("yyyyMMdd"))
  }
}
```
### mi.getDateSeparator
Returns date separator as a char.

Example:
```groovy
void ReadSeparator () {
sep = mi.getDateSeparator() 
String oldDate = '04-DEC-2012'
Date date = Date.parse( 'dd-MMM-yyyy', oldDate )
String newDate = date.format( 'M'{sep}'d'{sep}'yyyy' )
}
```

### mi.getRemainingRecords
Returns the amount of remaining records allowed to be written in current transaction.

Example:
```groovy
void main() {
  if (mi.getRemainingRecords()>= 2) {
    for (int i = 0; i < 3; i++) {
      mi.outData.put("WHLS" + WHSL)
      mi.write()
    }
  }
}
```

### mi.hasRemainingRecords
Returns true if there are still any additional records to be written or false if not.

Example:
```groovy
void main() {
  if (mi.hasRemainingRecords()){
    mi.outData.put("MMRI" + SLDQ)
    mi.write()
  }
}
```

### mi.getMaxRecords
Returns the maximum amount of possible records to be written as an integer.

Example:
```groovy
public void main() {
  String WHSL = mi.in.get("WHSL")
  for (int i = 0; i < mi.getMaxRecords(); i++) {
    mi.outData.put("WHLS" + WHSL + " " + i)
    mi.write()
  }
}
```

### mi.write
Writes response from data present in outData. Clears all values in outData.

Example:
```groovy
public void main() {
  mi.outData.put("WHLO","Test")
  mi.write()
}
```

### mi.error
Writes error responses with parameters:<br>
String errorMessage - error message to display (only supports 240 characters);<br>
String field - field error occurred for (only supports 10 characters);<br>
String errorCode - code for error which occurred (only supports 2 characters).

Example 1:
```groovy
boolean validateInput() {
  if (!getWarehouse(whlo)){
    mi.error("Warehouse " + whlo + " is invalid");
    return false;
  } else {
    return true;
  }
}
```
<br>

Example 2:
```groovy
boolean validResponsible(String Responsible) {
  if (Responsible.isEmpty()){
    mi.error("Responsible must be entered");
    return false;
  } else {
    return true;
  }
}
```
## Considerations and Guidelines
It is not possible to add extra fields to transactions using MI API.
