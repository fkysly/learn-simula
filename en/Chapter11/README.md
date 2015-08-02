#CHAPTER 11 - Like parent like child

##Sub-classes and complex Boolean expressions

###Building on what you have

One of the important ways we have of making sense of the world is to classify things. We put them into categories or classes. SIMULA allows us to reflect this very natural way of thinking in the way we write programs.
When classifying things we first group them either very generally, e.g. as animal, vegetable or mineral, or very specifically, e.g. as bees or roses, depending on circumstances. These approaches correspond to the programming techniques known as "top down" and "bottom up" design, respectively.

In practice, it is fairly easy to classify things in general terms, but appearances can be deceptive when it comes to detail. Things which look alike may actually have very different origins. Thus the hedgehog and the spiny anteater look remarkably similar and live very similar lives, yet, genetically, they are not closely related at all.

SIMULA takes the top down approach as the safest, just as natural science has tended to. It allows us to define a CLASS, as we have seen, to represent a general type of object. This may then be extended, to reflect the special characteristics of sub-types by defining sub-classes of the original. These retain some or all of the characteristics of the parent type, but include characteristics which are only found in certain objects of this type.

It is important to notice that sub-types in SIMULA extend and refine the range of characteristics of the parent type. The more general the class of objects described, the fewer characteristics that are given to it.

One example of the use of such sub-types, that we have already seen, is the Class File and its sub-classes. If you look back at chapter 7, you will see that special purpose files are represented by adding to the attributes of first File, then ImageFile and, for PrintFile, Outfile. We can show this as a tree; a family tree. Diagram 11.1 is the File family tree as we have seen it so far.

Diagram 11.1 - Family tree of class File

                    class File
                        |
             ------------------------
                                    |
                            File class ImageFile
                                    |
             -----------------------------------------------------------
             |                            |                            |
    ImageFile class Infile     ImageFile class DirectFile   ImageFile class OutFile
                                                                    |
                                                            Outfile class PrintFile
The syntax of a sub-class declaration is very simple. The keyword class is preceded by the name of the parent class. Otherwise the declaration is the same as for a simple class. The new class is said to be "prefixed" by the parent.
###A word processing example

We can consider a chapter in a book as a sequence of pages. This sequence can, as we have seen, be represented as an array or as a linked list. In either case we want all the pages to be of the same type. If they are not, they cannot be held together. All items in an array must be of the same type. All items in a linked list are joined by pointers declared as ref(type) variables, with type the same for all. Thus we need a basic class Page.
Each Page will contain printed blocks. The first Page in each Chapter will have a title block, followed by a sequence of other blocks. The other blocks could contain text or diagrams. A linked list of Print_Block objects can represent the contents of a Page.

Example 11.1 shows the outline of such a set of classes. The parent class for blocks on a page is Print\_Block. It contains a link in the form of ref(Print\_Block) Next. It also contains an array of text variables, Contents, representing the lines in the block. Parameters Width and Length are integers representing line length and number of lines in the block, respectively. Each member of the text array is filled with blanks, initially.

Example 11.1: Representing pages as Print_Block objects.

      begin
         class Page;
         begin

            class Print_Block(Width,Length);integer Width, Length;
            begin
               ref(Print_Block)Next;
               text array Contents(1:Length);
               integer Count;
               for Count:=1 step 1 until Length do Contents(Count):-Blanks(Width)
            end--of--Print_Block;

            Print_Block class Title_Block(Title);text Title;
            begin
               Contents(Length//2):=Title
            end--of--Title_Block;

            Print_Block class Text_Block;
            begin
               for Count:=1 step 1 until Length do
               begin
                  InImage;
                  Contents(Count):=Intext(Width)
               end
            end--of--Text_Block;

            Print_Block class Diagram(Title); text Title;
            begin
               Contents(1):=Title
            end--of--Diagram;

            ref(Print_Block) Head, Tail, New_Block;
            text Directive;
            integer Len;

            procedure Add(NewBlock); ref(Print_Block) NewBlock;
            begin
               if Head==None then Head :- NewBlock;
               if Tail=/=None then Tail.Next :- NewBlock;
                Tail :- NewBlock
            end++of++Add;

            Directive :- InText(2);
            while Directive NE "$E" do
            begin
               if Directive= "$B" then
               begin
                  Len := InInt;
                  InImage;
                  Add(new Title_Block(80,Len,InText(80)))
               end
               else if Directive="$C" then
                  Add(new Text_Block(80,InInt))
               else begin
                  Len := InInt;
                  InImage;
                  Add(new Diagram(80,Len,InText(80)))
               end;
               Directive :- InText(2)
            end
         end..of..Page;
         new Page;
      end
This parent class is used to prefix the three classes representing different types of block on a page. Each contains additional attributes, actions or both. Two contain parameters. Note that these also are additional to those in the parent class.
###A few properties of sub-classes

The program shows some important features of sub-classes.
The attributes of the prefixing class are normally visible in its sub-classes. As we shall see later there is a way of "hiding" the parent's attributes. Note that this visibility of local quantities is unlike any other type of block, such as a sub-block or procedure body.
The parameters of the prefixing class must still be given when generating a new object of one of its sub-classes. Thus, the generation of a new Title\_Block requires three parameters, the two declared for parent Print\_Block followed by the additional one for Title_Block. The prefixing class parameters always come before the sub-class parameters.
The actions specified for the prefixing class are performed, before those of the sub-class, when a new sub-class object is generated. Thus the for-loop which sets the elements of Contents to blank filled texts of the desired Width, takes place before any of the actions of Title\_Block, Text_Block or Diagram.
A reference variable which points to objects of the prefixing class can also point to objects of its sub-classes. Variables which are declared as ref to a sub-class cannot point to a parent object, however. Thus the use of ref(Print\_Block) variables for all the links in the list of blocks means that objects of type Print\_Block, Title\_Block, Text\_Block and Diagram can all fit onto the same list. They are all "qualified" by the type Print\_Block. On the other hand, objects created by the generator new Print_Block, would not be allowed on a list linked by pointers declared as ref(Text\_Block).
###What the program does

The program reads text from SysIn. This text is assumed to end with a line starting with the characters "$E", meaning end of page. The character '$' followed by a sequence of letters and numbers is assumed to be a directive, telling the program what the next sequence represents. Lines of 80 characters are assumed.
The directive "$B" means that a Title\_Block is required. The number of, mostly blank, lines to be used, is given as an integer following the $B directive. The text for the title parameter is on the following line. The title is copied into the middle line of the block by the actions of a new Title_Block.

The directive "$C" means that a new Text\_Block is required. The number of eighty character lines in this block is given as an integer following the directive and is used as a dynamic upper bound to Contents. The contents of the block are on the following lines. The actions of a new Text_Block copy this into Contents.

The directive "$D" means that a space for a diagram should be left. Again the number of lines to be left follows and the next line is the text to be used for the title of the diagram. This is copied into the first text elememt of Contents by the actions of a new Diagram object.

###Exercises

11.1 Add a printing procedure to class Print_Block and use the program to process a source in the required format.
11.2 By using a linked list to hold lines, remove the need to specify the number of lines in a Text_Block. Use a line breaker to avoid splitting words at the ends of a line.

Example 11.2: Concatenation .. sub-sub-classes

      begin
         class OuterMost(First); text First;
         begin
            text OuterText;
            OuterText :- Copy("Outermost=first on prefix chain");
            OutText(OuterText);
            OutImage;
            OutText(First);
            OutImage
         end--of--OuterMost;

         OuterMost class Middle(Second); text Second;
         begin
            text MiddleText;
            MiddleText :- Copy("Middle=second on prefix chain");
            OutText(MiddleText);
            OutImage;
            OutText(First);
            OutText(Second);
            OutImage
         end--of--Middle;

         Middle class InnerMost(Third); text Third;
         begin
            text InnerText;
            InnerText :- Copy("Inner=last on prefix chain");
            OutText(InnerText);
            Outimage;
            OutText(First);
            OutText(Second);
            OutText(Third);
            OutImage
         end--of--InnerMost;

         new OuterMost("One");
         new Middle("One","Two");
         new InnerMost("One","Two","Three")

      end**of**program
###Sub-sub-classes

As is clear from the File family tree, a sub-class can prefix its own extensions. The rules given above still apply, except that the parameters, local variables and actions of all three classes are combined in the grandchild, in the order grandparent, parent, grandchild.
Example 11.2 shows this in a trivial example. Try compiling and running it to see the effects for yourself.

There is no limit to the number of levels of such prefixing which you may use. The same rules apply with each extension. The combining of attributes and actions in the correct order is called the "concatenation" of the prefixing classes and the final extension.

The sequence class, sub-class, sub-sub-class etc. is called a "prefix chain".

In more technical descriptions the first class on the prefix chain is called the "outermost" and the final one the "innermost". A sub-class is "inner" to its parent, grandparent etc. A class is "outer" to its sub-classes, their sub-classes etc.

###Name conflicts in sub-classes

It is quite legal to declare identifiers in a sub-class with the same name as those declared in its parent or another outer class. This is shown in example 11.3. The effects are simple to describe, but can sometimes be confusing to grasp. It is perhaps worth running 11.3 before reading on, to give you a practical experience of the concepts.
In a parent class the use of the identifier always refers to the declaration of that identifier inside that class.

In a sub-class the use of the identifier always refers to the declaration of that identifier inside the sub-class.

That is fairly straightforward. The formal description is that the use of an identifier within the class body where a declaration exists for it, always refers to the location associated with that declaration. If no declaration exists for it inside the class body where it is used, it is said to be "uncommitted" at that prefix level. It is then assumed to refer to the innermost prefixing class containing such a declaration. If there is no class on the prefix chain outer to the class in which the identifier is used which contains a declaration for it, the identifier is looked for outside the class and is not an attribute of it.

Example 11.3: Name conflicts in prefix chains.

      begin
         class Grandad;
         begin
            text T;
            T :- Copy("Grandad"); ! Always refers to the preceding declaration;
            OutText(T);           ! Should always print Grandad;
            OutImage
         end--of--Grandad;

         Grandad class No1Son;
         begin
            text T;               ! Conflicts with declaration in Grandad;
            T :- Copy("No1Son");  ! Refers to T immediately above;
            OutText(T);           ! Should always print No1Son;
            OutImage
         end--of--No1Son;

         Grandad class No2Son;
         begin
            OutText(T);           ! Should always print Grandad, no conflict;
            OutImage
         end--of--No2Son;

         No1Son class GrandDaughter;
         begin
            OutText(T);          ! Should use T from No1Son, print No1Son;
            OutImage
         end--of--GrandDaughter;

         new Grandad;            ! Prints   Grandad;

         new No1Son;             ! Prints   Grandad
                                            No1Son;

         new No2Son;             ! Prints   Grandad
                                            Grandad;

         new GrandDaughter;      ! Prints   Grandad
                                            No1Son
                                            No1Son;
      end**of**program
Example 11.4: The use of inner.

      begin
         class Parent;
         begin
            OutText("Before inner");
            OutImage;
            Inner;
            OutText("After inner");
            OutImage
         end--of--Parent;

         Parent class Child;
         begin
            OutText("In the inner class");
            OutImage
         end--of--Child;

         new Parent;
         new Child
      end**of**program
###Top and tailing - the use of inner

The use of actions within classes should be familiar by now. The ability to build on these in suitably different ways in sub-classes is used in the three sub-classes of Print_Block in example 11.1. This is a very powerful feature of SIMULA.
The usefulness of sub-class actions is increased still further by the inner statement. This allows a parent class to specify one set of actions to be performed before those of its sub-classes and another to be performed after them. Consider example 11.4.

The class Parent contains a statement consisting of the keyword inner and nothing else. When an object is generated from Parent itself this statement is ignored.

The class Child is prefixed by Parent. If the inner statement was not in Parent all the actions of Parent would be performed before any actions of Child, whenever an object was generated from Child. The effect of the inner statement is to alter this sequence.

The two statements of Parent before the inner are executed first. The actions of sub-class Child are performed when the inner is reached. The remaining statements of Parent are only performed when those of Child are complete.

This allows the outer class to define both a prologue and an epilogue to the actions of the inner one. When an object of such an outer class is generated rather than of the inner class, the inner statement is ignored.

Only one inner statement can appear in a class body, since it can only have one inner class whose statements it will perform.

A sub-class may contain an inner statement, in which the actions of any sub-sub-classes will be performed when it is reached, and so on along the prefix chain. Practical uses of this feature, especially in conjunction with prefixed blocks, will be demonstrated later in this book.

###Exercises

11.3 Use an inner statement in class Print_Block to write the number of lines created for that block when processing of it is complete. Write it to a different file from the one used for the actual text.
11.4 Study the following program. What would its output be? Try running it to check your answer.

      begin
         class Outer;
         begin
            text T;
            T :- Copy("ABC");
            OutText(T);
            inner;
            OutImage
         end--of--Outer;

         Outer class Middle;
         begin
            text T;
            T :- Copy("DEF");
            OutText(T);
            inner;
            OutText(T);
            OutImage
         end--of--Middle;

         Outer class Centre;
         begin
            text T1;
            T1 :- Copy("DEF");
            OutText(T);
            OutImage
         end--of--Centre;

         Middle class Inner;
         begin
            text T1;
            T1 :- Copy("GHI");
            OutText(T);
            T := T1;
         end--of--Inner;

         new Outer;
         new Middle;
         new Centre;
         new Inner
      end**of**program
###The types of class objects

We have seen in example 11.1 some of the rules governing the types or qualifications of class objects. Here we will consider these further and more systematically. We shall also see how to check the qualification of a class object.
When a class object is generated it posesses all the attributes of the class whose name is given in the object generator. This includes any visible attributes from classes on its prefix chain, following the rules given above concerning name clashes.

The type of such an object is the class specified and this is called its qualification. It can also be thought of as being qualified by the classes on its prefix chain, except that not all the attributes of these may be visible.

A variable which is declared as a ref to a class which is the qualification of an object or is on the prefix chain of its qualifying class may be used to access that object. The type of the reference variable used controls how much of the prefix chain may be so accessed.

It is only legal to treat an object which is being remotely accessed as if it was qualified by the class of the referencing variable. Thus, in example 11.1, when New\_Block is set to denote a new Text\_Block only the attributes declared in Print\_Block, which prefixes Text\_Block, may be accessed through New\_Block. This is because New\_Block is declared as a ref(Print\_Block) not a ref(Text_Block) variable. This means that the statement

      NewBlock.Count := 3
would be illegal.
Breaking any of these rules will cause the SIMULA system to report an error, either during compilation or at runtime. Some extra features of SIMULA can be used to circumvent these restrictions, as we shall see later, but these checks provide an important safeguard against serious errors which could occur otherwise.

###Checking the qualification of an object

In addition to checking whether or not two reference variables point to the same object (reference equality and inequality), SIMULA also allows us to check the qualification of any referenced object. To do this we use the reference comparators is and in.
The operator is checks whether an object is qualified by a particular class. It will only give the value True when the object's innermost class matches that with which it is compared. Thus

      Obj is ThisClass
will only give True if the object referenced by Obj was generated by
      new ThisClass.
The operator in makes a less strict check. It will give True if the object is either of the specified class or has that class on its prefix chain. The use of is and in is shown in example 11.5.
Example 11.5: Use of is and in.

         begin
            class A;
            begin
               integer I;
            end--of--A;

            A class A1;
            begin
               integer K;
            end--of--A1;

            A class A2;
            begin
               integer L;
            end--of--A2;

            ref(A) Obj1,Obj2,Obj3,Obj4;

            Obj1 :- new A;
            Obj2 :- new A1;
            Obj3 :- new A2;

            for Obj4 :- Obj1,Obj2,Obj3 do
            begin
               if Obj4 is A then OutText("Object is A") else
               if Obj4 in A then
               begin
                  OutText("Object in A");
                  if Obj4 is A1 then OutText(" and is A1")
                                else OutText(" and is A2")
               end else OutText("Object is not A and is not in A");
               OutImage
            end
         end**of**program
###Exercise

11.6 Rewrite the text processing program so that it recognises the directive $A. This is followed by a text sequence which is to be printed after all the other blocks. In every other way it is treated as a Text_Block. Use is to test the blocks and print them in the correct order.
###Some extra features for Boolean expressions

As we have often seen, comparisons are what make it possible to write most of our useful programs. You will probably have begun to realise that making the right test in the right place is vital to success in programming. To help with this, SIMULA provides facilities for making more than one comparison in a single begin statement, while loop or whatever. These facilities allow us to write neater, more compact code. At the same time they make it even more important to check when we use them that the right tests are being applied.
A comparison is more formally called a "simple Boolean expression", giving the value True or False. The keywords True and False are themselves simple Boolean expressions, with a constant value. Boolean variables and Boolean procedures, like LastItem in a text, are also simple Boolean expressions.

Thus a Boolean expression is a sequence of SIMULA which can be evaluated to True or False.

###not

The simplest extension to a simple Boolean expression is the Boolean operator not. The keyword not reverses the value of the Boolean expression which follows immediately after it. The Boolean expression
      3 = 2
has the value False, and so the expression
      not 3 = 2
has the value true.
###and

When two Boolean expressions are linked by the operator and, the value of the combined Boolean expression is False unless both the linked expressions have the value True.
The expression

      3 = 2 and 4 = 4
has the value False, since one of its sub-expressions has the value False. The examples in 11.6 show the other possibilities.
Examples 11.6: The use of and.

      a) 4=4 and 7=7;   ! Value is True
      b) 2=2 and 6=8;   ! Value is False
      c) 10=1 and 2<0;  ! Value is False
Examples 11.7: The use of or.
      a) 3=2 or 4=4;   ! Value is True
      b) 4=4 or 7=7;   ! Value is True
      c) 2=2 or 6=8;   ! Value is True
      d) 10=1 or 2<0;  ! Value is False
Examples 11.8: The use of eqv
      a) 3=2 eqv 4=4;   ! Value is False
      b) 4=4 eqv 7=7;   ! Value is True
      c) 2=2 eqv 6=8;   ! Value is False
      d) 10=1 eqv 2<0;  ! Value is True
Examples 11.9: The use of imp.
      a) 3=2 imp 4=4;   ! Value is True
      b) 4=4 imp 7=7;   ! Value is True
      c) 2=2 imp 6=8;   ! Value is False
      d) 10=2 imp 2<0;  ! Value is True
###or

Two Boolean expressions linked by the operator or form a combined Boolean expression which has the value True if either of the sub-expressions has the value True. Only where both have the value False does the combined expression have the value False.
It is easy to confuse the operator and with the operator or in ordinary speech, since we often use these words very loosely. You should take great care to get your meanings precise in SIMULA.

The examples in 11.7 show the possible combinations.

###eqv

Two Boolean expressions linked by the operator eqv form a combined Boolean expression which has the value True if both sub-expressions have the same value and has the value False if the sub-expressions have different values. eqv is an abbreviation for "equivalent to".
The examples in 11.8 show the possible combinations.

###imp

imp is also used to link two Boolean expressions to form a combined expression. Its meaning is only of interest to formal mathematicians, but I include it for completeness. The possible combinations using it are given in examples 11.8.
imp is an abbreviation for "implies".

###Summarising Boolean operators

Table 11.1 show a summary of the effects of the various Boolean operators. B1 represents the first Boolean sub-expression, B2 the second. This sort of table is often called a "truth table".
Table 11.1: Effects of Boolean operators.

   B1 =	TRUE	TRUE	FALSE	FALSE
   B2 =	TRUE	FALSE	TRUE	FALSE
   B1 AND B2 =	TRUE	FALSE	FALSE	FALSE
   B1 AND THEN B2 =	TRUE	FALSE	FALSE	FALSE
   B1 OR B2 =	TRUE	TRUE	TRUE	FALSE
   B1 OR ELSE B2 =	TRUE	TRUE	TRUE	FALSE
   B1 EQV B2 =	TRUE	FALSE	TRUE	FALSE
   B1 IMP B2 =	TRUE	FALSE	TRUE	TRUE
   NOT B1 =	FALSE	FALSE	TRUE	TRUE
   NOT B2 =	FALSE	TRUE	FALSE	TRUE
   Combining Boolean operators

Boolean operators can be combined to form more complicated expressions. The rules for evaluating Boolean expressions are then as follow. All working out is done from left to right.

   1. All the comparisons are performed and the values True or False put in their places.
   2. All not operations are carried out.
   3. All and operations are carried out.
   4. All or operations are carried out.
   5. All imp operations are carried out.
   6. All eqv operations are carried out.
Example 11.10 shows this sequence for an unusually complicated sequence.
###Bracketing

Parentheses may be placed around any sub-expression. This has the effect of forcing the value of that sub-expression to be fully evaluated before the rest of the main expression and so can override the order given above. This is equivalent to the use of parentheses in arithmetic expressions.
Example 11.11 shows the effects of bracketing on parts of 11.10. Note that the innermost brackets' contents are evaluated first and so on.

Example 11.10: Evaluation of complex Boolean expressions.

         3=2 or 7=7 and 9<10 eqv 7>4 imp not 23=23

   1) False or True and True eqv True imp not True
   2) False or True and True eqv True imp False
   3) False or True eqv True imp False
   4) True eqv True imp False
   5) True eqv False
   6) False
Example 11.11: Evaluation of complex Boolean expression with bracketing.
         3=2 or (7=7 and (9<10 eqv 7>4)) imp not 23=23

   1) False or (True and (True eqv True)) imp not 23=23
   2) False or (True and (True)) imp not True
   3) False or (True) imp not True
   4) False or True imp False
   5) True imp False
   6) False
###Hints on using Boolean operators

The use of and and or in particular can make programs shorter and easier to read. The sequence
      if I=J then
      begin
         if K>L then
can nearly always be replaced by
      if (I=J) and (K>L) then
which seems a lot clearer, to me at least. (The reason for saying "nearly always" will be explained below.)
The use of parentheses, even when, as above, they are not strictly necessary, can also make it easier to see which sub-expressions are linked by which operators. It is not always easy to remember which of and and or will be evaluated first, but it is easy to remember that bracketed expressions are evaluated before others. The expressions

      I=J and K<L or I<L and K=J
and
      (I=J and K<L) or (I<L and K=J)
have exactly the same value, but the second is much clearer.
Finally, a warning; do not try to be "clever" in your use of Boolean expressions. Keep them as straightforward and clear as possible. Sometimes it may be better to use two nested begin statements rather than a convoluted Boolean expression, keeping the program's meaning clear at the cost of making it slightly longer.

###Side effects and how to avoid them

I hope I am not over emphasising Boolean expressions. They are often the key to a successful program. They are certainly the cause of many errors in programs which may be very difficult to locate if we do not take great care to use them sensibly. Most of us will find and, or and not very useful and it is worth looking at one problem with the first two which is rare, but very hard to find when it does occur.
In addition to or and and, SIMULA has operators or else and and then. These produce the same value as their simpler counterparts, as table 11.1 shows. They are included to allow programs to be written which run faster, at the expense of introducing the possibility of unpredictable side effects from procedure calls. In most cases and then is interchangeable with and, while or else is interchangeable with or.

The difference is that when two sub-expressions are linked by and or by or, both are evaluated, regardless of the value of the first. Yet, with and, when the value of the first sub-expression is False, we know without having to evaluate the second that the value of the two anded together must be False. Similarly with or, if the first sub-expression gives True, the overall value will always be True. The evaluation of the second sub-expression is unnecessary in these cases.

The use of and then prevents the evaluation of the second sub-expression if the first is False. The use of or else prevents it if the first is True. This may make the program run faster. It also prevents any procedures which form part of the second sub-expression being called.

It is the second effect which can cause problems. If the calling of a type procedure in the second sub-expression affects values which are used elsewhere in the program, the effect of skipping its evaluation could be disastrous. If you must write such programs, you must use the simpler forms, even though they will result in slower running.

Example 11.12 shows a program with side effects. Running it and supplying first a sequence not containing 99 and then one which does contain it demonstrates the problem. It may seem "smart" to write like this. It is certainly an interesting technique. It is even more certainly a very unsafe one.

###Exercise

11.8 What are the values of the following expressions?
4=3 or 0>2 and 3=3
(4=3 or 0>2) and 3=3
4=3 or (0>2 and 3=3)
###Summary

This chapter has dealt with sub-classes, qualification and Boolean operators.
We have seen how to use classes as prefixes to form families of sub-classes, which extend their parent's attributes.

We have learned the rules governing this concatenation, including the order of execution of actions.

We have seen how the qualification of a class object and its prefix chain control both the objects which may be pointed to by references of particular class types and the attributes which may be accessed remotely using such references.

The use of inner to allow epilogues of actions in prefixing classes has been demonstrated.

The use of Boolean operators to extend the power of Boolean expressions has been shown, along with some dangers associated with their use.

Example 11.12: Unsafe side effects in a complex Boolean expression.

      begin
         integer I,J,K;

         Boolean procedure SideSwipe;
         begin
            J := J + 1;   ! J is updated here;
            SideSwipe := I>0
         end..of..SideSwipe;

         K := InInt;
         while J<K do
         begin
            I := InInt;
            comment The first test could interfere with the updating of J;
            if I NE 99 and then SideSwipe then OutText("Greater")
                                          else OutText("Less")
         end;

         OutImage
      end**of**program

{{ book.Chapter12 }}