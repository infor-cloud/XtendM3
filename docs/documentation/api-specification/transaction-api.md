---
layout: default
title: Transaction API
parent: API Specification
grand_parent: Documentation
nav_order: 13
---

# Extension API
{: .no_toc }

Checklist for requesting approval for your extensions.
{: .fs-6 .fw-300 }
![](/assets/images/warning-24px.svg) **️IN PROGRESS, NOT COMPLETE YET**

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

**Since**: `0.12.0`  
**Context**: API  
**Category**: Context specific  
---
## Description
Transaction API allows to extend MI transactions. Can be used to set, retrieve or change a parameter and skip current transaction.
## Features

### Parameters
For retieving and changing MI transaction buffer or data structure
Example 1:
```groovy
public void main() {
    String SLRORN = transaction.parameters.SLRORN
    String SLRSCD = transaction.parameters.OAORNO
    if (SLRORN.isBlank() || SLRSCD.isBlank()) {
      return
    }
```
Example 2:
```groovy
void preFetch(DBContainer OSLOST){
    String POSX = program.LDAZZ.POSX
    OSLOST.setInt("SLRORX", POSX.isInteger() ? POSX as Integer : 0)
    OSLOST.set("SLRORX", transaction.parameters.ORNO)
  }
```
### Skip the current transaction
Example:
```groovy
if (!getWarehouse(whlo)){
      mi.error("Warehouse " + whlo + " does not exist");
      transaction.abortTransaction()
    }
```

## Considerations and Guidelines