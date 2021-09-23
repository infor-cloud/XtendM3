---
layout: default
title: MICaller API
parent: API Specification
grand_parent: Documentation
nav_order: 5
---

# MiCaller API
{: .no_toc }

{: .fs-6 .fw-300 }
![](/assets/images/warning-24px.svg)

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

**Since**: `0.1.0`  
**Context**: Any  
**Category**: Integration  
---
## Description
This API can be used for MI calls in XtendM3

## Features
### MI Call
There are 2 kinds of MI Call methods to be used: one with parameters and one without

#### 1. Without parameters <br>
Example:
```groovy
public class TestProgram extends ExtendM3Trigger {
  private final ProgramAPI program
  private final ExtensionAPI extension
  private final InteractiveAPI interactive
  private final MICallerAPI miCaller
  private final LoggerAPI logger
  
  public TestProgram(ProgramAPI program, ExtensionAPI extension, InteractiveAPI interactive, MICallerAPI miCaller, LoggerAPI logger) {
    this.program = program
    this.extension = extension
    this.interactive = interactive
    this.miCaller = miCaller
    this.logger = logger
  }
  
  public void main() {
    String customer = null
    def callback = {
    Map<String, String> response ->
      logger.info("Response = ${response}")
      if(response.CUNO != null){
        customer = response.CUNO  
      }
    }
    
    miCaller.call("CRS610MI","LstBySearchKey", callback)
    
    interactive.showOkDialog("Customer: " + customer)
  }
}
```

#### 2. With parameters <br>
Example:
```groovy
public class TestProgram extends ExtendM3Trigger {
  private final ProgramAPI program
  private final ExtensionAPI extension
  private final InteractiveAPI interactive
  private final MICallerAPI miCaller
  private final LoggerAPI logger
  
  public TestProgram(ProgramAPI program, ExtensionAPI extension, InteractiveAPI interactive, MICallerAPI miCaller, LoggerAPI logger) {
    this.program = program
    this.extension = extension
    this.interactive = interactive
    this.miCaller = miCaller
    this.logger = logger
  }
  
  public void main() {
    String DSPname = interactive.display.fields.WRCUNM.toString();
    String DSPaddress = interactive.display.fields.WRCUA1.toString();
    String DSPpostalcode = interactive.display.fields.WRPONO.toString();
    String DSPtown = interactive.display.fields.WRTOWN.toString();
    
    def params = [ "SQRY":"CUNM:${DSPname}~ AND CUA1:${DSPaddress} AND PONO:${DSPpostalcode} AND TOWN:{DSPtown}".toString() ] // toString is needed to convert from gstring to string
    String customer = null
    def callback = {
    Map<String, String> response ->
      logger.info("Response = ${response}")
      if(response.CUNO != null){
        customer = response.CUNO  
      }
    }
    
    miCaller.call("CRS610MI","SearchCustomer", params, callback)
    
    interactive.showOkDialog("Customer: " + customer)    
  }
}  
```

### Set List Max Records
This method sets the maximum amount of records to be returned by the MI Caller method when using a list transaction. <br>
Example:
```groovy
public class TestProgram extends ExtendM3Trigger {
  private final ProgramAPI program
  private final ExtensionAPI extension
  private final InteractiveAPI interactive
  private final MICallerAPI miCaller
  private final LoggerAPI logger
  
  public TestProgram(ProgramAPI program, ExtensionAPI extension, InteractiveAPI interactive, MICallerAPI miCaller, LoggerAPI logger) {
    this.program = program
    this.extension = extension
    this.interactive = interactive
    this.miCaller = miCaller
    this.logger = logger
  }
  
  public void main() {
    String DSPname = interactive.display.fields.WRCUNM.toString();
    String DSPaddress = interactive.display.fields.WRCUA1.toString();
    String DSPpostalcode = interactive.display.fields.WRPONO.toString();
    String DSPtown = interactive.display.fields.WRTOWN.toString();
    
    def params = [ "SQRY":"CUNM:${DSPname}~ AND CUA1:${DSPaddress} AND PONO:${DSPpostalcode} AND TOWN:{DSPtown}".toString() ] // toString is needed to convert from gstring to string
    String customer = null
    def callback = {
    Map<String, String> response ->
      logger.info("Response = ${response}")
      if(response.CUNO != null){
        customer = response.CUNO  
      }
    }

    miCaller.setListMaxRecords(1)
    miCaller.call("CRS610MI","SearchCustomer", params, callback)
    
    interactive.showOkDialog("Customer: " + customer)    
  }
}  
```

### Set Date Format
The setDateFormat method uses two parameters: character separator and date format. These are the allowed date formats: YMD8, YMD6, MDY6, DMY6, and YWD5. <br>
Example:
```groovy
public class TestProgram extends ExtendM3Trigger {
  private final ProgramAPI program
  private final ExtensionAPI extension
  private final InteractiveAPI interactive
  private final MICallerAPI miCaller
  private final LoggerAPI logger
  
  public TestProgram(ProgramAPI program, ExtensionAPI extension, InteractiveAPI interactive, MICallerAPI miCaller, LoggerAPI logger) {
    this.program = program
    this.extension = extension
    this.interactive = interactive
    this.miCaller = miCaller
    this.logger = logger
  }
  
  public void main() {
    if (program.getUser() != "CRIUBA36") {
      return 
    }
    String DSPname = interactive.display.fields.WRCUNM.toString();
    String DSPaddress = interactive.display.fields.WRCUA1.toString();
    String DSPpostalcode = interactive.display.fields.WRPONO.toString();
    String DSPtown = interactive.display.fields.WRTOWN.toString();
    
    def params = [ "SQRY":"CUNM:${DSPname}~ AND CUA1:${DSPaddress} AND PONO:${DSPpostalcode} AND TOWN:{DSPtown}".toString() ] // toString is needed to convert from gstring to string
    String customer = null
    def callback = {
    Map<String, String> response ->
      logger.info("Response = ${response}")
      if(response.CUNO != null){
        customer = response.CUNO  
      }
    }
    
    miCaller.setDateFormat("/","YMD8")
    miCaller.call("CRS610MI","SearchCustomer", params, callback)
    
    interactive.showOkDialog("Customer: " + customer)
  }
}  
```


## Considerations and Guidelines