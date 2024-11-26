---
layout: default
title: Exceptions
parent: Documentation
nav_order: 5
---

# Exceptions
{: .no_toc }

How to work with exceptions in XtendM3
{: .fs-6 .fw-300 }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Exception capabilities
In the context of XtendM3, it is possible to catch generic Java exceptions listed below in the Generic Java Exceptions section.<br/>
It is not possible to throw generic Java exceptions, these can only be caught. <br>
Some XtendM3 specific exceptions exists, that can be thrown either via API calls or via Groovy throw().<br>
Documentation on the XtendM3 exceptions API are available in the [API Specification/ExceptionAPI](../api-specification/exception-api) section under Documentation.

### Generic Java Exceptions
The following exceptions can be caught in XtendM3 code:<br/>

**Runtime exceptions**
- *NumberFormatException*
- *DateTimeException*

**Compile time check**
- *FileUtilException*
- *Specific Java exceptions*
  - E.g., java.IOException
  - Essentially, any exception within a package beggining with *.java* can be caught

#### Example code
Example code for catching generic Java exceptions.<br/>

*NumberFormatException*
```groovy 
String aString = null;
Integer anInteger = 10;
try {
  anInteger = aString.toInteger(); 
} catch(NumberFormatException e) {
  logger.debug("Execution failed with exception: ${e}");
}
```

*DateTimeException*
```groovy
String aString = null;
try {
  aString = LocalDate.now().format(DateTimeFormatter.ofPattern("yyMMdd")); 
} catch(DateTimeException e) {
  logger.debug("Execution failed with exception: ${e}");
}
```

*FileUtilException*
```groovy
String fileSize = null;
try {
  fileSize = textFiles.size("fileName"); 
} catch(FileUtilsException e) {
  logger.debug("Execution failed with exception: ${e}");
}
```

### XtendM3 Specific Exceptions
Three levels of XtendM3 exceptions are available to throw. Interactive, MI Response and Adaptive. Detailed documentation and example code on each available exception are available in the [API Specification/ExceptionAPI](../api-specification/exception-api) section under Documentation. API documentation is also available in the API documentation section in the XtendM3 app in H5.

**Interactive exceptions**<br/>
Interactive exceptions can be thrown from Trigger extension contexts. Most of the available XtendM3 specific exceptions are interactive exceptions, ranging from *ShowOkDialogException* to *ShowErrorException*.

**MI Response exceptions**<br/>
MI response exceptions are used for throwing exceptions from Transaction contexts, to indicate write errors.<br/> 
Can be thrown with just a *String message*. Alternatively a *String field* and *String errorCode* can be added for additional verbosity.  

**Adaptive exception**<br/>
The *AdaptiveShortCircuitException* can be thrown from any given XtendM3 context and will adapt to the context of execution.