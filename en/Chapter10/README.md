#Chapter 10 - Make me a list

##Lists 1 - Arrays and simple linked lists

###Storing lists

In setting exercises 9.3 and 9.4, I hoped to focus your attention on the need to hold lists in ways which are easy to access. Many programs need to read, update and write out long series of data items. These items are the objects which we wish to manipulate. It is rarely worthwhile to use a computer to process one or two items. Even our program which wrote only a few copies of one label used an object with a list of data items within it.
The use of files allows us to read lists from outside the program and to store them at its end. Unfortunately, as our updating programs show, it is not a good idea to create a new file, external to the program, each time we add, delete or modify an item in a list. We soon end up with a multitude of out of date files.

The use of objects defined by classes allows us to hide a number of basic items inside larger, more complex items. It does not solve the problem of how to refer conveniently to a long list of items in succession. The need to declare and use a separate identifier for each possible line of a letter, for instance, makes long letters unwieldy to process and those of indefinite length almost impossible.

This chapter is the first of three dealing with the handling of lists. It provides simple but elegant mechanisms for solving most of the problems mentioned above. Let us start with the problem of holding a long list of items, which are all of the same type.

###The letter program revisited

The text of a letter can be represented as a list of SIMULA text objects, with a maximum number of characters in each. So far, the only way we have seen to hold them is as a list of text declarations, one for each line. This leads to a very long winded program and you probably confined your answer to exercise 9.3 to letters with only a few lines. A much simpler and more concise way of representing the same thing is to declare a single identifier, representing a numbered list of text references. Such an identifier represents an object known as an array of texts.
Example 10.1 shows the use of an array in a much simplified letter program, where no addresses are allowed for, only the text and the name of the sender.

First look at the array declaration in class Leter. (The mispelling is deliberate since there is a system Boolean procedure called Letter, which we might well wish to use in the same program. This is described in chapter 12.)

Example 10.1: Letter program using a text array.

      begin
         class Leter;
         begin
            text Sender;
            text array Line(1:60);
            integer Len;

            procedure ReadLetter;
            begin
               InImage;
               inspect SysIn do
                  while Image.Strip ne ".end" do
                  begin
                     Len := Len + 1;
                     Line(Len) :- Copy(Image.Strip);
                     InImage
                  end
            end++of++ReadLetter;

            procedure WriteLetter;
            begin
               integer Current;
               for  Current := 1 step 1 until Len do
               begin
                  OutText(Line(Current));
                  OutImage
               end;
               OutText("          Yours faithfully,");
               OutImage;
               OutText("             ");
               OutText(Sender);
               OutImage
            end++of++WriteLetter;

            OutText("Type your letter, ending with '.end' on a line by itself");
            OutImage;
            ReadLetter;
            OutText("Now type your name on a single line");
            OutImage;
            InImage;
            inspect SysIn do Sender :- Copy(Image.Strip)

         end--of--class--Leter;

         new Leter.WriteLetter

      end**of**program
###Simple array declarations

The syntax of an array declaration is the type specifier of the items in the list (integer, ref(Leter) etc.), followed by the keyword array, followed by an identifier, followed by the "bounds" of the list, enclosed in parentheses. Spaces (or ends of line) are used to separate keywords and identifiers as usual. They are not required between the identifier and the left parenthesis, but may be used if you wish.
It is legal to omit the type specifier, in which case the array is asumed to be of type real.

The syntax has not included the form of the bounds. In the commonest case we wish to declare a simple numbered list. The bounds then are two arithmetic values, which are converted to integers if necessary, separated by a colon. In example 10.1 the constant integers 1 and 60 are the bounds. This definition is only the simplest variant, but it covers most uses of arrays for the moment.

The semantics of such a declaration produce information telling the system to reserve space for a list of items of the specified type. This list is to be numbered consecutively, starting with the value before the colon and ending with the value after the colon. This also defines the number of elements in the list.

This list as a whole is referred to by its identifier. Thus a whole array can be passed as a parameter to a class or procedure, by giving just the identifier. Individual items in the list can be referred to by the identifier followed by an arithmetic value enclosed in parentheses, giving the number of the element to be accessed, within the list.

Thus the declaration in example 10.1 tells the SIMULA system to reserve space for a list of sixty text variables. These are to be declared to be numbered from one to sixty. The list will be referred to in the program by the identifier Line.

Note that the value of the first bound does not have to be 1. The bounds can have any values, even negative ones, as long as the first bound is less than the second or equal to it. The first bound is usually referred to as the lower bound and the second as the upper.

Note also that the values of the bounds may be given as real values. In this case they are converted to integers in the same way as for assignments. The values can be arithmetic expressions as well as constants. The normal rules for evaluating expressions apply.

###Using array elements

The items in an array list are often called its "elements". Example 10.1 shows how an individual element of Line can be accessed. This is known as a subscripted variable. The value within the parentheses is called the subscript or the index.
Item number Len of the list is accessed in ReadLetter. It is referred to as Line(Len). Since Len is increased by one before each Image is copied to Line(Len) the effect is to copy successive lines of input into successive elements of the text array Line.

The syntax of a simple subscripted variable is an identifier followed by an arithmetic value enclosed in parentheses. The arithmetic value may be a constant, a variable or a more complicated expression, including a call on an arithmetic type procedure. Where necessary the value will be converted to an integer, following the normal rules.

The semantics are also simple. The value of the subscript gives the number used as an index to the elements of the array.

Note that the value of the lower bound is important in determining which element this refers to. A subscript of six will only refer to the sixth element if the lower bound was one. If the lower bound was four, indexing by six gives the third element.

A subscripted variable may be used wherever a simple variable of the same type may be used.

The value of the subscript, converted to an integer if necessary, must lie between the values of the lower and upper bounds, inclusive. If it is outwith this range a runtime error will be reported.

###Notes on differences amongst SIMULA systems

Some older SIMULA systems may require square brackets, [ and ], instead of parentheses, ( and ). Programs written for such machines may require changes to compile on up to date systems and vice versa. Some compilers will accept either form, which requires even greater care when moving programs.
The lowest permitted value for the lower bound, the highest permitted value for the upper bound and the maximum total number of items permitted in an array are all likely to be different on different systems. The maximum number of elements in an array of one type may also be different from that of another type, even on the same system. Check the Programmers' Reference Manual or Users' Guide for the system you are using.

###Variable length lists

Clearly the use of arrays allows large amounts of data to be held in locations declared within our programs, without the continual need to access files and without declaring long lists of identifiers. The use of loops, especially for loops, allows us to handle arrays in concise and clear ways.
One problem with the use of arrays is that we must tell the system in their declarations how many elements they contain and what their bounds are. Often this may not be known until runtime. This means that example 10.1 can only cope with letters of up to sixty lines. If someone wanted to use the program for a longer letter, they would have to alter the source and recompile it.

Although the array is not always the best solution when there is no way of knowing in advance how long the list will be, it can be made more generally useful by specifying the bounds in other ways.

We have defined the bounds as any expressions giving arithmetic values. This includes constants, as used in 10.1, but also variables and expressions involving operators and type procedures. The only restriction is that any variables used must already have their values fixed before entering the block in which the array is declared. This means that the bounds can changed each time a block is entered.

This idea is not necessarily obvious at first, so take a while to get it straight in your mind. The SIMULA system allocates the space used by each block only when that block is entered. Thus it does not need to know how big an array is until then. If a variable used in the bounds of an array has been declared in an outer block, this variable can have its value set in that outer block before the array's space is allocated in the inner block. The variable must not be declared in the same block as the array, since the system may allocate the block's arrays before its other variables and, anyway, these variables could only have their initial zero values, since no statements may come in front of declarations in a block.

As a consequence, the only block which cannot use variables in array bounds is the program block. This is the outermost block and must use constants in all its array bounds.

All sub-blocks, procedures and classes used in a program are free to use variables in array bounds so long as these are declared in an enclosing block or, for classes and procedures, are parameters. Remotely accessed variables may also be used.

The simple examples in 10.2, 10.3 and 10.4 show how "dynamic bounds", as this mechanism is known, may be used for sub-blocks, procedures and classes respectively. These trival examples demonstrate a very powerful facility.

One important point to note is that when the parameters of a procedure or class are used in bounds for arrays declared in that procedure or class body, they are treated as outside that body. This is the only case where any distinction is made between parameters and other locally declared variables inside the procedure or class body. It is very important that this be allowed.

Example 10.2: Dynamic array bounds in a sub-block.

      begin
         integer I1,I2;   ! Declared at the outermost block level;
         I1 := 2;         ! Sets a non-zero value in I1;
         I2 := 3;         ! Sets a non-zero value in I2;
         begin

            comment Start a sub-block which can only be entered after
                    I1 and I2 have had their values set;

            integer array A1(I1:I2);   ! Declare with I1 and I2 as bounds;
            A1(2) := 6;
         end--of--sub-block;
         comment Array no longer accessible;
      end
Example 10.3: Dynamic array bounds in a procedure.

      begin

         procedure Bounder(Lowest); integer Lowest;
         begin
            comment Parameters may be used as bounds inside a procedure body;

            character array C1(Lowest:4*Lowest); ! Use an expression containing
                                                   Lowest as upper bound;
            C1(Lowest*2+1) := '&';   ! Use an expression in the subscript too;
            OutChar(C1(5));          ! Null unless Lowest is 2;
            OutImage
         end--of--procedure--Bounder;

         Bounder(2);                 ! Should print &;
      end**of**program
Example 10.4: Dynamic array bounds in a class.

      begin
         integer Lower;
         class Cl1(Upper); integer Upper;
         begin
            Boolean array BoolArr(Lower:Upper); ! Use a mixture of enclosing
                                     block's declarations and parameters to set
                                            bounds;
            BoolArr(Lower+3) := True
         end--of--class--Cl1;

         ref(Cl1) Cl1Ref;
         Lower := 4;      ! Sets lower bound before object generation;
         Cl1Ref :- new Cl1(7);      ! Passes upper bound as a parameter;
         if Cl1Ref.BoolArr(5) then OutText("True") else OutText("False");
         OutImage
      end**of**program
###A more practical use of dynamic arrays

Let us return to our general text processing system. It has been some time since we looked at it, but I hope the features introduced since then have been suggesting solutions to the problems we were facing. In particular, I hope the use of classes and object oriented programming has seemed relevant.
This section shows how the combination of class objects and arrays can help the design of the Book level of our program. What we are going to try to do is to design a class Book, with the necessary data structures and procedures for manipulating them. Consider example 10.5.

The Book is represented as a class. It is assumed to have a title page, a contents, a preface, a sequence of chapters and an index. The different data structures of each of these are represented in turn by classes. Since each may only exist as a part of a book, they are declared inside the body of Book (or "local to" Book).

A single ref variable is declared for each of these classes, except for Chapter. The number of chapters in a book can vary considerably and so an array of ref(Chapter) variables is declared. Its lower bound is 1 and its upper bound is a parameter of Book. The actual number of chapters can be specified when the Book object is generated in the main program block. We use

      new Book(InInt)
so that the number is read in and passed, in one statement.
Example 10.5: Class Book and the accompanying program block.

      begin

         class Book(Num_of_Chaps); integer Num_Of_Chaps;
         begin
            text Title, Author;
            ref(chapter) array Chaps(1:Num_Of_Chaps);
            ref(Contents) Cont;
            ref(Preface) Pref;
            ref(Index) Ind;
            integer Count;

            procedure Title_Page;
            begin
               Eject(10);
               OutText(Title);
               OutImage;
               OutText("   by");
               OutImage;
               OutText(Author);
               OutImage;
               Eject(0);
            end++of++Title++Page;

            Pref :- new Preface;
            for Count := 1 step 1 until Num_Of_Chaps do
                     Chaps(Count) :- new Chapter(Count);
            Cont :- new Contents(Pref,Chaps);
            Ind :- new Index(Chaps);
            Cont.PrintContents;
            Pref.PrintPreface;
            for Count := 1 step 1 until Num_Of_Chaps do
                     Chaps(Count).PrintChap;
            Ind.PrintIndex
         end--of--Book;

         class Chapter(Numbr); integer Numbr;
         begin
            procedure PrintChap;
            begin comment Print out this chapter in appropriate format;
               ...
               ...
            end++of++PrintChap;
            ...
            ...
         end--of-Chapter;

         class Contents(Pref,Chaps); ref(Preface) Pref; ref(Chapter) array Chaps;
         begin
            procedure PrintContents;
            begin comment Print a contents page for Pref and Chaps;
               ...
               ...
            end++of++PrintContents;
            ...
            ...
         end--of--Contents;

         class Preface;
         begin
            procedure PrintPreface;
            begin comment Print a preface in appropriate format;
               ...
               ...
            end++of++PrintPreface;
            ...
            ...
         end--of--Preface;

         class Index(Chaps,Num_Chaps); ref(Chapter) array Chaps;
                                       integer Num_Chaps;
         begin
            procedure PrintIndex;
            begin comment Print an index for Chaps;
               ...
               ...
            end++of++PrintIndex;
            ...
            ...
         end--of--Index;

         comment This is the main program;
         OutText("How many chapters?");
         OutImage;
         new Book(InInt)
      end**of**main**program
###Top down again

Example 10.5 shows how object oriented programming makes top down design easier. Only the skeletons of the components of Book are provided, yet its own actions can be fully defined in terms of these. Actions to be performed on one component can be made into local procedures within the class corresponding to that component. The detail of how such procedures will work can be left until that class is itself considered in detail.
This leads to the following description of top down, object oriented design.

The visible properties of each component are defined by the operation of the object of which it forms part. Properties which are not externally visible can be left undefined until the component itself is implemented in detail.
Even simpler is,
Don't clutter up your program with unnecessary detail until you need it.

###Exercises

10.1 Write a program which reads the names of a group of students and creates an array of pupil records, holding name, age, address and marks in maths, English and physics. Allow student details to be filled in in any order, copying the details into the correct entry in the array. Print out the contents in the order in which the list of names was first given.
10.2 Extend 10.1 to sort the names into alphabetical order as it first reads them and to print the records in this order.

10.3 Further extend your program to calculate each student's average mark and to print separate lists ordered alphabetically, by order of marks in each subject and by overall average mark.

###Rearranging lists

An array is an ordered list. Each item in the list is numbered and can be accessed by this number combined with the name of the array. This is very useful where the order is fixed or is irrelevant.
Exercise 10.2 shows that it is often useful to be able to rearrange the order in which the items of a list are held. Exercise 10.3 takes this further and shows that the same items may need to be thought as being on several different lists, held in different orders. The array can only do this by clumsy rearrangement of its elements or by copying the same elements into a number of different arrays.

Even an apparently trivial operation, like adding a new item to a list which is already in order is not easy. Consider example 10.6.

Once the correct position in the list has been found it is necessary to move all the labels after this up by one in the array, unless the new label happens to belong at the end. This is potentially very time consuming. Another problem is that the array may not be large enough. If we want to allow for a list of unknown length, with any number of new labels, arrays are not likely to be useful.

Fortunately, SIMULA allows us to create much more flexible lists, tailored to the needs of a program. These use the ability of class objects to contain pointers or links, in the form of reference variables, to other objects. Lists can be formed in this way, known as "linked lists".

###Reading into a linked list

Diagram 10.1 shows how a new object is added to a linked list. Each object of class Linked\_Lab contains an attribute called Next, which is of type ref(Linked\_Lab). The corresponding program is shown in example 10.7.
New objects are added to the end of this list. Pointers to the current first and last members of the list are held in the ref(Linked\_Lab) variables List\_Head and List\_End.

The concept of adding to a list by manipulating pointers is very powerful. It is worth spending some time getting 10.7 clear in your mind.

Example 10.6: Adding an item to an ordered list in an array.

   begin

      ref(InFile) MyInput;

      class Lab;
      begin
         procedure ReadLabel;
         begin
            integer I;
            inspect MyInput do
            begin
               SeqNo := InInt;
               InImage;
               for I := 1 step 1 until 5 do
               begin
                  Address(I) :- Copy(Image.Strip);
                  InImage
               end
            end
         end--of--ReadLabel;

         text array Address(1:6);
         integer SeqNo;
      end++of++Lab;

      integer Count1, Count2, I;
      ref(InFile) Source;
      ref(Lab) array Labs(1:100);
      ref(Lab) NewLab;

      Source :- new InFile("Labels");
      inspect Source do
      begin
         Open(Blanks(20));
         MyInput :- Source;
         while not EndFile do
         begin
            comment Assume labels in Source already in order of SeqNo;
            Count1 := Count1 + 1;
            Labs(Count1) :- new Lab;   ! Read in successive labels;
            Labs(Count1).ReadLabel
         end..of..reading ..source..into..Labs;
         Close
      end%%of%%inspecting%%Source;

      MyInput :- SysIn;
      NewLab :- new Lab;
      NewLab.ReadLabel;
      Count2 := 1;
      while Count2<=Count1 do
      begin
         if Labs(Count2).SeqNo>NewLab.SeqNo then
         begin
            comment New label goes before Labs(Count2) so make room;
            for I := Count1 step -1 until Count2 do Labs(I+1) :- Labs(I);
            Labs(Count2) :- NewLab;
            Count2 := Count1 + 2;   ! Force end of while loop;
         end--of--adding--new--label;
         Count2 := Count2 + 1;
      end..of..while..loop;

      if Count2=Count1+1 then Labs(Count2) :- NewLab; ! Goes at the end;

      comment Check the sequence;
      for I := 1 step 1 until Count1 + 1 do OutInt(Labs(I).SeqNo,3)

   end**of**program
Example 10.7: Building the linked list in Diagram 10.1.
   begin
      class Linked_Lab;
      begin
         ref(Linked_Lab) Next;
         integer SeqNo;
         comment Simplified version of Lab;
         SeqNo := Inint;
      end--of--Linked--Lab;

      ref(Linked_Lab) List_Head, List_End, Temp;

      ! a)  List_Head and List_End are both None;

      ! b); List_Head :- List_End :- new Linked_Lab;

      ! c); List_End.Next :- new Linked_Lab;
            List_End :- List_End.Next;

      comment Now print the SeqNo of each item in the list;

      Temp :- List_Head;
      while Temp=/=None do
      begin
         OutInt(Temp.SeqNo,3);
         Temp :- Temp.Next
      end..of..printing..list

   end**of**program
###Class reference comparison

The final while loop of example 10.7 contained a class object reference comparison. This used the operator =/=, which means "does not reference" or "does not point at". Formally, it tests for reference inequality. It is similar to the text reference comparator.
There is also a positive equivalent, ==, meaning "does reference", which tests for reference equality. Thus

      if P1==P2 then OutInt(3,3)
would print 3 if the class object reference variables P1 and P2 pointed to the same object.
###None

The same comparison also used None as one of the reference variables. None is a reference to no object. Any reference variable initially references None. Any reference variable can be pointed back to None.
Reference variables of any type can be pointed at None. It is the initial value of reference variables, regardless of their type.

###Exercises

10.4 Extend example 10.7 to build a list of five objects.
10.5 Write a program to add items to the start of a linked list, rather than the end, and which builds a list of four such items.

###Generalising simple linked lists

In exercises 10.4 and 10.5 you will have noticed that for all items except the first, adding a new item to a linked list requires the same sequence of statements. There is a clear case for using procedures for such tasks.
If we think further, a linked list is a data structure with attributes unique to itself. Each list has a head and a tail (although it is possible to use just a head). Furthermore, our proposed procedures for adding items at the beginning and end of a list operate only on attributes of a list object. It is sensible to define a class embodying these attributes for use in processing linked lists. Example 10.8 shows how this might be implemented.

10.8 also shows how a new item can be added to the middle of a list without needing to update all the items which follow. The mechanism used is illustrated in diagram 10.2. Only two reference assignments are needed to achieve it.

Example 10.8: Class Label_List.

   begin
      class Linked_Label;
      begin
         ref(Linked_Label) Next;
         integer SeqNo;
         SeqNo := InInt
      end--of--Linked--Label;

      class Label_List;
      begin
         ref(Linked_Label) Head, Tail;

         procedure AddtoEnd(Lab);ref(Linked_Label) Lab;
         begin
            if Head==None then
            begin
               comment Special case when list is empty;
               Head :- Tail :- Lab
            end else begin
               comment Normal case;
               Tail.Next :- Lab;
               Tail :- Lab
            end
         end++of++AddtoEnd;

         procedure AddtoFront(Lab);ref(Linked_Label) Lab;
         begin
            if Head==None then
            begin
               comment Special case when list is empty;
               Head :- Tail :- Lab
            end else begin
               comment Normal case;
               Lab.Next :- Head;
               Head :- Lab
            end
         end++of++AddtoFront;

      end--of--Label--List;

      ref(Label_List) Labs;
      ref(Linked_Label) New_Lab, Temp;
      integer Count;

      comment Create an initial list of 4 items;
      Labs :- new Label_List;
      for Count := 1 step 1 until 4 do Labs.AddtoEnd(new Linked_Label);

      comment Add a new label to the front;
      Labs.AddtoFront(New Linked_Label);
      comment Add a new label between existing nos. 2 and 3;
      New_Lab :- new Linked_Label;
      Temp :- Labs.Head;
      Temp :- Temp.Next;
      comment Temp now points to item 2 on list;
      New_Lab.Next :- Temp.Next;   ! Step b) in diagram 10.2;
      Temp.Next :- New_Lab;        ! Step c) in diagram 10.2;

      Temp :- Labs.Head;
      while Temp=/=None do
      begin
         OutInt(Temp.SeqNo,3);
         Temp :- Temp.Next
      end;
      OutImage
   end**of**program
###Exercises

10.6 Write a program which locates and removes an item in a linked list.
10.7 Add local procedures to class Linked_Lab, one of which inserts a label object after a given member of the list and one of which removes a label from the list.

10.8 Rewrite your answers to 10.2 and 10.3 using linked lists instead of arrays. Remember that an object can have as many ref attributes as you like and so can be on as many linked lists simultaneously as you wish.

###Arrays versus linked lists

We have seen that arrays are a very convenient way of storing simple values and references to more complex class objects in an ordered list. We have seen that rearranging such lists is very inconvenient. Although the use of dynamic bounds allows greater flexibility, the need to specify the overall size and bounds of an array at the start of the block in which it is declared still imposes quite strict limits to the usefulness of arrays.
Linked lists are much easier to rearrange and to extend. We do not need to fix an upper limit to the number of elements in them. The same object can exist on several such lists at once. On the other hand, if we wish to find item number six, say, on a linked list, we must chain through, counting as we go. In an array accessing item number six is trivial.

There are strengths and weaknesses in both these list structures. It is a question of picking the more appropriate one for your purposes. As we shall see later, linked lists can be even more flexible than those we have used so far and some of their limitations can be reduced. For certain very common purposes, however, arrays remain the best choice.

###Summary

We have seen how simple, ordered lists can be held in arrays. The means for declaring and accessing arrays have been learned, especially how to use subscripted variables to treat any item of an array as a simple variable of the same type.
The use of dynamic bounds to allow variable sized arrays has been shown

We have considered briefly the use of simple linked lists and noted their advantages over arrays.

Finally we have compared the merits of arrays and linked lists and concluded that each structure is better suited to certain applications.

{{ book.Chapter11 }}