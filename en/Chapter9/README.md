#CHAPTER 9 - classes as Records

###A simple example

One very common use for computers in offices today is for printing self-adhesive labels for envelopes. Consider a program which reads in a name and address followed by the number of labels required. We will simplify things by printing our labels underneath one another, one at a time.
Using our knowledge of SIMULA so far, we might write the program shown in example 9.1.

Example 9.1: Simple labels program without classes.

      begin
         integer NumLabs,I;
         text Nam, Street, Town, County, Code;

         procedure OutLine(T); text T;
         begin
            OutText(T);
            OutImage
         end*of*OutLine;
            
         text procedure InLine;
         begin
            InImage;
            inspect SysIn do InLine:-Copy(Image.Strip)
         end*of*InLine;

         OutLine("Name?");
         Nam:-InLine;
         OutLine("Street?");
         Street:- InLine;
         OutLine("Town?");
         Town:- InLine;
         OutLine("County?");
         County:- InLine;
         OutLine("Code?");
         Code:- InLine;
         OutLine("How many copies?");
         InImage;
         NumLabs:=InInt;
         Eject(1);
         for I:=1 step 1 until NumLabs do
         begin
            OutLine(Nam);
            OutLine(Street);
            OutLine(Town);
            OutLine(County);
            OutLine(Code)
         end
      end
This example may not be the neatest SIMULA writeable with our current knowledge, but it does the job. It also shows that, within the program, a label is a sequence of five text values, which are first read in and then printed out. What we are really doing, in a clumsy way, is using an object which has five text variables as attributes.
SIMULA allows us to use complex objects, made up of attributes which are already defined. These attributes may be of the standard SIMULA types or may reference types defined by the user, i.e. one user defined type may use others as attributes.

The construction in SIMULA which can be used to declare a complex type is the class. We have already seen predefined system classes when we looked at File and its sub-classes. Now let us declare a class Lab for use in our program. Example 9.2 shows 9.1 reworked using such a class.

Example 9.2: Simple labels program with classes.

      begin
         integer NumLabs, I;
         
         procedure OutLine(T); text T;
         begin
            OutText(T);
            OutImage
         end;

         text procedure InLine;
         begin
            InImage;
            inspect SysIn do InLine:- Copy(Image.Strip)
         end;
   
         class Lab;
         begin
            text Nam, Street, Town, County, Code;
         end--of--class--Lab;
   
         ref(Lab) Label1;! Declare a pointer to a Lab object;
         Label1:- new Lab;! Create a Lab object and point Label1 at it;
         comment Remote access through dot notation;
         Label1.Nam:- InLine;
         Label1.Street:- InLine;
         Label1.Town:-InLine;
         Label1.County:- InLine;
         Label1.Code:- InLine;
         InImage;
         NumLabs:= InInt;
         comment Now connected access through inspect;
         inspect Label1 do
         begin
            for I:=1 step 1 until NumLabs do
            begin
               OutLine(Nam);
               OutLine(Street);
               OutLine(Town);
               OutLine(County);
               OutLine(Code)
            end
         end
      end
This example may seem longer and more complicated than 9.1. It is certainly true that, for very simple purposes, using classes may offer little advantage. For any but the simplest programs, however, classes can make things much simpler. By the end of this chapter, we shall see this with our labels program.
Let us look at the new features used here. First there is the class declaration. This provides a description for a class of objects which all have the same attributes. In this case we define Lab (label is a SIMULA keyword and may not be used as an identifier). In general, a class declaration is very like a procedure declaration, with the keyword class instead of procedure. We shall look at the precise syntax later.

The declaration of Lab specifies the name of the complex type being defined as the identifier following the keyword class. This identifier is followed by a semi-colon. The attributes of the class are defined in a statement, known as the class body, which follows. Thus Lab has five attributes, all of type text.

Having defined the attributes of our new type, we can now create an object, or as many objects as we like, with those attributes. This is done by using an object generator.

An object generator can be used as a statement on its own or as a reference expression, i.e. on the right hand side of a reference assignment or as a reference parameter. Examples of all these are shown in 9.3.

Examples 9.3: Valid occurences of object generators.

As a complete statement:

      new Printer
As the right hand side of a class reference assignment:

      OutF :- new OutFile
As a class reference parameter:

      Queue_Up(new Passenger)
Other cases are also possible, as we shall see shortly, but these are the main ones.
Our labels program uses the commonest and most easily grasped of these, the reference assignment. A variable is first declared, whose type is ref(Lab). This means that it identifies a location where a pointer to an object of the type defined by class Lab may be stored. This variable is used first as the left hand side (destination) of a reference assignment statement.

The effect of this statement is that a new object containing the attributes of Lab is created. Since Label1 is assigned a pointer to this object (references it), the object's attributes can be accessed through the variable Label1. As we have seen with objects which were of types InFile and OutFile, there are two ways of doing this. Both are shown in example 9.2.

"Remote accessing" of a class object is done by using the identifier of a reference variable which currently contains a pointer to the object, Label1 in our example. A ref(Lab) procedure could also be used, as we have seen with SysIn and SysOut. This reference is followed by a dot, followed by the name of a visible attribute of the class which defines the type of the object being accessed.

This method of accessing attributes may be used for both text objects and class objects. This distinction is important, since the type "text" is not defined by a class.

The other way of accessing the attributes of an object is by "connecting" it first. To connect an object we must use an inspect statement. The syntax of a simple inspect statement was described in chapter 8. In the statement which follows the keyword do, the use of any identifier which has a declaration in the class defining the type of the connected object is assumed to refer to this attribute. If no matching declaration is found in this class or its prefixes, the identifier is assumed to belong outside the object.

Thus, within the inspect statement in example 9.2, the occurences of Nam, Street, Town, County and Code are taken to refer to attributes of the object Label1, since declarations for them are found in class Lab.

A full description of remote accessing is given in {{ book.Chapter13 }}.

###Exercises

9.1 Rewrite the labels program, adding an integer attribute to class Lab. Extend the program so that it will:
read a label from SysIn, prompting for each attribute in turn, including the integer attribute;
Copy labels from an InFile, holding any number of labels, to a printfile;
insert the new label in the correct place as it writes them, assuming the labels to be in numerical order, defined by the integer attribute.
Devise suitable test data and check that your program copes with all possible cases.
9.2 A company wishes to computerise its personnel records. Each record contains the following information:

            Name
            Age
            Date of birth
            Works number
            Job
            Salary
            Marital status
Devise a program which will:
read in a new record, prompting for input, and add this record to the existing file of records, held in order of works number;
request the user to type in the service required and then perform it, initially only performing (a) above;
add an additional service to find a record according to its works number;
add another service to print out all records where a specified attribute has a specified value, e.g. Name=F.Jones;
add another service to find a record and update any or all of its attributes and write an updated file.
###Making classes work for themselves

Classes are objects containing attributes. These may be of any type visible in the block where the class is declared. As we have seen this can allow us to create objects which match the natural groupings of data that we wish to process in our program. This approach is usually called "object oriented" programming.
We also saw, when we considered class File and its sub-classes, that it is not just data attributes that a class object can contain. The power of the class concept as a way of representing objects in our programs is considerably increased by the ability to define procedures as attributes of classes.

Consider our labels program. In example 9.2, we read in each attribute, one at a time, and wrote it out in the same piecemeal way. In the extended version of exercise 9.1, which inserts a new label in the correct place in a file of labels, all with the same structure, this piecemeal reading will have to occur in several places in the program. We have used the action of copying a single line into a text, repeated five times, to read five lines of label data into the five text attributes of a Lab object. This is really one action on an object of type Lab, so far as our object oriented view of labels is concerned, just as addition is an action on two arithmetic objects.

Example 9.4 shows the labels program again, but this time the reading and writing of the contents of a Lab object are made into procedures local to class Lab. The actions allowed on the object are now included in its definition.

Note how indentation and the use of comments after each end makes the structure of the program much easier to follow.

Example 9.4: Labels program using procedures as attributes of a class

      begin
         integer NumLabs, I;

         procedure OutLine(T); text T;
         begin
            OutText(T);
            OutImage
         end--of--OutLine;

         text procedure InLine;
         begin
            InImage;
            inspect SysIn do InLine :- Copy(Image.Strip)
         end--of--InLine;

         class Lab;
         begin
            text Nam, Street, Town, County, Code;

            procedure ReadLabel;
            begin
               Nam :- InLine;
               Street :- InLine;
               Town :- InLine;
               County :- InLine;
               Code :- InLine
            end++of++ReadLabel;

            procedure  WriteLabel;
            begin
               OutLine(Nam);
               OutLine(Street);
               OutLine(Town);
               OutLine(County);
               OutLine(Code)
            end++of++WriteLabel;

         end--of--Lab;

         ref(Lab) Label1;

         Label1 :- new Lab;
         Label1.ReadLabel;
         InImage;
         NumLabs := InInt;
         for I := 1 step 1 until NumLabs do Label1.WriteLabel

      end..of..program
One major advantage of this approach is that, given a sensible choice of names, we will have a much more readable program. Complicated detail is moved from the main part of the program to the procedure attributes of the class and replaced by meaningful procedure names.
By designing the data structure and the operations to be performed on that structure together, as a class declaration, we make the writing of the main program much simpler. We are freed from detail and can think in high level terms. The essence of object oriented programming is to use good design of class declarations to make the rest of our task easier. Once we had defined ReadLabel as an attribute of Lab, for instance, we no longer had to worry how to read in the data each time it was needed. Any program using our Lab class objects can rely on a standard reading procedure call.

Example 9.4 shows a solution to exercise 9.1 using procedures as attributes of Lab. I think it is much clearer than any solution without them, including that given at the back of this book.

You will probably have noticed that in these examples the procedures InLine and OutLine have not been declared inside the class body of Lab, although they are used only there. This is because they do not refer directly to the data structure which Lab represents. They refer to the more general data structure defined by text and so are declared at the most general level, the program block. This leaves them free for use anywhere in the program that they are useful. ReadLabel and WriteLabel are only useful as part of Lab.

Example 9.4 uses File objects to allow label lists to be accessed and created. Note carefully the use of MyInput and MyOutput, which allows input and output to be switched between the default Files, SysIn and SysOut, and the user defined ones, using the same procedures.

Example 9.5: Inserting a numbered label using procedure attributes.

    begin
    
       comment*******************************************
       * MyInput and MyOutput replace SysIn and SysOut. *
       * They can be redefined as necessary.            *
       **************************************************;

       ref(InFile) MyInput;
       ref(OutFile) MyOutput;

       comment***************************
       * Variables used by main program *
       **********************************;

       text Request, Source, Output;
       Boolean Unwritten;
       integer Count;
       ref(Lab) NewLabel, NextLabel;

       comment********************************************
       * Utility procedures, used throughout the program *
       ***************************************************;

       procedure OutLine(T); text T;
       begin
          inspect MyOutput do
          begin
             OutText(T);
             OutImage
          end
       end--of--OutLine;

       text procedure InLine;
       begin
          text Tem;
          inspect MyInput do
          begin
             InImage;
             InLine :- Copy(Image.Strip)
          end
       end--of--InLine;

       comment*******************************
       * Basic label class definition - Lab *
       **************************************;

       class Lab;
       begin

          comment * Data attributes of Lab * ;

          text Nam, Street, Town, County, Code;
          integer Sequence_No;

          comment * procedures operating on objects of type Lab * ;

          procedure WriteLabel;
          begin
             MyOutput.OutInt(Sequence_No,10);
             MyOutput.OutImage;
             OutLine(Nam);
             OutLine(Street);
             OutLine(Town);
             OutLine(County);
             OutLine(Code)
          end++of++WriteLabel;

          Boolean procedure ReadLabel;
          begin
             text First;
             First :- InLine;
             if First ne ".end" then
             begin
                Sequence_No := First.GetInt;
                Nam :- InLine;
                Street :- InLine;
                Town :- InLine;
                County :- InLine;
                Code :- InLine;
                ReadLabel := True;
             end
          end++of++ReadLabel;

       end--of--Lab;
    
       comment*********************
       * Main program starts here *
       ****************************;

       MyInput :- SysIn;
       MyOutput :- SysOut;
       OutLine("Please type name of file holding labels");
       Source :- InLine;   ! Read old label file from SysIn;
       Output :- Blanks(Source.Length+1);
       Output := Source;
       Output.PutInt(Count);   ! New list in file Output;
       OutLine("Do you wish to add another label? Please type Yes or No");
       Request :- InLine;
       while Request="Yes" do
       begin
          UnWritten := True;
          NewLabel :- new Lab;
          OutLine("Type the new label, using a new line for each item");
          if not NewLabel.ReadLabel then OutLine("No new label?");
          MyInput :- new InFile(Source);   ! Read old list from Source;
          MyOutput :- new OutFile(Output);  ! Write new list to Output;
          MyInput.Open(Blanks(80));
          MyOutput.Open(Blanks(80));
          NextLabel :- new Lab;
          while NextLabel.ReadLabel do
          begin
             comment Copy old to new, checking sequence nos.;
             if NextLabel.Sequence_No>NewLabel.Sequence_No then
             begin
                if Unwritten then
                begin
                   NewLabel.WriteLabel;
                   Unwritten := False; ! Prevent further copies;
                end
             end;
             NextLabel.WriteLabel
          end*of*copying*file*to*file;
          OutLine(".end");
          MyInput.Close;
          MyOutput.Close;
          Count := Count + 1;
          Source :- Copy(Output); ! Use Output as input for next addition;
          Output.SetPos(Output.Length-1);
          Output.PutInt(Count); ! Name of next Output file;
          MyInput :- SysIn;
          MyOutput :- SysOut;
          OutLine("Do you wish to add another label? Type Yes or No.");
          Request :- InLine
       end*of*while*"Yes";
       MyOutput.OutText("New label list written in file ");
       OutLine(Source);    ! Name of last Output file used;
    end*of*program

###Making classes work even harder

Procedures as attributes make it possible to embed sequences of actions inside classes. This removes the need for tedious reprogramming of these sequences every time they are used in the main program. Having designed and implemented the class attributes, their internal details can be forgotten. Another mechanism can save even more tedious work.
Often the first actions performed on a new object of a particular class follow the same pattern each time one is created. Typically they involve setting the initial values of the data attributes of the object. In class Lab we made this much easier by writing ReadLabel in the definition of the class. This meant that creating and initialising the data in a Lab object required only two statements. By using a simple extension of the class declaration of Lab, we can do it in one.

Consider example 9.6. Here we have a sequence of statements at the end of the class body of Lab. These read in the values of its various attributes. Such a sequence will be executed whenever a new object of this class is generated by new.

Another novel feature is the use of a parameter in the class declaration. Here it is a Boolean, used to indicate whether the new object should prompt for input or merely read without prompting. This can allow interactive input to be treated differently from input from a file. The value of the parameter must be supplied in the object generator.

For the moment we shall treat parameters to classes rather informally, but the following points are important:

      1. Parameters may not be in Name mode. Only the default mode for the type may be used with classes.
      2. Once an object has been generated by new, at which point matches for all the parameters in the declaration must be supplied, these parameters can be treated as normal attributes of the object. They may be accessed by remote accessing via dot notation or connection.
      3. Procedures and labels may not be used as parameters to classes.

The value of Prompt in 9.6 is set to True, so that input is prompted for. If we rewrote example 9.5 using this version of Lab, the value given would be True for NewLabel and False for NextLabel, to avoid prompting when reading from a file.
Example 9.6: Parameters and initialisation Code in classes.

      begin

         procedure OutLine(T); text T;
         begin
            OutText(T);
            OutImage
         end--of--OutLine;

         text procedure InLine;
         begin
            InImage;
            inspect SysIn do InLine :- Copy(Image.Strip);
         end--of--InLine;

         class Lab(Prompt); Boolean Prompt;
         begin
            integer Sequence_No;
            text Nam, Street, Town, County, Code;

            procedure WriteLabel;
            begin
               OutInt(Sequence_No,10);
               OutImage;
               OutLine(Nam);
               OutLine(Street);
               OutLine(Town);
               OutLine(County);
               OutLine(Code)
            end++of++WriteLabel;

            procedure ReadLabel;
            begin
               Sequence_No := InInt;
               Nam :- InLine;
               Street :- InLine;
               Town :- InLine;
               County :- InLine;
               Code :- InLine
            end++of++ReadLabel;

            comment These actions are performed each time a Lab object
                     is generated;

            if Prompt then
            begin
               OutLine("Type, on separate lines, sequence number, Name, "
      "Street, Town, County and Code.")
            end**of**prompting**if**requested;

            ReadLabel
         end--of--Lab;

         ref(Lab) Label1;

         Label1 :- new Lab(True);   ! Use prompting;
         Label1.WriteLabel
      end..of..program
###Summary

In this chapter we have seen how a programmer can define his or her own complex object types, using structures composed of simpler types. The SIMULA feature which is used for this is the class.
We have seen that procedures may also be attributes of classes.

The means of creating an object of a type defined by a class using the object generator new has been explained.

The two methods of remote accessing of attributes of a class object, dot notation and connection by inspect, have been revised.

The concepts of object oriented programming have been explained and we have seen some of the benefits of this approach. We have noted particularly how the use of procedure attributes to manipulate data attributes can simplify the rest of a program.

The inclusion of actions in the definition of classes has been described. Their use in setting initial values for the data attributes has been shown.

Lastly we have seen the use of parameters to classes. The types and modes allowed have been listed and the need to specify them in the object generator has been explained.

{{ book.Chapter10 }}

###Exercises

9.3 Extend the definition of a label object to describe a letter to someone. Data should include name and address of both sender and recipient, text of the letter, date and method of sending, e.g. surface or air mail. You may assume any suitable maximum number of lines for the text. Write a program which reads in a letter and prints it along with a label for mailing it.
What problems would occur if the letter had to be able to contain a large and unspecified number of lines?

9.4 Rewrite your answers to 9.1 and 9.2 using the new features introduced since then. What are the problems when adding several new records, which cannot be assumed to be in order themselves, to an ordered set like those in our labels file? Think especially of a system where the amount of storage space on the computer is limited. What extra features in SIMULA can you think of to help?

Table 9.1: Modes of transmission to classses for parameter types.
                                     Mode
         Type                   Value   Reference   Name
         Simple type            Default  Illegal  Illegal
         text                   Illegal  Default  Illegal
         Object reference       Illegal  Default  Illegal
         Simple type array      Illegal  Default  Illegal
         Reference type array   Illegal  Default  Illegal
         procedure              Illegal  Illegal  Illegal
         type procedure         Illegal  Illegal  Illegal
         label                  Illegal  Illegal  Illegal
         switch                 Illegal  Illegal  Illegal
