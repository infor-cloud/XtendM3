---
layout: default
title: Batch Extension
parent: Examples
nav_order: 1
---
 
# Batch Extension
{: .no_toc }
 
Working with Batch Extension in XtendM3
{: .fs-6 .fw-300 }
 
## Table of contents
{: .no_toc .text-delta }
 
1. TOC
{:toc}
 
---
 
## Description
Batch extensions use standard functionality in M3 Business Logic for long-running jobs or scheduled jobs triggered from another extension. It is a custom Batch program using API for job calls that are running in the background. It is possible to retrieve UUID data from Batch operations using [Batch API](../../../docs/documentation/api-specification/batch-api), which can also be used inside Batch extensions.
 
## Use cases
* Used to run transactions that require no user interaction
* Can be scheduled on a regular basis for execution
* Suitable for long-running jobs
 
## Step by step implementation
 
### 1. Open XtendM3 tool
 
After opening M3 main page, select Menu in the top left corner. Then select XtendM3 from the Administration Tools folder.
 
<img src="../../../assets/attachments/ex004/open_xtendm3.png" width="950" >
 
### 2. Create a new extension
 
To create a new extension select the "Create a new extension" button.
 
<img src="../../../assets/attachments/ex004/create_extention_button.png" width="950">
 
### 3. Extension Type as a Batch extension

Opened window displays an option to select the type of extension to be created. Select “Batch” and then click “Next”.
 
<img src="../../../assets/attachments/ex-batch/type.png" width="950">
 
### 4. Create extension name

Input extension name in format "EXTNNN" where N is an integer e.g., EXT001. Then input a description of the program. Lastly, select the "Create" button.
 
<img src="../../../assets/attachments/ex-batch/name_batch.png" width="950">
 
### 5. Skeleton of the extension

Created extension will contain a default code template.
 
<img src="../../../assets/attachments/ex-batch/template.png" width="950">
 
Over the code field there are several tools with which you can operate on the designed extension.
 
<img src="../../../assets/attachments/ex-batch/tool.png" width="950">
 
Listing them from the left side they are used to:
 
* Enable editing mode
* Export the code
* Extra settings to change the parameters of the extension or enable it etc.
* Delete the extension
* Information about the extension
* Test compile
* Refresh
 
### 6. Code example of transaction extension and batch execution
In the example to perform batch extension usage there will be a need to use another type of extension - Transaction extension.<br><br>
The example can be broken down into five points:
- Creation of batch extension and its code implementation
- Creation of transaction extension and its code implementation
- Future logging configuration
- Initialization of the transaction extension that has the code implementation to call the batch extension
- Reading the result file
 <br>

<b> Creation of batch extension and its code implementation </b>
<br>
It is the same procedure as presented above. At the point of implementing functionality, the batch extension example will include basic operations using [Logger API](../../../docs/documentation/api-specification/logger-api), just to see inputted result in the log file.
<br>
Batch extension example:

```groovy
public class EXT002 extends ExtendM3Batch {
  private final LoggerAPI logger
  private final BatchAPI batch
  
  public EXT002(LoggerAPI logger, BatchAPI batch) {
    this.logger = logger;
    this.batch = batch;
  }
  
  public void main() { 
    logger.info("Testing EXT002 - executing code - This is the first line of the batch extension");
    logger.info("Still testing - This is the second line of the batch extension with Uuid-gen:" + batch.getReferenceId().get());
    logger.info("Line no. 2 uses a Batch API to generate/retireve job UUID number");
  }
}
```
After implementing functionality it needs to be saved. 
<br>

<b> Creation of transaction extension and its code implementation </b>
<br>
To create a transaction extension check the link [here](../../../docs/examples/Transaction-extension). It is very similar to the batch. At the point of implementing functionality there will be some code that is using MICaller API for executing very important extension named SHS010MI with Batch scenario extension point, which is responsible for executing Batch extensions.

```groovy
public class batchTest extends ExtendM3Transaction {
  private final MIAPI mi;
  private final MICallerAPI miCaller;
  private final DatabaseAPI database;
  private final LoggerAPI logger;
  
  public batchTest(MIAPI mi, MICallerAPI miCaller, DatabaseAPI database, LoggerAPI logger) {
    this.mi = mi;
    this.miCaller = miCaller;
    this.database = database;
    this.logger = logger;
  }
  
  public void main() {
    logger.info("Running EXT919MI")  // Extra log line for testing whether everything is working properly
    testSHS010MI()
  }
  /*
   *  SHS010MI is a program which has a method for executing and/or scheduling Batch extensions named SchedXM3Job - without calling it - the Batch extension won't be executed.
   *  It has several mandatory parameters to call the extension point of it, although depending on what scheduling you want to do:
   *  - TX30 - description for the execution or scheduling job (ex. which transaction is being executed - here EXT919MI)
   *  - XCAT - job schedule category, depends on the category data available in SHS050
   *  - SCTY - job schedule type. 1 - single execution. 2 - Repetitive. If XNOW is 1, SCTY should be 1. If XNOW is 0, SCTY should be 2
   *  - XNOW - execute now. This parameter should be set to 1 only if you wish to execute the job ones, now. Otherwise this parameter should be set to zero. There are another similar parameters for execution at at specific day and time. As well as scheduling recurring executions. 
   *  - JOB  - name of Batch extension to be executed
   *  - UUID - UUID number. Can be fetched using Batch API. See example above
   */
  void testSHS010MI() {
    logger.info("Testing SHS010MI");
    Closure<?> callback = {Map<String, String> result ->
      logger.info("Result is: ${result}");
    }
    Map<String, String> params = ["TX30": "CallingFromEXT919MI", 
        "XCAT": "010".toString(), 
        "SCTY": "1".toString(), 
        "XNOW": "1".toString(), 
        "JOB": "EXT000".toString(), 
        "UUID": "1eedcd6d-5cb2-43ab-a55b-487658c38f88".toString()];
    // This method is calling SHS010MI to execute with inputted parameters. More info about MICaller API is available in the doc
    miCaller.call("SHS010MI", "SchedXM3Job", params, callback);
  }
}
```
 

To finish, save the written extension by clicking the “Save” button and activate it inside the settings.
<br>

<b> Future logging configuration </b>
<br>
It is very important to execute this procedure <b>after</b> writing and saving the code in transaction extension. 
<br>
Logging configuration is possible to create inside the Business Engine Jobs.
Inside the page there is a table and above it there are two options:
- Kill Job - it is used for stopping Batch in the background
- Logging - it is a drop-down box, which has three elements inside it:
    - view log - to have a small preview of chosen log
    - configure logging - to change some options inside chosen log
    - <b>configure future logging</b> - to create a new log job
<br>


<img src="../../../assets/attachments/ex-batch/sc1.PNG" width="950">
<br>
The third option allows user to configure new logging job, which will be connected to the chosen Batch extension.
Inside opened window for "configure future logging" there will be a small pop-up window with some options and a table inside it. The table allows user to 'Add', 'Edit', 'Remove' logging jobs or 'Refresh' them.


<img src="../../../assets/attachments/ex-batch/sc2.PNG" width="950">
<br>
To create a new logging job for new Batch extension, just click the 'Add' option. Following window should pop-up:


<img src="../../../assets/attachments/ex-batch/sc3.PNG" width="950">
<br>

<img src="../../../assets/attachments/ex-batch/sc4.PNG" width="950">
<br>
Basic things to input for executing Batch extension are the program name - which should be exact same as previously created Batch extension, and to see the result into log file, it is needed to make a check inside the 'Log to file' box. Another significant field is 'Number of jobs to start with log configuration' where user can choose, how many times it is possible to execute batch wit the logging job result. The number can be chosen from 1 to 5.
<br>

<b>Other options of that window depends on user needs.</b><br>
The last thing to do is to save the logging job. After that new Batch extension can be executed.

<b>Initialization of the transaction extension that has the code implementation to call the batch extension</b><br>
To test the Batch extension output open MetaData Publisher and select "Test API" button from "M3 API Repository". Then in the left menu select created Transaction extension as the "Program" and method for Batch testing. Then select "Run". In this example as shown below.

<img src="../../../assets/attachments/ex-batch/Meta.PNG" width="950">

<b>Reading the result file</b><br>
Extension execution log should now be displayed in the Business Engine Log. To read Batch output select the log and download is as a text file by clicking top left button "Download selected log file".

<img src="../../../assets/attachments/ex-batch/Log.PNG" width="950">

If executed correctly the output will be visible inside the file.<br>
Example: <br>

 <img src="../../../assets/attachments/ex-batch/txtfile.PNG" width="950">
 
## Important notes
* Whenever a user updates the code of an extension through which a batch extension is called, it is necessary to create a new log job related to that batch. Otherwise the batch will not be executed.
* Always test the examples for your own solution before using them in production
* It is a good practice to use a test compilation of the program before running
* Data presented in the examples is random
 
## Exported Extension
- [BatchExtension.json](../../../assets/attachments/ex-batch/BATCH-EXT002.json)
- [TransactionExtension.json](../../../assets/attachments/ex-batch/TRANSACTION-EXT919MI-batchTest.json)
 
## See Also
[More examples](../../../docs/examples)<br>
[API documentation](../../../docs/documentation/api-specification)<br>
[Batch API](../../../docs/documentation/api-specification/batch-api)<br>
 
 
 
 
 
 
 
 

