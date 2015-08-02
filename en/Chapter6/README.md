#CHAPTER 6 - Correct Procedures

###Building blocks

We have written some programs which do real work, but at the moment they have to be run separately, with the input provided for each run. It is not obvious how we can use such separate programs to build a large, flexible piece of software.
One method would be to combine all the small programs that we write into one large program, using each program in the right order. We could use our multiple space removing program, followed by our line breaking program and so on. This is fine for programs that only need to be used in one place, but if the same function is required in several places we will need to duplicate the instructions each time. This is inefficient, making our programs much longer than they need to be. It can also make it tedious to follow the overall working of a large program, since a mass of detailed instructions tend to obscure the important features of the design.

As an example of a program which could be used in many places in our package, we might take the text concatenation program. In fact this is such a useful thing to be able to do that we might wish that a language feature existed for it. In fact, such a facility is now provided as part of SIMULA. Unfortunately, it is not yet available in most systems. We shall see it in chapter 8. Fortunately, we are able to define our own procedures, which we can then use wherever we like inside the block where we declare them. In fact we can even build a library of our favourite procedures and use it in all our programs.

###Declaring procedures

It is clearly not enough to declare a procedure in the way we declare an integer. The declaration

      procedure Concatenate
cannot magically tell the SIMULA system what we want Concatenate to do. We must also supply the actions to match the name. Here is a valid procedure declaration:

           procedure PrintName;
           OutText("Alice");
The syntax of the simplest procedure declaration is the keyword procedure, followed by the identifier to be used for the procedure, followed by a semi-colon, followed by a statement.
The statement following the semi-colon is known as the procedure body and specifies what is to be done each time the procedure is invoked or "called" in the subsequent program. Calling the procedure is done by using its identifier as a statement in the program, exactly as we call system procedures.

Example 6.1 shows the use of our procedure, PrintName.

Example 6.1: Simple procedure use.

        begin
        
           procedure PrintName;
              OutText("Alice");

           Concatenate;
           OutImage
        end
Note the use of blank lines to make it easier to see where the procedure begins and ends. These are not compulsory, but make the program more readable to humans.
We would normally want to have more than one statement in our procedure body. We can achieve this by using a compound statement or a block. Example 6.2 is our Concatenate program from chapter 5 used as a procedure.

Example 6.2: Concatenate as a procedure.

                begin
                   procedure Concatenate;
                   begin
                      text T1,T2,T3;
                      InImage;
                      inspect SysIn do
                      begin
                         T1:-Copy(Image.Strip);
                         InImage;
                         T2:-Copy(Image.Strip);
                         T3:-Blanks(T1.Length + T2.Length);
                         T3:=T1; T3.SetPos(T1.Length+1);
                         while T2.More do T3.PutChar(T2.GetChar);
                      end--of--inspect--SysIn;
                      OutText(T3);
                      OutImage
                   end;
                   Concatenate
                end
###Parameters to procedures

This Concatenate procedure only joins texts which are read in by it. Having done so, it merely prints out the result. This would be much more useful if we could use it to combine any two texts in our programs and to assign the result to any text we chose. This can be done by specifying parameters in the declaration.
We have already seen how parameters can be used to pass values to system procedures. Example 6.3 shows how to declare two texts as parameters to the Concatenate procedure.

Example 6.3: Concatenate with parameters.

                begin
                   procedure Concatenate(T1,T2); text T1,T2;
                   begin
                      text T3;
                      T3:-Blanks(T1.Length+T2.Length);
                      T3:=T1;
                      T3.SetPos(T1.Length+1);
                      while T2.More do T3.PutChar(T2.GetChar);
                      OutText(T3);
                      OutImage
                   end;
                   text Text1,Text2;
                   Text1:-"Fred";
                   Text2:-"Smith";
                   Concatenate(Text1,Text2)
                end
This passes in our texts, T1 and T2, which are now used as parameters to the procedure. The identifier given for the procedure in its declaration is followed by a list of all the parameters to be used, enclosed in parentheses and separated by commas. The declaration

      text T1, T2;
following the semi-colon and before the begin is known as the type specifier and gives the type of each of the parameters.
Where more than one type of parameter is to be used, more than one type declaration must be given. 6.4 is an example, using one text and one integer parameter.

Example 6.4: A procedure with more than one type of parameter.

                begin
                   procedure TextAndInt(T,I); text T; integer I;
                   begin
                      OutText(T);
                      OutInt(I);
                      OutImage
                   end;
                   TextAndInt("NUMBER",10)
                end
When execution of a procedure is complete all quantities declared inside it are lost. This includes the final values of its parameters. The remaining question is how to get back the text containing the concatenated texts once Concatenate is complete. There are two ways of doing this in SIMULA and the first of these uses a different sort of parameter.
###Parameter modes

The way of specifying parameters that we have used so far will always work for passing values into a procedure. If we want to get information out, we may have to add a mode specifier for some parameters.
This sounds confusing, but is easy to follow in practice. Here is a final version of Concatenate.

Example 6.5: Using a name parameter to return a result.

                begin
                   procedure Concatenate(T1,T2,T3);
                   name T3; text T1,T2,T3;
                   begin
                      T3:-Blanks(T1.Length + T2.Length);
                      T3:=T1;
                      T3.SetPos(T1.Length + 1);
                      while T2.More do T3.PutChar(T2.GetChar);
                   end;
                   text Text1,Text2,Text3;
                   Text1:-"Fred";
                   Text2:-"Smith";
                   Concatenate(Text1,Text2,Text3);
                   OutText(Text3);
                   OutImage
                end
Notice that, as well as specifying that T3 is of type text, we have specified that it is name. name is not a type but a mode. When a parameter is defined as of name mode, any assignments to it alter the value of the variable actually passed in the call, rather than a local copy, as would have happened with the other parameters, which are passed by value.
In fact there are three modes; value, reference and name.

Where a mode specifier is not given for a parameter, a mode of value or reference is assumed, depending on its type. Some modes are illegal for certain types. Table 6.1 is a complete table of the assumed (usually referred to as default), legal and illegal modes for parameters to procedures.

      Type                  Mode      Value   Reference Name

      Simple type           Default   Illegal   Legal
      text                  Legal     Default   Legal
      Object reference      Illegal   Default   Legal
      Simple type array     Legal     Default   Legal
      Reference type array  Illegal   Default   Legal
      procedure             Illegal   Default   Legal
      type procedure        Illegal   Default   Legal
      label                 Illegal   Default   Legal
      switch                Illegal   Default   Legal

      Table 6.1: Modes of transmission for parameter types.

A simple type is integer, real, character or Boolean and any long or short variants of them.

Now we can see that name is always legal and reference is the default for all but simple types. Do not worry about the meaning of those types which are new. We shall consider their use when we encounter them.

###Value parameters

A value parameter to a procedure acts as if it were a variable of that type declared in the body of the procedure. The value passed to it when the procedure is called is copied into it as part of the call statement. Since values declared inside a block cannot be used outside that block, the value of this mode of parameter is lost on returning from the procedure.
When calling a procedure, any value of the correct type may be passed to a value mode parameter. Thus constants, expressions and variables are all allowed.

To see the effect of passing a parameter by value, consider example 6.6.

Example 6.6: Passing parameters by value.

                begin

                   procedure P(Val); integer Val;
                   begin
                      OutInt(Val);
                      OutImage;
                      Val := Val - 1;
                      OutInt(Val);
                      OutImage
                   end..of..P;

                   integer OuterVal;
                   OuterVal := 4;
                   P(OuterVal);
                   OutInt(OuterVal);
                   OutImage
                end
The value in OuterVal, 4, is copied into the parameter Val's location when P is called. Thus the first number printed will be 4.
When 1 is subtracted from Val, OuterVal is not changed. Thus the second number printed is 3, but the third is 4.

When a text is passed by value to a procedure (N.B. this is not the default) it has the effect of creating reference to a local text frame with the same length as the text passed, into which the characters from the latter text are copied. Consider example 6.7.

OutLine is actually quite a useful procedure. Note that in order to pass our text parameter by value we have to give a mode specification for it, using the keyword value.

When the procedure is called, the parameter T is initialised as if the following statements had been executed.

                T :- Blanks(OuterT.Length);
                T := OuterT
Example 6.7: Text parameter passed by value.
                begin
                   text OuterT;

                   procedure OutLine(T); value(T); text(T);
                   begin
                      OutText(T.Strip);
                      OutImage
                   end..of..OutLine;

                   OuterT:-"Here's a line";
                   OutLine(OuterT)
                end
###Reference parameters

When a parameter is passed by reference, the local parameter points at the location holding the object passed, rather as if the :- reference assignment operator had been used. No local copy is made of the contents of the object.
For every reference parameter type except text, this explanation is sufficient and should be reconsidered for its meaning when those types are encountered.

As we have seen, when a text is assigned by reference new copies of Pos, Length etc. are made, but the same actual text frame is referenced. Pos, Length etc. will have the same values as those for the original reference, but will not change if the originals do.

As far as the passing of text parameters by reference is concerned the following effects occur:

The characters in the frame referenced by the parameter may be changed by the procedure. Since this is the same actual location as the frame of the reference which was copied, the contents of the frame remain changed when execution of the procedure is complete.
The other attributes have local versions created, with the same values as those current for the parameter. When those other attributes are changed for the parameter, they remain unchanged for the original. Thus, any changes to these is lost when execution of the procedure is complete.
Try rewriting the Concatenate procedure with all the parameters passed by reference. What would be the effect on running the program using it now?

You should find that it fails since the Length of Text3 cannot be changed by manipulating T3 inside the procedure. The only way to get this program to work would be to set the length of Text3 before calling the procedure, as shown in example 6.8.

Note that as reference mode is the default for all types where it is legal, it is never necessary to give a mode specification for reference parameters. Thus there is no keyword reference to match value and name.

Example 6.8: Concatenate using only reference mode parameters.

                begin

                   procedure Concatenate(T1, T2, T3); text T1, T2, T3;
                   begin
                      T3 := T1;
                      T3.SetPos(T1.Length + 1);
                      while T2.More do T3.PutChar(T2.GetChar);
                   end**of**Concatenate**by**reference;

                   text Text1, Text2, Text3;

                   Text1 :- "Fred";
                   Text2 :- " Smith";
                   Text3 :- Blanks(Text1.Length+Text2.Length);
                   Concatenate(Text1,Text2,Text3); 
                   OutText(Text3);
                   OutImage
                end
###Name parameters

Name parameters are very powerful, but complex. It is sometimes possible to make serious errors using them, through failing to consider all possile outcomes of their use.
When a variable is passed by name, its use within the procedure has the same effect as when that variable is used outside the procedure. Thus any actions on the parameter inside the procedure directly affect the variable passed in the call. This is obviously a suitable mode for getting values back from a procedure, as we have seen.

This contrasts with the use of reference mode, where the contents of what a variable points at are changed, but the variable still points at the same location. If a reference assignment is made to a name parameter, it is actually made to the variable passed originally, not a local copy.

Example 6.5 returned the concatenated texts in the name parameter T3. When the procedure was called, the variable Text3 was passed as this parameter and when the statement following the call was executed, Text3 contained the combined texts. There is one statement missing from this Concatenate. It is needed because the Pos, Length and other attributes of Text3 will be changed by the procedure, when it manipulates T3.

What is this missing line? See if you can work out what it is before reading the polished version below.

Example 6.9 is the version of Concatenate which we can use in all our programs.

Example 6.9: Finished version of Concatenate.

                begin

                   procedure Concatenate(T1, T2, T3);
                   name T3; text T1, T2, T3;
                   begin
                      T3 :- Blanks(T1.Length + T2.Length);
                      T3 := T1;
                      T3.SetPos(T1.Length + 1);
                      while T2.More do T3.PutChar(T2.GetChar);
                      T3.SetPos(1); ! Did you get this right?;
                   end**of**Concatenate;

                   text Text1, Text2, Text3;

                   Text1 :- "Fred";
                   Text2 :- " Smith";
                   Concatenate(Text1,Text2,Text3);
                   OutText(Text3);
                   OutImage
                end
The missing statement must reset the position within T3 to the start of the characters it now contains, since it is left pointing to their end.
Note that, since name mode is never a default for any type, the mode specifier name must be used in a mode specification for any parameters which are to be used in this way.

It is worth mentioning that all parameters passed by name are re-evaluated each time they are used inside the procedure. This is important in some cases since actions inside the procedure may change the value of an expression passed in this way, while expressions passed by value or reference are evaluated and their values copied into the local variables specifying those parameters, once and for all, at the call. Try compiling and running example 6.10 to see the difference.

Note also that while it is legal to pass expressions by name in this way, an attempt to assign to a name parameter when an arithmetic expression like those in 6.10 or anything else which is not a valid left hand side has been passed will cause a runtime error. The general rule is that the exact text of what is passed replaces each occurence of the name parameter within the procedure.

Example 6.10: Expressions by name and by value.

      begin
         procedure Use_Name(Val1, Val2); name Val2; integer Val1, Val2;
         begin
            OuterVal := 3;
            OutInt(Val1,4);
            OutInt(Val2,4);
            OutImage
         end..of..Use_Name;

         integer OuterVal;
         OuterVal := 5;

         Use_Name(OuterVal+3,OuterVal+3)
      end++of++program
###Exercises

6.1 Write a program which uses a procedure to find the larger of two numbers and returns it in a name parameter.
6.2 Write a program which uses our multiple space removal program as a procedure. Devise a series of tests to cover all the cases you can think of which this procedure might have to cope with and try them. Do not use lots of tests which all check the same thing, but try to use one good test for each possibility.

6.3 Write and test a procedure which takes two text parameters and Concatenates them, returning the result in the first one.

6.4 Write and test a procedure which takes two characters and moves the value in the first into the second and the value in the second into the first.

6.5 Write and test a procedure which counts the number of non-space characters in a text.

###Functions

There is another way to return values from procedures to the calling block. We have already seen examples of system procedures which use this mechanism in GetChar and InInt. Now let us see how to write procedures for ourselves.
Example 6.11 is an example of such a procedure which adds two numbers together and returns the result. Such a procedure is usually referred to as a "type procedure" or, most commonly, a "function".

This is a very simple example, but it shows how to declare and use such a procedure. The declaration is just like that of any non-type procedure, except that a type specifier, integer in this case, is given before the keyword procedure. This is the type of the value that is to be returned by a call on this procedure.

Within the procedure body the name of the procedure may be used as a variable of the type specified for the procedure, but only on the left hand side of assignment statements. If you use it anywhere else it is regarded as a further call on the procedure by itself. This idea of "recursive" calling is looked at later in this chapter. When the program returns from a call to the procedure, the last value assigned to the procedure identifier is the value returned. This value may be assigned to a variable (as in example 6.11), used as a parameter or used as a value in an expression.

In example 6.11 there is only one statement in the procedure body and this assigns the total of the two parameters to the procedure identifier. The normal type rules apply. The result of the procedure call will be this value, which is here assigned to the variable Result as part of the calling statement in the main program.

If no assignment is made to the procedure identifier, the initial value for the type of the procedure, as defined in chapter 3, is returned.

A type procedure may be called as if it was a typeless procedure and its returned value ignored. This is often the case with Open, a system procedure described later.

Example 6.12 is Concatenate as a text procedure and used as a parameter to OutText.

Example 6.11: A simple type procedure or function.

                begin

                   integer procedure Add(Val1, Val2);
                                integer Val1, Val2;
                      Add := Val1 + Val2;

                   integer Result;

                   Result := Add(2,3);
                   OutInt(Result);
                   OutImage
                end
Example 6.12: Concatenate as a text procedure.

          begin

             text procedure Concatenate(T1, T2); text T1, T2;
             begin
                text LocalText;
                LocalText :- Blanks(T1.Length+T2.Length);
                LocalText:= T1;
                LocalText.SetPos(T1.Length + 1);
                while LocalText.More do
                   LocalText.PutChar(T2.GetChar);
                LocalText.SetPos(1);
                Concatenate :- LocalText; ! Now assign text as the result;
             end--of--Concatenate--as--a--function;

             OutText(Concatenate("PART ONE"," PART TWO"));
             OutImage
          end
###Exercises

6.6 Write and test a function which returns the amount of tax payable on an income and which takes three parameters - the tax free allowance, the rate of taxation and the amount earned.
6.7 Use the function to write a program which calculates the tax on a person's income, assuming the following rates.

        Tax free allowance for single person = #2000
        Tax free allowance for married person= #3500
        Tax rate for first #10000            = 30%
        Tax rate for next  #5000             = 45%
        TAx rate for next  #5000             = 60%
        Tax rate above this                  = 75%
What is the smallest set of test data needed to test this fully?
6.8 Write and test a program to find the occurence of the sequence "and" in a text. Extend this to provide a function to search for any given sequence, returning the position in the text where it is found.

6.9 Using the procedure from 6.8, write a program to replace the next occurrence of a sequence with any other given sequence.

6.10 Using 6.8 and 6.9 to provide procedures to locate and replace sequences in texts, write a simple editor which does the following.

Reads in a text.
Reads in and performs the following commands:
                F/sequence/ - find a sequence in the text and report success
                              or failure.
                R/sequence2/ - replace the sequence just located with a new
                               one.
                S - move back to the start of the text.
                E - end further command processing and print the final text.
###Recursive procedures

A procedure must be declared inside a block. Like any other declared item program, it may be used anywhere within that block. This includes using it within blocks and procedures which are also within that block. In a block which has more than one procedure declared within it, any one of these procedures may be called inside any or all of the others, even those which are declared before itself. This is known as the scope of such a declaration.
This has one rather important implication. A procedure may make a call on itself. This as known as a "recursive" call. 6.13 is an example of a program which uses a recursive procedure call.

When this happens each call creates a new incarnation of the procedure. Thus each has its own versions of the parameters and declared items for that procedure. In each, these items start with the value passed to them, for parameters, or at the initialisation value for their type (see chapter 3), not the value of their equivalent in the calling procedure.

Example 6.13: A recursive procedure call.

        begin

           integer procedure Non_Blank(Text1); text Text1;
           begin
              if Text1.GetChar NE ' ' then Non_Blank := Text1.POS - 1
                                      else Non_Blank := Non_Blank(Text1);
           end OF Non_Blank;

           integer New_Start;

           InImage;
           T :- SysIn.Image;
           New_Start := Non_Blank(T);
           T :- T.Sub(New_Start, T.Length-New_Start);
           OutText(T);
           OutImage
        end
Example 6.13 shows the use of recursion and emphasises why the procedure identifier of a function has a different meaning when used as the left hand side of an assignment than in any other use. This program removes the blanks at the start of a text, but uses recursion instead of a while loop. Here is a quick "walkthrough" of Non_Blank.
First note that this is an integer procedure. It will return the position of the first non-space character in the text passed to it as a parameter. Next note that the parameter is passed by reference, the default for text. These are the things to check in any procedure when you are trying to work out its use.

When Non_Blank is called it reads the next character of the text and compares it with the space character. If it is not a space then the first non-space character is the one just read. Thus the required result is the current Pos of the text parameter minus one, since we have gone one character past it by calling GetChar.

If the character read is a space, Non\_Blank is called again and the text is passed again. Since the mode is reference, the current Pos, Length etc are passed on, so that on this call Non\_Blank starts from the character after that just read. Thus Non\_Blank calls itself, updating the position in the text by one each time, until a call finds a non-space character. This call returns the position of this character, as we have seen. Each preceding call passes the value returned back as its result through the assignment

      Non_Blank := Non_Blank(Text1)
showing clearly the two uses of Non_Blank as left side of an assignment, returning the value for the function, and as recursive call, with parameter.
Try working through for yourself using texts with various numbers of spaces at their start. Once you have seen how it works for a couple of examples you should get the idea.

###Text function results and recursion

A text procedure returns a text reference. Thus only reference assignments to the procedure identifier, witthin the procedure body assign a new result. Value assignments merelyalter the contents of the text frame of the currently assigned reference.
When the procedure identifier occurs on the right hand side of an assignment the effect is independent of whether it is a reference or value assignment. Such an occurence is always taken as a recursive call.

###Dangers of recursion

It is not necessary to have a type procedure to use recursion. There are many instances when non-type procedures can use recursion. It is a vey powerful device. Example 6.14 uses a non-type procedure . It shows the power of recursion and the possible problems of using it without proper thought.
Example 6.14: The danger of non-terminating recursion.

      begin

         procedure Numbr(Tex, Num); Text TEX; integer Num;
         begin
            OutText(TEX);
            OutInt(Num);
            OutImage;
            Numbr(Tex, Num + 1)
         end OF Numbr;

         Numbr("Line no ",1)
      end
Can you spot the problem? For once I am not going to suggest that you try running the program, especially if you are running on a batch machine. This sort of mistake can cost a lot of paper.
If you are not sure what is wrong, look carefully at the call on Numbr inside the body of Numbr. Now try following through the working of the program. When will recursive calls stop being made? In the Non_Blank procedure in example 6.13, recursive calls stopped when a non-space character was found. More importantly they only happened when a space was found. Just like while loops, recursive calls must stop at the right point. They must not go on for ever.

In general, recursive calls must only be made as part of an if statement or an if-then-else statement and the condition for their being called must become false at some point. This requires some care, but is no more of a problem than writing while loops which stop at the desired point. In fact recursive calls and while loops are very similar.

###Summary

We have seen how to declare and call simple procedures, with and without parameters.
We have seen how to specify the types of any parameters to a procedure.

We have seen the meaning of the modes of parameter transmission and how to specify these.

We have learned the default modes for all the possible types of parameters to procedures.

We have learned how to declare and use type procedures or functions.

We have learned how to use simple recursion.

{{ book.Chapter7 }}

###Exercises

6.11 Use a recursive procedure to write a program which scans a text for a occurences of a sequence of characters and replaces them with another.
6.12 Use a recursive procedure to write a program which reads in lines of text and prints them out with a line number. Set the program to stop when the line

.end
is found.