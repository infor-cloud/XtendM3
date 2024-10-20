---
layout: default
title: TextFiles API
parent: API Specification
grand_parent: Documentation
nav_order: 9
---
 
# TextFiles API
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---
**Since**: `0.11.0`  
**Context**: Any  
**Category**: Storage  
---

## Description
TextFiles API is used to perform various file processing operations. It can be used to open, read and write in files from current and subdirectories as well as list existing folders and files.
 
## Features
### textFiles.open
This method allows the user to open a sub directory. This can be used when trying to access a file located in another location. Takes name of the sub directory as a parameter.
<br>
Example:
 
```groovy
public void openRecords() {
  textFiles.open(“nameOfSubDirectory”);
	/*
  After applying this method, file access is changed. 
  Now the extension is already in the subDirectory folder, so file operations only affect files in the active subDirectory. 
  */
  textFiles.read("TEXTFILE.txt", "UTF-8", updateTEXTFILE);	
}
```

### textFiles.read
This method is responsible for reading data from the selected file and applying it via the corresponding command using the bufferedReader in the extension.
Parameters:
- String fileName - name of the file<br>
- String encoding - string representation of the encoding<br>
- Consumer<BufferedReader> readTask - consumer accepting BufferedReader to read from file. BufferedReader is a class which simplifies reading text from a character input stream. It buffers the characters in order to enable efficient reading of text data.
<br>

Example:
 
```groovy
List<String> resolveFields(String line) {
  List<String> list = new ArrayList<>();
  String[] fields = line.split(resolveDelimiter(line));
  for(String field : fields) {
    list.add(field);
  }
  return list;
}
Closure<?> readCODEFILE = { BufferedReader reader ->
  List<String> header = resolveFields(reader.readLine());
  while((line = reader.readLine()) != null) {
    reader.println(line);
  }
}
public void readRecords() {
  textFiles.read("CODEFILE.csv", "UTF-16", readCODEFILE);
}
```
 
### textFiles.write
This method allows data to be written inside the selected file.
Parameters:
- String fileName - name of the file<br>
- String encoding - string representation of the encoding<br>
- boolean append - if true, append to the contents, otherwise (re)create the file<br>
- Consumer<PrintWriter> writeTask - consumer accepting PrintWriter to print to file. PrintWriter class is the implementation of Writer class. It is used to print the formatted representation of objects to the text-output stream.<br>

Example:
 
```groovy
void log(String message) {
  message = LocalDateTime.now().toString() + " " + XtendM3;
  Closure<?> consumer = { PrintWriter printWriter ->
    printWriter.println(message)M
  }
  textFiles.write(reportName, "UTF-16", true, consumer);
}
```

### textFiles.delete
This method deletes the selected file. It takes the file name as parameter, in String format.
<br>
Example:
 
```groovy
public void DeleteExample() {
  textFiles.delete("test.txt");
}
```
 
### textFiles.size
This method allows to obtain information about the size of the selected file. The size is displayed as a number of the long type and the units are bytes.
<br>
Example:
 
```groovy 
public void SizeExample() {
  String fileName="test.txt";
  interactive.showOkDialog("Selected file has size of "+textFiles.size(fileName).toString()+"units...");
}
```
### textFiles.exists
The method checks if the file is available in the extension file location. A boolean true/false value is returned depending on available files.
<br>
Example:
 
```groovy 
public void ExistExample() {
  if(textFiles.exists("test.txt")) {
    interactive.showOkDialog("Hello! The file test.txt does exist!");
  } else {
    interactive.showWarningDialog("Hello! The file test.txt does not exist!");
}
```
 
### textFiles.listFiles
The method lists all files at the extension directory.
<br>
Example:
 
```groovy
public void ListFiles() {
  textFiles.open("config_data");
  StringBuilder sb = new StringBuilder();
  Iterator<String> list = textFiles.listFiles();

  for (String element : list) {
    sb.append(element);
    sb.append(" ");
  }
}
```
 
### textFiles.listFolders
The method lists all folders at the extension directory.
<br>
Example:
 
```groovy
public void ListFolders() {
  textFiles.open("config_data");
  StringBuilder sb = new StringBuilder();
  Iterator<String> list = textFiles.listFolders();
      
  for (String element : list) {
    sb.append(element);
    sb.append(" ");
  }
}
```
 
 
## Considerations and Guidelines
TextFile API can be used in [Trigger](../../../examples/example-003) and [Transaction](../../../examples/Transaction-extension) type extensions.

