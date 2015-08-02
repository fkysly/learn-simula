#Chapter 13 - Let Us See what We Can See

##Inspection and Remote Accessing

###Recap

So far we have treated inspection and dot notation accessing as the same thing. In most simple cases they are. In other situations they may be used to do rather different things. In particular the inspect statement can be used for much more than just a convenient shorthand.
Essentially we have seen that the dot notation form of remote accessing allows us to use the attributes of a class object from outside it. It is also used to access the attributes of text objects, which are not class objects.

The inspect statement has allowed us to assume that any identifiers used in the statement following the keyword do are possibly attributes of the class object being inspected. Thus the programs in examples 13.1 can be treated as identical. A text object may not be inspected in this way.

Example 13.1b shows the situation where an identifier inside the class is the same as an identifier in the main program. When Int is used inside the class, it refers to the declaration inside the class. When Int is used outside the class, it normally refers to the declaration in the main program block. When it is used inside an inspect statement it is always taken to refer to the declaration in the class inspected.

The rule is that the statement following the do in an inspect statement is treated as if it were inside the body of the class being inspected.

This can cause confusion when there is a name conflict, like the one in example 13.1b. It is often clearer to use dot notation in such cases. A better solution, of course, is to avoid name conflicts where possible.

Examples 13.1: Remote accessing.

a) Dot notation.

         begin
            integer Int;

            class Example(Ch); character Ch;
            begin
               integer Int;
               text Txt;
               procedure Proc; OutText("Called");
            end--of--Example;

            ref(Example) Ex1;
            Ex1 :- new Example('?');
            OutChar(Ex1.Ch);
            Ex1.Proc;
            for Int := Ex1.Int step 2 until 6 do Ex1.Txt := "Txt"
         end
b) inspect statement.
         begin
            integer Int;

            class Example(Ch); character Ch;
            begin
               integer Int;
               text Txt;
               procedure Proc; OutText("Called");
            end--of--Example;

            ref(Example) Ex1;
            Ex1 :- new Example('?');
            inspect Ex1 do
            begin
               OutChar(Ch);
               Proc;
               comment Note that the first Int in the next statement now refers to
                       the attribute of Ex1, not the declaration in the main program;
               for Int := Int step 2 until 6 do Txt := "Txt"
            end
         end
###Limitations of dot notation

The most important limitation on dot notation occurs with an object qualified by a class which itself has classes declared within its body or within the body of a prefixing class. This is shown in example 13.2a.
Class Outside contains a class declaration for Inside and other attributes. The main program contains a variable Outsider which references an object of class Outside. Using dot notation we cannot access any of the attributes of Outsider from the main program block.

Similarly, the class Outside2 is a subclass of Outside. The ref(Outside2) object, Outsider2 has attributes in addition to those of Outside, but none of these may be accessed by dot notation because the prefix Outside has a local class declaration.

N.b. the restriction refers to class declarations, not reference variable declarations.

In order to access these attributes from the main program block we must use an inspect statement as shown in example 13.2b.

Examples 13.2: Limitations of dot notation.

a) An illegal use of dot notation.

         begin

            class Outside;
            begin

               class Inside;
               begin
                  text InsideText;
               end..of..Inside;

               integer Int1;
               text Text1;

            end--of--Outside;

            Outside class Outside2;
            begin
               integer Int2;
               text Text2;
            end;

            ref(Outside) Outsider;
            ref(Outside2) Outsider2;

            Outsider :- new Outside;
            Outsider2 :- new Outside2;
            Outsider.Text1 :- Copy("Illegal");
            Outsider2.Int2 := 4

         end++of++program
b) A legal version of the same program using inspect.
         begin

            class Outside;
            begin

               class Inside;
               begin
                  text InsideText;
               end..of..Inside;

               integer Int1;
               text Text1;

            end--of--Outside;

            Outside class Outside2;
            begin
               integer Int2;
               text Text2;
            end;

            ref(Outside) Outsider;
            ref(Outside2) Outsider2;

            Outsider :- new Outside;
            Outsider2 :- new Outside2;
            inspect Outsider do Text1 :- Copy("Legal");
            inspect Outsider2 do Int2 := 4

         end++of++program
Extending inspect

The simple inspect statement that we have used so far is useful so long as we know the type or qualification of the object being inspected. This may not always be sufficient.
We saw in chapter 11 that it is possible to reference all objects which are subclasses of one parent class using a variable declared as a ref to the parent. This enabled us to form a linked list of objects of different subclasses, for instance. At the same time we saw the limitation of this, in that the subclass attributes could not be accessed through these variables, only the attributes of the parents. Neither dot notation nor simple inspect statements can get around this problem.

Another problem with remote accessing is that it is clearly nonsense to use a reference variable before it has been pointed at an object (assigned a reference). Such an attempt is in fact illegal and will cause a runtime error to be signalled.

To get around these problems we can use extensions of the inspect statement.

###inspect plus otherwise

The simplest extension allows us to detect the use of references which currently do not point to a class object. Such references are said to point to an imaginary object, with no attributes, called None. This is the only object that any reference, regardless of its type, can point at.
Consider examples 13.3. The first program would simply ignore some elements of the array References, since not all the reference variables in this array have been pointed at objects of class Example. The second program, on the other hand, will detect such variables and report which they are. It does this by adding an otherwise clause to its inspect statement.

The syntax of our extended inspect statement is now the keyword inspect, followed by a reference to a class object, followed by the keyword do, followed by a statement, optionally followed by an otherwise clause.

The syntax of an otherwise clause is the keyword otherwise followed by a statement.

The reason for choosing inspect as the keyword for this type of statement is perhaps a little clearer now. The meaning of an inspect statement makes it a suitable choice. The class object is first inspected to see if it is None. If not, it is used to provide the class which is assumed to be remotely accessed in the statement following the keyword do, which is then executed. If it is None, the statement following the keyword otherwise is executed, where present. If there is no otherwise clause, the use of None will cause the statement following do to be skipped.

Notice that we have used the phrase "reference to a class object", for what comes between inspect and do. This is usually an indentifier of ref to class type, possibly subscripted, but could be any expression yielding a ref to class result. An example of an alternative could be a type procedure, whose type is ref to a class and which returns a reference to a class object, like SysIn and SysOut. Chapter 17 will make a lot of use of such type procedures.

The statement following do acts as if it were inside the class object being inspected. All identifiers are first matched against declarations within the class and its prefixing classes. In technical terms, the qualification of this statement is the same as the class inspected.

This extension to the inspect statement is quite useful, but it does not allow us to do anything that was not already possible. Example 13.3c shows the same program written without the otherwise, but still checking for None. To see the real power of the inspect concept, we must extend the definition still further.

Examples 13.3: Detecting None with otherwise.

a) A program which fails to check for None.

         begin

            class Example;
            begin
               text Message;
               Message :- Copy("Legal")
            end..of..Example;

            ref(Example) array References(0:10);
            integer Counter;

            comment Only every other element assigned to;
            for Counter := 0 step 2 until 10 do References(Counter) :- new Example;

            comment Every element inspected;
            for Counter := 0 step 1 until 10 do
               inspect References(Counter) do
               begin
                  OutText(Message);
                  OutImage
               end--of--inspecting--element

         end++of++program
b) The same program using otherwise to check for None.
         begin

            class Example;
            begin
               text Message;
               Message :- Copy("Legal")
            end..of..Example;

            ref(Example) array References(0:10);
            integer Counter;

            for Counter := 0 step 2 until 10 do References(Counter) :- new Example;

            comment Still inspect every element, but use otherwise to report nones;
            for Counter := 0 step 1 until 10 do
               inspect References(Counter) do
               begin
                  OutText(Message);
                  OutImage
               end--of--the--connection--block
               otherwise
               begin
                  OutText("Illegal");
                  OutImage
               end--of--the--othrwise--clause

         end++of++program
c) The same program without inspect.
         begin

            class Example;
            begin
               text Message;
               Message :- Copy("Legal")
            end..of..Example;

            ref(Example) array References(0:10);
            integer Counter;

            for Counter := 0 step 2 until 10 do References(Counter) :- new Example;

            comment Use direct checking and dot notation;
            for Counter := 0 step 1 until 10 do
            begin
               if References(Counter)=/=None then
               begin
                  OutText(References(Counter).Message);
                  OutImage
               end--of--legal--reference--case
               else
               begin
                  OutText("Illegal");
                  OutImage
               end--of--the--None--case
            end**of**for**loop

         end++of++program
###The use of inspect plus when

The inspect statement used so far allows us to treat None and one particular type of class object differently. By adding a feature called the when clause, we can treat a class object according to its actual type, out of a range of possible alternatives.
Example 13.4 shows the use of an inspect statement with when clauses to process a linked list. All the objects on the list belong to classes sharing a common prefix. They are linked by variables declared as ref to this common prefix.

When we come to process each object, we wish to treat it differently according to its innermost type. In fact we need not select the innermost, but we are restricted to the level of qualification that we choose, so that attributes at inner levels would be invisible within the when clause.

This extension's syntax replaces the single keyword do with one or more when clauses. The otherwise clause remains optional, but its meaning changes slightly.

Each when clause consists of the keyword when, followed by the name of a class, followed by the keyword do, followed by a statement. Each occurrence of the keyword when marks the beginning of a new when clause.

The when clause contains an identifier matching a class declared in the program and visible to the inspect statement. The execution of an extended inspect statement tries to match the qualification of the inspected object or one of its prefixing classes to the class identifiers in each when clause in turn. Once a match is found, the statement following the do in that when clause is executed.

The statement following the do in a when clause is treated as if it were declared inside the class whose identifier is used in that clause. Thus, in example 13.4, when the objects on the list are inspected this determines which prefix level the declaration of procedure Print is taken from. Although the objects from the list can only be accessed as ref(A) objects, within each when clause the attributes of the appropriate sub-classes can be used.

Note the order of the when clauses. Inner classes must be tested for before outer, if we wish them to be distinguished. This is an important consequence of the rules given above.

If no match is found, the statement is skipped unless it contains an otherwise clause, in which case that is executed.

Within the extended inspect statement the otherwise clause, where present, is executed if the referenced object is None, as before, and also if its qualification fails to match any of the when clauses. In programs which are still being developed this can be used to handle currently missing cases, by printing out a suitable warning. It can also catch those cases which you have forgotten to deal with!

Example 13.4: Mixed list processing using when.

         begin

            class A;
            begin
               ref(A) Link;
               procedure Print; OutText("Class A");
            end..of..A;

            A class B;
            begin
               procedure Print; OutText("Class B");
            end..of..B;

            A class C;
            begin
               procedure Print; OutText("Class C");
            end..of..C;

            B class D;
            begin
               procedure Print; OutText("Class D");
            end;

            C class E;
            begin
               procedure Print; OutText("Class E");
            end..of..E;

            ref(A) Head, NextA;

            for NextA :- new B, new C, new D, new E do
            begin
               NextA.Link :- Head;
               Head :- NextA
            end--of--building--mixed--list;

            while NextA=/=None do
            begin
               NextA.Print; ! Always prints "Class A";
               inspect NextA
                  when E do Print
                  when D do Print
                  when C do Print
                  when B do Print
                  when A do Print
                  otherwise OutText("Not a predicted subclass of A");
               OutImage;
               NextA :- NextA.Link
            end--of--while--loop

         end++of++program
###A practical example - simple sorting and merging

Example 13.5 shows the use of a when clause to help in processing records which are read in unsorted. The records are of four types and are marked by the contents of their first line. The first character of this line is 'M' for a male and 'F' for a female. The second character is 'A' for an accountant and 'D' for a dancer.
Input is terminated by a line containing ".end" as its start.

The program first reads each record into an object of one of four classes designed to hold their particular data. All four of these classes are prefixed by class Linker, which contains pointers to allow linked lists to be built up. At first a single list of all the objects is constructed using the first pointer variable in Linker.

This list is then processed, with the aid of a when clause, and the objects placed on one from each of two pairs of lists, using the other two pointers in Linker.

Finally these lists are written out separately.

The two complementary actions of sorting and merging lists are fundamental to many uses of computers. The addition of facilities to insert new items in a list and to modify existing items gives a powerful basic set of tools for manipulating data. We are now in a position to build such tools and extend them as the need arises.

Example 13.5: Merging and sorting using inspect.

         begin

            class Linker;
            begin
               ref(Linker) Next, Sex, Employment;
               text ID;
            end--of--Linker;

            Linker class Male_Dancer;
            begin
            
               ! Should contain full details;

            end--of--Male--Dancer;

            Linker class Female_Dancer;
            begin

               ! Should contain full details;

            end--of--Female--Dancer;

            Linker class Male_Accountant;
            begin

               ! Should contain full details;

            end--of--Male--Accountant;

            Linker class Female_Accountant;
            begin

               ! Should contain full details;

            end--of--Female--Accountant;

            procedure Onto_List(Entry,Gender,Occupation);
                               name Gender,Occupation;
                               ref(Linker) Entry,Gender,Occupation;
            begin
               Entry.Sex :- Gender;
               Gender :- Entry;
               Entry.Employment :- Occupation;
               Occupation :- Entry
            end--of--Onto--List;

            procedure Write_List(Heading, ListHead); text Heading;
                                                     ref(Linker) ListHead;
            begin
               Boolean SexList;
               SexList := (ListHead==Males or ListHead==Females);
               OutImage;
               OutText(Heading);
               OutImage;
               OutImage;
               while ListHead=/=None do
               begin
                  OutText(ListHead.ID);
                  OutImage;
                  if SexList then ListHead :- ListHead.Sex
                             else ListHead :- ListHead.Employment
               end
            end--of--Write--List;

            text Line;
            ref(Linker) NextEntry,List,Males,Females,Dancers,Accountants;

            comment First read the input onto a single list;

            InImage;
            Line :- Blanks(80);
            while SysIn.Image.Strip ne ".end" do
            begin
               Line.SetPos(1);
               Line := SysIn.Image;
               if Line.GetChar='F' then
               begin
                  if Line.GetChar='D' then NextEntry :- new Female_Dancer
                                      else NextEntry :- new Female_Accountant
               end else begin
                  if Line.GetChar='A' then NextEntry :- new Male_Accountant
                                         else NextEntry :- new Male_Dancer
               end;
               InImage;
               NextEntry.ID :- Copy(SysIn.Image);
               InImage;
               NextEntry.Next :- List;
               List :- NextEntry
            end;

            comment Now process the main list, forming threaded lists;

            NextEntry :- List;
            while NextEntry=/=None do
            begin
               inspect NextEntry
                  
                  when Male_Dancer do Onto_List(NextEntry,Males,Dancers)

                  when Female_Dancer do Onto_List(NextEntry,Females,Dancers)

                  when Male_Accountant do Onto_List(NextEntry,Males,Accountants)

                  when Female_Accountant do Onto_List(NextEntry,Females,Accountants);

               NextEntry :- NextEntry.Next
            end;

            comment Now write out by lists;

            if Females=/=None then Write_List("Females",Females);
            if Males=/=None then Write_List("Males",Males);
            if Dancers=/=None then Write_List("Dancers",Dancers);
            if Accountants=/=None then Write_List("Accountants",Accountants)

         end++of++program
###Exercise

13.1 A theatrical agent has a file of records of different kinds of artists. He also has a file of records of requests for artists to be sent for audition. Assume that there are artists who are actors, musicians, singers and comedians and that requests may be for any of these. Further, assume that requests may specify the sex of the required artist and the age, with the age given as child, youth, mature or elderly. Write a program, based on the techniques used in example 13.5, to match requests and artists and print lists of all suitable artists for each request and all suitable requests for each artist.
Getting round the qualification rules

SIMULA goes to great lengths to force you to respect the type of a reference to a class object. In general a reference to such an object can only be used to access those attributes visible at the qualification level of the reference.
The restriction means that in a program containing declarations of class A, A class B and B class C, a variable declared as ref(B) can normally only be used as follows:

It cannot reference objects of class A.
It can reference objects of class B. Through it all the attributes declared in B and all the attributes declared in A whose names are not reused in declarations in B can be accessed remotely.
It can reference objects of class C, but only attributes declared in A or B can be accessed. Even if the name of an attribute declared in A or B is redeclared in C, the use of a reference whose qualification is B prevents the use of the meaning given in C.
One method of overcoming these restrictions is the use of the when clause form of inspect statement. We have seen that this can allow us to identify inner classes and access their attributes.
An alternative to this is known as "instantaneous" qualification. It does not allow a series of choices, like the full inspect statement with when clauses. Nor does it allow us to avoid references pointing to None. In these ways it is less powerful than inspection. On the other hand, it can be used to restrict as well as extend the levels of attributes which may be used.

The feature used is based on the keyword qua and example 13.6 show two ways of using it.

13.6a shows it used rather like a when clause. It allows legal access to a class object at a level inner to that at which it is being referenced.

13.6b shows its power in the reverse direction. It is used to access the procedure POuter declared in class A, through a reference to A class B, even though POuter is redeclared as an integer inside B.

13.6c shows an illegal use of qua. An attempt is made to access an inner level when the object referenced is not of the inner type. This would produce a runtime error.

Example 13.6: Uses of qua.

         begin

            class A;
            begin
               integer I,J;
            end--of--A;

            A class B;
            begin
               text I,J;
            end--of--B;

            ref(A) ARef1, ARef2;
            ref(B) BRef;

            ARef1 :- new B;
            BRef  :- new B;
            ARef2 :- new A;

      ! a);   ARef1 qua B.I :- Copy("Getting inside");

      ! b);   BRef  qua A.I := 4;

      ! c);   ARef2 qua B.I :- Copy("No inside to get to")

         end++of++program
###Syntax and semantics of reference expressions using qua

The use of qua is in expressions giving a reference to a class object. Its syntax is any reference to a class object, followed by the keyword qua, followed by the name of a class.
This group is still a reference to the class object, but it is accessed at the prefix level defined by the class specified. Only the attributes of this class and any prefixes to it may be accessed.

###Referencing yourself

This chapter has introduced some very powerful concepts, but the usefulness of them is not always immediately apparent. It is only when you have to write quite complex programs that you may appreciate qua, for instance.
One very useful but much simpler feature in SIMULA is the ability of an object to reference itself. It is achieved by using the keyword this. Example 13.7 shows the use of this.

Formally the keyword this, followed by a name of a class is a reference to the class object containing it. The class name must be the one qualifying that object or on its prefix chain.

To make this clearer, consider example 13.7. Here the generation of a new object of class Linker, or prefixed by it, automatically inserts that object in the list whose head is passed as a parameter to it. Without the use of this, it would be impossible to perform such an action.

Using this many more actions can be included as local procedure attributes or local statements in the definition of a class. This is very important to the idea of object oriented programming.

Example 13.7: Using this in 13.5.

         begin

            class Linker;
            begin
               ref(Linker) Next, Sex, Employment;
               text ID;

               procedure Add_to_List(LHead); name LHead; ref(Linker) LHead;
               begin
                  Next :- LHead;
                  LHead :- this Linker
               end..of..Add..to..List;

               procedure Onto_Lists(Gender,Occupation);
                   name Gender,Occupation;
                   ref(Linker) Gender,Occupation;
               begin
                  Sex :- Gender;
                  Employment :- Occupation;
                  Gender :- Occupation :- this Linker
               end..of..Onto..Lists;

               InImage;
               ID :- Copy(SysIn.Image);
               InImage;
            end--of--Linker;

            Linker class Male_Dancer;
            begin
            
               ! Should contain full details;

            end--of--Male--Dancer;

            Linker class Female_Dancer;
            begin

               ! Should contain full details;

            end--of--Female--Dancer;

            Linker class Male_Accountant;
            begin

               ! Should contain full details;

            end--of--Male--Accountant;

            Linker class Female_Accountant;
            begin

               ! Should contain full details;

            end--of--Female--Accountant;

            procedure Write_List(Heading, ListHead); text Heading;
                                                     ref(Linker) ListHead;
            begin
               Boolean SexList;
               SexList := (ListHead==Males or ListHead==Females);
               OutImage;
               OutText(Heading);
               OutImage;
               OutImage;
               while ListHead=/=None do
               begin
                  OutText(ListHead.ID);
                  OutImage;
                  if SexList then ListHead :- ListHead.Sex
                             else ListHead :- ListHead.Employment
               end
            end--of--Write--List;

            ref(Linker) NextEntry,List,Males,Females,Dancers,Accountants;
            text Line;

            comment First read the input onto a single list;

            InImage;
            Line :- Blanks(80);
            while SysIn.Image.Strip ne ".end" do
            begin
               Line.SetPos(1);
               Line := SysIn.Image;
               if Line.GetChar='F' then
               begin
                  if Line.GetChar='D' then NextEntry :- new Female_Dancer
                                      else NextEntry :- new Female_Accountant
               end else begin
                  if Line.GetChar='A' then NextEntry :- new Male_Accountant
                                      else NextEntry :- new Male_Dancer;
               end;
               NextEntry.Add_to_List(List)
            end;

            comment Now process the main list, forming threaded lists;

            NextEntry :- List;
            while NextEntry=/=None do
            begin
               inspect NextEntry

                  when Male_Dancer do Onto_Lists(Males,Dancers)

                  when Female_Dancer do Onto_Lists(Females,Dancers)

                  when Male_Accountant do Onto_Lists(Males,Accountants)

                  when Female_Accountant do Onto_Lists(Females,Accountants);

               NextEntry :- NextEntry.Next
            end;

            comment Now write out by lists;

            if Females=/=None then Write_List("Females",Females);

            if Males=/=None then Write_List("Males",Males);

            if Dancers=/=None then Write_List("Dancers",Dancers);

            if Accountants=/=None then Write_List("Accountants",Accountants)

         end++of++program
###this in inspect statements

It is important to remember that the statement following the keyword do in a simple inspect and the statements in the when clauses of the extended inspect are treated as if they were inside the body of the object inspected. It is therefore possible to use this in these statements to refer to the inspected object.
###Reference parameters

We have used references to objects as parameters without really considering the implications. We shall do so briefly here.
References to class objects may be declared and passed as parameters to both procedures and classes. They are passed to both by reference as a default. They may be passed by name to procedures but not to classes. They may never be passed by value. (See tables 6.1 and 9.1.)

The actual reference passed when the procedure is called or the class object is generated, must be compatible with the class specified in the declaration. This means that its qualification must be equal or inner to the declared class, just as in a reference assignment.

###Exercises

13.2 Rewrite example 13.7. Add a procedure Into to class linker. This takes one parameter which is a ref(Header). When Into is called it should insert the Linker object into the list of the Header passed. What mode should be used for the parameter?
13.3 Extend Into in 13.2, giving it a second parameter which is a ref to Linker. When called it should now insert the object through which it is accessed into the list after the object referenced by its second parameter. Beware of None. What modes should be used here?

###Summary

We have examined the uses, advantages and limitations of the dot notation for remote accessing.
We have seen the full use of the inspect statement in both its simple and extended forms.

We have used the otherwise clause to cope with cases of references to None.

We have used the when clause to differentiate subclasses referenced through a common prefix. This has allowed us to access attributes which were not available through this prefixing class. Otherwise allowed us to cater for unwanted subclasses along with None.

The use of qua has been shown to allow us both to extend to inner prefix levels and restrict to outer prefix levels the qualification of class object references.

We have examined ways in which this can be exploited, allowing objects to reference themselves and statements inspecting objects to reference those objects.

A summary of the use of references to class objects as parameters has been presented.

{{ book.Chapter14 }}