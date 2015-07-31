#CHAPTER 7 - File FOR future reference

###Simple input and output using InFile, OutFile and PrintFile

###Storing and retrieving information

So far we have been limited in what our programs can do by the need to read everything in from the terminal or batch input stream and write everything out to the terminal or batch printer. This means that our SIMULA programs have not been able to use information already held on the computer or to leave information on the computer for other programs to use. This chapter will show how SIMULA provides very powerful mechanisms for this purpose.
You are probably used to the fact that computers keep permanent information in collections called files. Some systems use other names such as data sets, but they are essentially the same thing. These files have names by which you can identify them to the computer. Programs can read from these collections of information and write to them.

SIMULA has objects called Files as well. When you want to read from or write to a file on your computer, you must use a SIMULA File object to stand for the external file and tell the computer which external file you want. The exact way that this works may vary slightly from one computer to another, but the important points are the same.

In fact a SIMULA File can stand for any source of or destination for information. A printer can also be written to by using a File object to represent it in your programs. A magnetic tape reader can be used as a source of input in the same way.

In fact you have already been using two File objects without being told that that was what you were doing. These are the standard input File, SysIn, and the standard output File, SysOut. Whenever you have used InInt, OutImage and any other input/output instructions you have been using File attributes.

###Simple input

To read information from the computer we normally use a type of File object known as an InFile. In fact InFile is a sub-class of the object type or class called File. This means that all the properties of File are properties of InFile or are redefined in InFile, but that InFile has some extra ones of its own. In fact all types of File objects are sub-classes of File. InFile is not a direct sub-class of File, however; there is another level between them, called ImageFile.
Put more simply, class File defines a type of object with a number of attributes used to access sources of and destinations for information on a computer, such as files, printers, terminals and tape readers.

File class ImageFile is a sub-class of File. It has all the attributes of its parent class File, some of which it redefines, and in addition some extra attributes used to handle information in certain ways.

ImageFile class InFile is a sub-class of ImageFile. It has all the attributes of both File and ImageFile plus extra ones for reading information using the ways suited to ImageFile's attributes.

This probably sounds far from simple on first reading, but the idea of thinking of objects as classes and sub-classes is central to SIMULA and so we use it to describe formally the relationships of the various sub-types of File.

Example 7.1 is a program using an InFile to provide its information. Notice the familiar names used for the same purposes, but now prefixed with a File name.

Example 7.1: Simple input using InFile.

        begin

           ref (InFile) Inf;
           text T1;

           Inf :- new InFile("MYRECORDS");
           Inf.Open(Blanks(80));
           Inf.InImage;
           T :- Inf.Image;
           OutText(T);
           OutImage;
           OutInt(Inf.InInt);
           OutImage;
           Inf.Close
        end
There are a few new concepts in this program. Let us look at them one by one.
Firstly, we have a new type of declaration. It declares Inf to be a ref variable, but has the word InFile in parentheses between the keyword ref and the identifier Inf. A ref variable is a pointer to an object of a complex type. The class which defines that type is given in parentheses after the keyword ref. Thus Inf is a location which can be used to hold a pointer to an object which is of type InFile. It is initially pointed at an imaginary object called None, just as text variables initially reference NoText.

Inf is pointed at a real object by making a reference assignment to it, using the reference assignment operator, :-. Value assignments are not possible since Inf can only hold a pointer to an object, not the object itself.

In our program Inf is pointed at a new object of the required type by using an object generator as the right hand side of a reference assignment to it. The statement

      Inf :- new InFile("MYRECORDS")
thus creates a new InFile type object and points Inf at it. When an object is created in this way one or more parameters may be passed to it, depending on its type.
InFile demands a text value parameter. In fact this parameter is demanded by the grandparent class File and must be given to all new objects whose type is a sub-class of File. This parameter provides the link between the SIMULA InFile object and the file or device on the actual computer which it will represent inside the program. The exact way in which this text is interpreted on any particular computer and its meaning to that computer varies considerably and you should consult the Users' Guide or Programmer's Reference Manual for the system you are using.

In principle the statement is saying that an InFile object which is initially pointed at (or referenced) by Inf will be used in this program to represent some source of input on the computer where the program is to run. The string "MYRECORDS" identifies to that computer the actual source to be used in a way which that computer can understand.

Having created the InFile object the program then prepares it for use by calling the procedure Open, which is an attribute of InFile and so is specified by the prefix "Inf.". This also takes a parameter, in this case a text reference parameter, which must reference a variable text frame. This text frame will be used to hold the input read from the source. The parameter is reference assigned to a text attribute of ImageFile called Image.

Most of the rest of the program should be familiar, except for the prefixing of InImage and InInt by Inf. This tells the SIMULA system to read from the source associated with Inf rather than the standard input, as in our previous programs.

The final statement calls the procedure Close, to tell the SIMULA system that this File is not required until Open is called for it again.

####Simple output

Corresponding to InFile, but used for output of information, is the class OutFile. This is also a sub-class of ImageFile.
Example 7.2 is a simple example of the use of OutFile. It corresponds to the InFile example fairly closely and so no further explanation is given yet.

Example 7.2: Simple output using OutFile.

        begin

           ref (OutFile) Outf;

           Outf :- new OutFile("MYSTORAGE");
           Outf.Open(Blanks(132));

           Outf.OutText("This goes to a File");
           Outf.OutImage;
           Outf.OutInt(43,3);
           Outf.OutImage;
           Outf.Close
        end
###Exercises

The information you have about output to files should now be sufficient to allow some simple attempts at reading and writing to files. This is mainly intended to familiarise you with the way in which your particular computer and SIMULA system treat the File objects in SIMULA programs. You should read the documentation for your system carefully at this point.
7.1 Write a program which prints the integer values 1, 2, 3 and 4 to an OutFile. Check what sort of File your system has created on the computer. See if you are able to examine its contents and list them to a printer.

7.2 Write a program which reads the contents of the File produced in 7.1, using an InFile, and writes it to the terminal or batch output stream.

###ImageFile class InFile

Having seen informally how input and output are handled in SIMULA, let us now consider three of the four sub-classes of ImageFile and what they do, starting with InFile. We shall not consider all the attributes of class InFile here. Some will be dealt with in {{ book.Chapter8 }}. The remaining sub-class will be dealt with in {{ book.Chapter15 }}.
###Image

All ImageFile derived File objects use a text in their reading or writing. This represents the current input/output line or record. Files using this mechanism are called "record oriented". This means that a whole record is read into the Image text or written from it at a time. Most attributes of an ImageFile object operate on the copy in Image, not the actual File or device it stands for.
Image is a text variable within the ImageFile object. In an InFile, the parameter passed to Open is used initially as its Image, i.e. is assigned by reference to Image. This is usually an anonymous text created by giving the text generator Blanks as the parameter to Open. The number given as parameter to Blanks determines the length of Image and should match the record or line length of the external file or device. Where the external record length is not fixed, the length of Image should be the maximum record length possible or expected.

###Open

Open is actually a Boolean procedure. If the attempt by the SIMULA runtime system to find and access the external file specified is unsuccessful, Open will return the value False. This can happen if the external file does not exist, is not permitted to you or for a number of reasons which are system dependent. Any attempt to read from the File subsequently will result in a runtime error.
Older systems will have Open as a type-less procedure and attempting to open a file which is not available will cause a runtime error on these systems.

If Open succeeds, it will reserve the file or device for future use as a source of input by the program. It is not possible to write to an InFile.

Open also assigns its text reference parameter to Image, creating a "buffer" text into which all records from the external file or device will be read. It is possible to assign a new text as Image later in the program.

A successful call on Open will return the value True.

###IsOpen

IsOpen is a Boolean procedure. It returns false if the File is not currently open, i.e. no call of Open has yet been made or the File has been closed. Open sets IsOpen to true. Close sets IsOpen to false.
Older systems may not have IsOpen as a File attribute.

###SetAccess

Modern SIMULA systems allow programs to specify certain modes for open files. The exact meaning of these will depend on the physical file system of the computer. Each file used will have initial properties, some of which are defined in the SIMULA standard. These will be found in the documentation for the system you are using. A summary is given in table 7.1.
All SIMULA systems which meet the current standard will allow files with the following properties.

Shared:
Values can be: shared/noshared. Decides whether use of the file by this program excludes its use by others.
Append:
Values can be: append/noappend. Decides whether output to this file will overwrite its existing contents or is appended to it.
Create:
Values can be: create/anycreate/nocreate. At call of Open physical file must not exist/ will be created if it does not exist/ must already exist.
Readwrite:
Values can be: readonly/writeonly/readwrite. Decides whether reading or writing is allowed to this File. Only relevant for DirectFiles. (See chapter 15.)
Bytesize:
Values can be: any positive integer within a range allowed by that system. Tells the system how many bits are in a byte on this machine. Only relevant for ByteFiles. (See chapter 15.)
Rewind:
Values can be: rewind/norewind. Decides whether the file is to be reset in some way when Close is called.
Purge:
Values can be: purge/nopurge. Decides whether the physical is to be deleted when Close is called.

SetAccess is a Boolean procedure with a single text parameter, which must be one of the values given above or bytesize: followed by an integer. This is used to reset the appropriate mode value in the File. Where the mode is unrecognised or inappropriate for the type of file, SetAccess returns False. Otherwise it returns true.
Table 7.1: Default modes for file types.

    Mode             File type is            Affects
                  In-   Out-     Direct-  
    Shared      shared  noshared  noshared  Open
    Append      N/A     noappend  noappend  Open
    Create      N/A     anycreate nocreate  Open
    Readwrite N/A       N/A     readwrite Open
    Bytesize:n   *         *         *      Open
    Rewind     norewind norewind    N/A   Open/Close
    Purge    nopurge  nopurge     nopurge    Close
###InImage

The procedure InImage copies the next record from the external file or device into Image. This is equivalent to a text value assignment. If the external record is shorter than Image.Length then it is copied into the leftmost character locations in Image and the remaining character Positions are filled with spaces. If the external record is longer than Image.Length, calling InImage causes a runtime error to be reported.
InRecord

Newer SIMULA systems will also have Boolean procedure InRecord. This works in the same way as InImage, except where the record being read is either shorter or longer than Image.Length.
In the first case InRecord copies it into the start of Image, but leaves the rest of Image's text frame unchanged. Image.Pos will be set to the point to the first unchanged character. In this case InRecord returns false.

Where the external record is longer than Image, only enough is read in to fill Image. In this case InRecord returns true, indicating that a further call will read in the missing part of the current Image, not a new record.

Where the lengths are the same InRecord acts exactly as InImage, but returns false.

###Close

Boolean procedure Close tells the SIMULA system that the external file or device represented by this File object is no longer required by it. This means that it can be used by another File object. Thus, once an InFile has been closed, it can be reopened as an OutFile. Once an external file or device has been released in this way it may also be claimed by other programs and users. If an attempt is then made to reopen it using the original File object, a runtime error may result.
If Close is unable to perform its task, it returns false. Otherwise it returns true. Older systems will only have Close as a type-less procedure.

If a File is left open at the end of a program, it will be closed by the runtime system, with a warning in most systems. It is safer to close files in the program.

###SetPos

This procedure is equivalent to Image.SetPos.
###Pos

This integer procedure returns Image.Pos.
###More

This integer procedure returns Image.More.
###Length

This integer procedure returns Image.Length.
###EndFile

This is a Boolean procedure. When the last character of the last record in the external file has been read it returns the value True, otherwise it returns the value False.
Example 7.3 shows of the use of EndFile to read and print the whole of a record structured File.

Once the last record of the real file is read in by InImage, a further call will assign the ISO end of file character, !25!, as the first character of Image. EndFile now returns the value True. Example 7.3 uses the new keyword not, which reverses the condition being tested. Thus the while loop is performed as long as Inf.EndFile is not True.

Example 7.3: Use of EndFile.

        begin

           ref (InFile) Inf;

           Inf :- new InFile("Source");
           if Inf.Open(Blanks(80)) then ! Assumes no records longer than 80;
           begin   ! Use Open as a Boolean procedure in case File not there;
              while not Inf.EndFile do
              begin
                 Inf.InImage;
                 OutText(Inf.Image);
                 OutImage
              end;
              Inf.Close
           end
     
        end
###LastItem

LastItem is a Boolean procedure, rather like EndFile. It returns False as long as there are any non-space characters left in the File. It will skip any spaces, updating Image and Pos, until it finds a non-space or the end of the File. If it has reached the end, it returns True; if it finds non-space characters, it returns False.
Example 7.4 is a word counting program which uses LastItem.

Example 7.4: Use of LastItem in word counting.

                begin
                
                   ref (InFile) Words;
                   integer WordCount;
                   text ThisWord;
                   Boolean ThisisFirstSpace;
                   character Item;
                   
                   Words :- new InFile("LATESTPAPER");
                   Words.Open(Blanks(132)); ! Assume line length of 132;
                   ThisisFirstSpace := True;
                   ThisWord :- Blanks(132);
                   
                   while not Words.LastItem do
                   begin
                      while Words.More do
                      begin
                         Item := Words.Image.GetChar;
                         if Item ne ' ' then
                         begin
                            ThisisFirstSpace := True;
                            ThisWord.PutChar(Item);
                         end else
                         if ThisisFirstSpace then
                         begin
                            ThisisFirstSpace := False;
                            OutText(ThisWord.Strip);
                            ThisWord :- Blanks(132);
                            OutText(" ");
                            WordCount := WordCount + 1
                         end--of--a--word;
                      end--of--a--line;
                      if ThisisFirstSpace then
                      begin
                         WordCount := WordCount + 1;
                         OutText(ThisWord);
                         ThisWord :- Blanks(132)
                      end;
                      OutImage;
                      OutText("   ### Words so far = ");
                      OutInt(WordCount,6);
                      OutImage;
                   end--of--word--counting--loop;
                   OutText("Total word count is ");
                   OutInt(WordCount,6);
                   OutImage;
                   Words.Close
                end
                begin
###Item oriented input

Some procedures read in the sequence of characters in the real file as an item of a certain type. Most are described in {{ book.Chapter8 }}, but here are two useful ones.
###InChar

InChar is almost the same as Image.GetChar. It is a character procedure which returns the next character in the File. If the end of the current Image has been reached, i.e. Image.More returns False, InImage will be called and the first character in the new Image read.
Since SIMULA specifies that a call on InImage once the last record of the real File has been read will place the ISO end of file character, !25!, as the first in Image. A call on InChar once the last character of the real file has been read will return this non-printing character and EndFile will then return True. If the end of the File has been reached, i.e. EndFile returns True, a call on InImage will cause a runtime error. We can rewrite our word counting program, using InChar to replace Image.GetChar, as shown in example 7.5.

Example 7.5: LastItem and InChar in word counting.

                begin
                
                   ref (InFile) Words;
                   integer WordCount;
                   text ThisWord;
                   Boolean ThisisFirstSpace;
                   character Item;
                   
                   Words :- new InFile("LATESTPAPER");
                   Words.Open(Blanks(132)); ! Assume line length of 132;
                   ThisisFirstSpace := True;
                   ThisWord :- Blanks(132);
                   
                   while not Words.LastItem do
                   begin
                      while Words.More do
                      begin
                         Item := Words.InChar;
                         if Item ne ' ' then
                         begin
                            ThisisFirstSpace := True;
                            ThisWord.PutChar(Item);
                         end else
                         if ThisisFirstSpace then
                         begin
                            ThisisFirstSpace := False;
                            OutText(ThisWord.Strip);
                            ThisWord :- Blanks(132);
                            OutText(" ");
                            WordCount := WordCount + 1
                         end--of--a--word;
                      end--of--a--line;
                      if ThisisFirstSpace then
                      begin
                         WordCount := WordCount + 1;
                         OutText(ThisWord);
                         ThisWord :- Blanks(132)
                      end;
                      OutImage;
                      OutText("   ### Words so far = ");
                      OutInt(WordCount,6);
                      OutImage;
                   end--of--word--counting--loop;
                   OutText("Total word count is ");
                   OutInt(WordCount,6);
                   OutImage;
                   Words.Close
                end
                begin
###InText

InText is a text procedure, with a single, integer value parameter. Its result is a text containing the next N characters in the real file, where N is the value of its integer parameter. It may include characters from more than one record in the real file, calling InImage as necessary.
Example 7.6 is a new line splitting program, using InText. Note that it assumes an input file with lines 132 characters long. Note also that it does not deal with blanks at the end of Image.

Example 7.6: Line splitting with InText.

                begin
                   ref(InFile) OldLines;
                   OldLines:- new InFile("MANUSCRIPT");
                   OldLines.Open(Blanks (132));
                   while not OldLines.EndFile do
                   begin
                      OutText (OldLines.InText (80));
                      OutImage
                   end;
                   OldLines.Close
                end
###ImageFile class OutFile

OutFile is the output equivalent of InFile and so has some similar attributes and some which are the output equivalents of InFile's input attributes. Again, some will be left until chapter 8. EndFile and LastItem do not exist for OutFile, since they would have no meaning.
###Image

The Image text of an OutFile is set up in the same way as that of an InFile. It is used to accumulate items which are to be output, until a call of OutImage or OutRecord writes the current line or record to the real file or device.
###Open

Boolean procedure Open works in approximately the same way as for InFile, except that the real file or device is reserved for output, not input, and the real file may be created if it does not exist already. The text reference parameter is used as the initial Image, as in InFile.
###IsOpen

IsOpen works in exactly the same way as it does in InFile.
###SetAccess

SetAccess works in exactly the way as it does in InFile.
###OutImage

When OutImage is called either, explicitly, from the program or, implicitly, by one of the item output routines like OutChar, the current contents of Image are written as a new record to the file or device connected to this OutFile.
Image is then filled with spaces as if by

      Image :- Blanks (Image.Length);
###OutRecord

Procedure OutRecord works in a similar way to OutImage, but only writes out those characters preceding Pos in Image. Thus OutRecord can be used to create files with variable length records, where the operating system supports this.
Older systems will not have OutRecord.

###BreakOutImage

Procedure BreakOutImage writes out the contents of Image in the same way as OutRecord, except that it does not produce any implicit line terminator. Thus successive calls of BreakOutImage will write a series of Images to the same line.
It is intended to allow prompts to be displayed on interactive terminals, in a manner that allows input to be typed on the same line.

Where an operating system does not support output without line terminators, BreakOutImage is exactly equivalent to OutRecord. Older systems will not have BreakOutImage.

###Close

This procedure is the same as for InFile, except that, if the Image is non-empty at close, i.e. Image.Pos is not 1, there remain some unwritten characters. In this case, Open calls OutImage to write out the remaining record.
Again, files will be closed automatically at the end of the program, although it is tidier and safer to close them in the program.

This means that the programs 7.7 a, b and c are equivalent.

It is much safer to call OutImage and Close explicitly, when they are needed. Consider the effect of extending the program to do further writing to Out1, if you are depending on the end of the program to output the Image containing "SUCCESS".

###CheckPoint

Boolean procedure CheckPoint is used to safeguard any output done so far. If it is called, the operating system is requested to complete all writing to the external file which may have been buffered or delayed before returning. If this is successful, CheckPoint returns true. If it fails or the operating system does not allow checkpointing, false is returned.
Example 7.7a.

                begin
                   ref(OutFile) Out1;
                   Out1:- new OutFile ("DESTINATION");
                   if Out1.Open (Blanks (132)) then
                   begin
                      Out1.OutText ("SUCCESS");
                      Out1.OutImage;
                      Out1.Close
                   end else 
                   begin 
                      OutText ("File DESTINATION could not be opened");
                      OutImage
                   end
                end
Example 7.7b.
                begin
                   ref(OutFile)Out1;
                   Out1:-new OutFile("DESTINATION");
                   if Out1.Open(Blanks (132)) then
                   begin
                      Out1.OutText("SUCCESS");
                      Out1.OutImage  ;
                      COMMENT do not call Out1.Close;
                   end else
                   begin
                      OutText("File DESTINATION could not be opened");
                      OutImage
                   end
                end
Example 7.7c.
             begin
                ref(OutFile)Out1;
                   Out1:-new OutFile("DESTINATION");
                   if Out1.Open(Blanks (132)) then
                   begin
                      Out1.OutText("SUCCESS");
                      COMMENT do not call Out1.OutImage
                      or Out1.Close;
                   end else
                   begin
                      OutText("File DESTINATION could not be opened");
                      COMMENT do not call OutImage;
                   end
                end
###SetPos, Pos, More and Length

These are all exactly as for InFile, i.e. equivalent to Image.SetPos, Image.Pos, Image.More and Image.Length.
###Item oriented output

Several attributes of OutFile are procedures which write out items of various types. Most are dealt with in chapter 8 , but here are the two which match those given for InFile.
###OutChar

This procedure takes a single character value parameter. It writes this to the File. If there is space in the current Image, i.e. Image.More returns True, it is written by Image.PutChar, otherwise OutImage is first called and the character is then written as the first in the next record.
###OutText

This takes a single text value parameter. As with OutChar, if there is insufficient space in Image, OutImage is first called. The characters of the text are copied into Image as if by successive OutChar calls. Pos is thus increased. If the length of the text is greater than Image.Length a runtime error occurs.
###Exercises

7.3 Write a program to remove double spaces from a whole file, producing a new file. Extend this to place a double space after each full stop.
7.4 Write a program to remove blank lines from a file, producing a new file.

7.5 Write a program which reads a file with no blank lines and produces a double spaced copy, i.e. a file with a blank line between each line of its contents.

7.6 Write a program which asks for the following information:

                input file;
                output file;
                line spacing (double/single);
                multiple space removal required?;
                word count wanted?;
                number of spaces after full stop;
                number of spaces after comma;
and then copies the input file to the output file in the required format.
###OutFile class PrintFile

The third sub-class of ImageFile is actually a sub-sub-class, being an extension of OutFile. A PrintFile has all the attributes of an OutFile, plus some extra facilities. It is designed for use when output is to go to a printer.
The attributes of PrintFile are designed for output with a maximum number of lines on each page. The length of each line will depend on Image.Length. Printing of lines can be set to be double or multiple spaced.

Some of the attributes of OutFile are redefined to allow for this page oriented output. It is a feature of classes in SIMULA, that an attribute of a class can be redeclared with a different meaning in a sub-class. The meaning in the sub-class is then used for objects which are referred to as being of the sub-class.

Only those attributes of PrintFile which are not in OutFile or which are redefined in PrintFile are listed here. All the other attributes of OutFile exist for PrintFile.

###Open

Open operates exactly as for OutFile, but also instructs the printer to move to the start of the first line of a new page.
###Close

Close also performs all the tasks of Close in OutFile. In addition it moves the printer to the end of the page and resets the Spacing, LinesPerPage and Line attributes to 1, system default and zero respectively.
###OutImage and OutRecord

OutImage and OutRecord operate exactly as for OutFile, but in addition:
Before Image is printed, check to ensure that the last line on the curent page, defined by LinesPerPage, has not been passed. If it has, the printer is instructed to move to the start of the next page.
Skip one or more lines if Spacing has been set to produce double or multiple spaced output.
Update a counter indicating how many lines have been output to the current page .
###LinesPerPage

The PrintFile initially assumes that some standard number of lines are allowed on a page. This number is different for different systems and you should consult the Users' Guide or Programmer's Reference Manual for your system to find what it is.
During execution of a program, this number can be reset to fit the printer or paper being used, by calling the procedure LinesPerPage, which has a single integer value parameter. The value of the parameter sets a new maximum number of lines per page.

A value of zero resets the default. A negative value indicates continous printing without page breaks. A positive value is the new number of lines per page.

Example 7.8 writes pages containing only ten lines. Try it and see the effect when you list the output to a printer. (Not all terminals behave like printers and so the effect may not show up if you use one as your output device). In recent SIMULA systems LinesPerPage is an integer procedure returning the value prior to this call. Older will have it as a type-less procedure.

Example 7.8: Small pages using a PrintFile.

                begin
                   ref(PrintFile) Printer;
                   ref(InFile) Source;
                   Printer:- new PrintFile ("LP23");
                   Source:- new InFile ("MYTEXT");

                   Printer.Open (Blanks (80)); ! Sets line length;
                   Source.Open (Blanks (80));
                   Printer.LinesPerPage (10);
                   while not Source.EndFile do
                   begin
                      Source.InImage;
                      Printer.OutText (SOURCE.Image);
                      Printer.OutImage
                   end;
                   Printer.Close;
                   Source.Close
                end
###Line

The PrintFile keeps a count of how many lines it has printed on the current page. Line is an integer procedure with no parameters which returns the number of the next line on the page. When the last OutImage has filled the current page, Line will return a value greater than that set by LinesPerPage. When the File is not open, Line returns zero.
###Spacing

The procedure Spacing has a single integer value parameter. This controls the number of blank lines output after each OutImage, i.e. the amount by which Line will have increased. Spacing is called by the system with 1 as a parameter when the PrintFile object is generated and again by Close.
Example 7.9 shows the effect of Spacing.

Example 7.9: The effect of Spacing in PrintFile.

                begin
                   ref(PrintFile) Prnt1;
                   
                   Prnt1:- new PrintFile ("Out1");
                   Prnt1.Open (Blanks (132));

                   Prnt1.OutText ("LINE 1");
                   Prnt1.OutImage;
                   Prnt1.OutText ("LINE 2");
                   Prnt1.OutImage;
                   Prnt1.Spacing (2);
                   Prnt1.OutText ("LINE 3");
                   Prnt1.OutImage;
                   Prnt1.Spacing (3);
                   Prnt1.OutText ("LINE 4");
                   Prnt1.OutImage;
                   Prnt1.OutText ("LINE 5");
                   Prnt1.OutImage;
                   Prnt1.Close
                end
###Eject

Procedure Eject has a single integer value parameter. It moves the printer to a line determined by the value of this parameter, as follows:

    Value zero or negative,
        report a runtime error.
    Value greater than number of lines allowed per page,
        move to first line of next page.
    Value greater than current value returned by Line, but less than maximum allowed per page,
        move forward on current page to that line number.
    Value less than or equal to current value returned by Line,
        move to that line number on next page.

Try program 7.10 which demonstrates the legal alternatives described above.
Example 7.10: Legal variations of Eject in PrintFile.

                begin
                   ref(PrintFile) Prnt2;

                   Prnt2:- new PrintFile ("OUT2");
                   Prnt2.Open (Blanks (132));
                   Prnt2.LinesPerPage (20);
                   Prnt2.OutText ("LINE 1");
                   Prnt2.OutImage;
                   Prnt2.Eject (10); ! Greater than current Line;
                   Prnt2.OutText ("LINE 2");
                   Prnt2.OutImage;
                   Prnt2.Eject (6); ! Less than current Line;
                   Prnt2.OutText ("LINE 3");
                   Prnt2.OutImage;
                   Prnt2.Eject (7); ! Equal to current line;
                   Prnt2.OutText ("LINE 4");
                   Prnt2.OutImage;
                   Prnt2.Eject (30); ! Greater than LinesPerPage;
                   Prnt2.OutText ("LINE 5");
                   Prnt2.OutImage;
                   Prnt2.Close
                end
###A useful shorthand

It is rather tedious writing

                Prnt2.OutImage;
                Prnt2.Eject(30)
etc. when the prefixing name is always Prnt2. SIMULA allows a shorthand, known as an inspect statement. This may not be used for prefixes which are text identifiers, however, since text is not defined as a class, but is a special type on its own.
The following statements are equivalent for the purposes described here.

                Prnt2.OutImage

                inspect Prnt2 do OutImage
Obviously, the new form is longer for this simple case, but consider the syntax of this inspect statement. There is the keyword inspect, followed by an identifier referencing an object, followed by the keyword do, followed by a statement.
The semantic rule for an inspect statement is that any use of an attribute of the referenced object (i.e. the one whose identifier appears between inspect and do) inside the statement following do is not required to be prefixed by the identifier and a dot.

If we use a compound statement or block as the statement following do, we can save a lot of tedious writing. Thus, programs 7.11 a and b are exactly equivalent.

Where we want to use more than one object we are allowed to use nested inspect statements. In other words we can use another inspect statement as the statement following the do. This is shown by example 7.12.

Examples 7.11: Use of simple inspect. 7.11a.

                begin
                   ref (PrintFile) P1;
                
                   P1:- new PrintFile ("OUT");

                   P1.Open (Blanks (80));
                   P1.OutText ("Message");
                   P1.OutInt (2,3);
                   P1.OutImage;
                   P1.Close
                end
7.11b.
                begin 
                   ref (PrintFile) P1;
                
                   P1:- new PrintFile ("OUT");
                   
                   inspect P1 do
                   begin
                      Open (Blanks (80));
                      OutText ("Message");
                      OutInt (2,3);
                      OutImage;
                      Close
                   end
                end
Example 7.12: Nested inspect statements.
                begin
                   ref(PrintFile) P1;
                   ref(InFile) I1;

                   P1:-new PrintFile ("OUT");
                   I1:-new InFile ("IN1");

                   inspect P1 do
                      inspect I1 do
                      begin
                         P1.Open (Blanks (132)); ! Needs P1;
                         Open (Blanks (80)); ! refers to I1;
                         InImage; ! Can only refer to I1;
                         OutText (Image); ! OutText is P1, Image is I1;
                         OutImage; ! Can only be P1;
                         Close; ! refers to I1;
                         P1.Close; ! Needs P1;
                      end*of*inspect*I1;
                end
Notice that all attributes are taken to be of I1, which is the innermost object inspected, if they might be of P1 as well. Thus, when P1 is opened, we must use P1.Open explicitly, whereas I1 only requires Open, with no prefix. Where an attribute only exists in one of the objects there is no ambiguity. For instance, OutImage does not exist for the InFile, I1, but does exist for the PrintFile, P1.
###SysIn and SysOut

If no prefixing object is used for OutText, InImage etc., we have seen that the SIMULA system assumes that they refer to the standard input and output devices for the system. These may be the screen and the keyboard of a terminal, the job stream and journal of a batch system or whatever is appropriate for the particular computer.
The default input device is accessed through a system InFile referred to as SysIn. The default output device is accessed through a system PrintFile referred to as SysOut.

The program block of a SIMULA program acts as if it were inside a nested inspect statement. This would look as follows:

                inspect SysIn do
                   inspect SysOut do
                   begin
                      ......
                      ......
                      ......
                      ......
                   end
SysOut is defined, not as a ref(PrintFile) variable, but as a ref(PrintFile) procedure.
This means that you cannot assign another File to be SysOut during your programs. Similarly, SysIn is defined as a ref(InFile) procedure.

This is not as complex as it may sound. The implications are roughly as follow:

The real devices referred to by SysIn and SysOut are determined by the SIMULA system and cannot be changed during a program.
Any use of an attribute of InFile or PrintFile in a SIMULA program, which does not have an appropriate object defined for it by the dot notation or by an enclosing inspect statement, is assumed to refer to SysIn or SysOut.
Since SysOut is the inner object inspected, any attribute found in both InFile and PrintFile is assumed to belong to SyOut. Others are assumed to belong to the appropriate one of SysIn and SysOut.
To refer to attributes of SysIn which also occur in PrintFile, the attributes must be prefixed by SysIn or inside an inspect SysIn statement, as we saw in several earlier examples.
###Summary

We have seen how sub-classes of File are used by SIMULA to allow programs to access real files or input/output devices on a computer.
We have looked especially at "record oriented" input and output, based on File class ImageFile.

We have seen the idea of a class and a sub-class.

We have looked at the attributes of three sub-classes of ImageFile, namely InFile, OutFile and PrintFile.

We have seen the use of inspect statements as an alternative to dot notation for accessing attributes of objects, when the object is not a text.

We have seen the way in which SysIn and SysOut provide default input and output devices.

{{  book.Chapter8 }}