#
Chapter IX: IO

##I/O Streams

* Byte Streams handle I/O of raw binary data.
* Character Streams handle I/O of character data, automatically handling translation to and from the local character set.
* Buffered Streams optimize input and output by reducing the number of calls to the native API.
* Scanning and Formatting allows a program to read and write formatted text.
* I/O from the Command Line describes the Standard Streams and the Console object.
* Data Streams handle binary I/O of primitive data type and String values.
* Object Streams handle binary I/O of objects.


## File I/O (Featuring NIO.2)

* What is a Path? examines the concept of a path on a file system.
* The Path Class introduces the cornerstone class of the java.nio.file package.
* Path Operations looks at methods in the Path class that deal with syntactic operations.
File Operations introduces concepts common to many of the file I/O methods.
* Checking a File or Directory shows how to check a file's existence and its level of accessibility.
* Deleting a File or Directory.
* Copying a File or Directory.
* Moving a File or Directory.
* Managing Metadata explains how to read and set file attributes.
* Reading, Writing and Creating Files shows the stream and channel methods for reading and writing files.
* Random Access Files shows how to read or write files in a non-sequentially manner.
* Creating and Reading Directories covers API specific to directories, such as how to list a directory's contents.
* Links, Symbolic or Otherwise covers issues specific to symbolic and hard links.
* Walking the File Tree demonstrates how to recursively visit each file and directory in a file tree.
* Finding Files shows how to search for files using pattern matching.
* Watching a Directory for Changes shows how to use the watch service to detect files that are added, removed or updated in one or more directories.
* Other Useful Methods covers important API that didn't fit elsewhere in the lesson.
* Legacy File I/O Code shows how to leverage Path functionality if you have older code using the java.io.File class. A table mapping java.io.File API to java.nio.file API is provided.
