#CHAPTER 4 - if only

##Conditional statements

###What do computers do?

It may seem a little late in this book to ask such a question, but we have really taken for granted what computers are and what they can do. if we are to use them as fully as possible, we need to understand exactly what they are capable of.
Most people probably used to think of computers as giant, superfast calculating machines. More recently the idea of very small micro-computers has changed that Image somewhat. More importantly, the use of computers in word processing and graphics (particularly computer games) has introduced many more people to the use of computers in information processing and decision making.

As programmers we can consider computers as performing three sets of functions, which can be combined in our programs to achieve a wide range of tasks.

  1. Movement of information - reading a magnetic tape and printing on a lineprinter; reading the input from a keyboard and storing in random access memory; reading the input from a "joystick" and moving a picture on a screen.
  2. Arithmetic calculations - we have seen some examples of this already.
  3. Comparison of information and choice of actions depending on the result - this chapter will look at the basic mechanisms for this.

###Conditions and choices

The ability that makes computers more than calculators or fancy typewriters is the ability to perform different actions according to some condition or conditions, which it can determine to be either true or false. Computers can be told, "Check this condition. If it is true then do the following, otherwise do this other thing". In some cases the other thing is nothing. Put crudely, computers can make choices.
What they cannot do, or, at least, as far as the author is aware, not yet, is decide which condition to test, or whether the actions which follow are really sensible. They must be told these things and programming languages have mechanisms for doing so.

In SIMULA, the most important construction for making choices is the "conditional statement" or, as it is often known, the if statement.

###What is an if statement?

Example 4.1: Simple use of an if statement.
                begin
                   integer Int1;
                   Int1:=InInt;
                   if Int1=2 then OutText("YES");
                   OutImage
                end
This program will read in a whole number and compare its value against 2. If it is equal to 2 then the program will print YES, otherwise a blank line will be printed. Compile and run it to make sure.

From this example we can see the syntax of an if statement.

An if statement starts with the keyword if, followed by a condition, followed by the keyword then, followed by a statement.

The semantics are probably obvious to you as well.

The program checks the condition. If it is true, the statement is executed (or carried out, if you prefer), but if it is false, the statement is skipped. The next statement, if there is one, is then executed.

An if statement may be used wherever a simple statement may be used.

###The if-then-else statement

Consider example 4.2 which uses if statements.
Example 4.2: Un-combined if statements.

                begin
                   integer Int1;
                   Int1 := InInt;
                   if Int1=2 then OutText("YES"); 
                   if Int1 ne 2 then OutText("NO");
                   OutImage
                end
Here we have added a second if statement to our first example, but all that it does is check the opposite condition to the first. ne is the symbol for "not equal" in SIMULA. This program will print out YES if the number read in is 2 otherwise it will print out NO. Again, try it and see.

This might seem a useful device, but it is wasteful since the program makes the same check - is the number equal to 2 - twice and decides its actions on the basis of whether the outcome is true or false. In practice this combination is so useful that SIMULA provides a less wasteful means of achieving the same result. Rewriting our example using this concept we get example 4.3.

Example 4.3: The if-then-else statement.

                begin
                   integer Int1;
                   Int1:=InInt;
                   if Int1=2 then OutText("YES")
                             else OutText("NO");
                   OutImage
                end
###A "real" program

So far we have used only trivial examples to demonstrate features of SIMULA. None of them has had a purpose apart from that. Although writing and correcting such programs is interesting for a while, it soon becomes boring. We are now going to begin to construct a series of programs which have a very real and very practical purpose. If you complete this book you will have built the basis of a suite of word processing programs, which will allow simple editing and formatting of text files. You may then extend these to provide some very powerful tools. We shall also look at some simple database tools, which will allow you to store and retrieve information efficiently.
The important thing about writing large programs which perform complex functions is to break the design into sections which perform sub-tasks and which are simple enough to write easily. Our next examples introduce simple programs, although ones which are more complex than those we have seen so far. On its own each is not very useful, but later on we will use it as a building block in our much more powerful programs.

###A "top down" design

Soometimes, in explaining the programs we wish to write, we start from simple components and work towards the whole system. This approach is known as "bottom up" design, for fairly obvious reasons. Where we are only designing a small part of our "grand design" this approach is sufficient. There are not too many components to hold in our heads as we build up the program.
When the system we are designing gets bigger, this approach is not good enough. It would be very difficult to write down now all the parts of our formatting and editing system in this kind of detail, especially as we have not said exactly what we want the system to do. It is much more sensible to start at the highest level, with our overall system, and break this down step by step until we reach components which are simple enough to write in SIMULA in detail. This approach is called "top down" design.

Here is a quick sketch of the design of our total system, concentrating on the line formatting programs we are about to write. This shows a major advantage of top down design: since the low level components are designed to operate as independently as possible, we can write and test them separately and then build up our overall system by combining tested units. The only important parts of each component are its function - what it does - and how it receives and passes on information from and to other components - its "interface".

Figure 4.1: Overall top down design of Formatter/Editor

                      EDITOR/FORMATTER
                      ----------------
          ___________________I________
          EDITOR             FORMATTER
          ------             ---------
               __________________I___________________________
               CONTENTS  PREFACE         CHAPTERS       INDEX
               --------  -------         --------       -----
               _____________________________I______
               TITLE                          PAGES
               -----                          -----
                 _______________________________I______________
                 HEADING        PARAGRAPHS            NUMBERING
                 -------        ----------            ---------
              _______________________I_______
              INDENTING                 LINES
              ---------                 -----
   _______________________________________I____________________
   CLEANING   LEFT MARGIN   LENGTH   RIGHT JUSTIFYING   SPACING
   --------   -----------   ------   ----------------   -------
 ______I______
 DOUBLE SPACES
 -------------
This type of design allows us to begin work now, without losing sight of the overall plan. It is also possible to design components now and improve on them later if we discover better ways of implementing them. Most importantly this form of design makes it easier to change parts of the system without upsetting other parts, since the degree of interdependence of components can be seen easily.
We shall fill in other branches as we learn more of SIMULA's capabilities. In fact we shall not finish the system. No piece of software is ever so good that extra or better features might not improve it. You should be able to add such refinements for yourself before we finish.

One last point about top down designs: in reality it is impossible to start with a complete plan, along the lines shown, and write the final program from it. The process of implementing the program in SIMULA will show errors in it and suggest better solutions to some problems. Thus, we can change the design in the light of experience, but we should never change the program without making the same changes in the design. It is surprising how quickly you can forget what the program really does if you have not got a clear description of it. It is even worse if someone else has to look after a program written by you. Since we are writing "real" programs, we must do the job properly.

###Some simple starting points

We are going to write some programs now which can be extended to perform some of the functions associated with components in our plan. In fact they may prove useful in other places in the system as well. There are two that are simple to write using if statements and if-then-else statements. One will check if a text starts with a double blank and remove one blank if it does. The other will check if a line is longer than 60 characters and split it if it is.
###Double blank removal

This problem is easy to describe in English. We have a text variable, which may contain any number of printing characters (we assume no non-printing characters are present). If the first two of these are spaces (ISO blank characters) we want to remove one of them, to leave a shorter text.
A good way of writing programs is to write them in SIMULA as far as we can at the moment and to describe any parts which are missing in English. The program in example 4.4 shows this, with the genuine SIMULA parts in normal letters and the informal English descriptions in italics.

Example 4.4: Informal description of double space remover.

         begin
            text T;
            read in a line of text and place it in T;
            if first character in T =' ' then
            begin
               if next character in T =' ' then
                  remove the first character from T
            end;
            OutText(T);
            OutImage
         end
Example 4.5: More complete version of double space remover.
      begin
         text T;
         InImage;      ! Read into SysIn.Image;
         inspect SysIn do    ! To use SysIn.Image, not SysOut.Image;
         begin
            T :- Blanks(Image.Length);   ! Create a text of the required length;
            T := Image;      ! Copy in the contents of SysIn.Image;
         end;
         if first character in T =' ' then
         begin
            if next character in T =' ' then
               remove the first character from T
         end;
         OutText(T);
         OutImage
      end
We shall use italics in programs in this way from now on.

There is one thing about the use of if statements which this program shows, even in this unfinished form. The then in an if statement must be followed by a statement. The syntax of SIMULA states that this following statement must not be a second if statement and so the second test is made by an if statement enclosed in a begin-end pair. In other words the statement following the then is a compound statement containing a single if statement. Any sequence of one or more statements, not containing declarations, and enclosed in the keywords begin and end is a compound statement and may be used, amongst other things, to hide a second if statement, following a then. Compound statements are explained more fully at the end of this chapter.

The condition in the second if statement is only checked if the condition in the first if statement is true. Thus we only check if the second character is a space after we have found that the first one is.

This can be extended so that the statement following the second then is an compound statement containing a single if statement, allowing us to check for triple spaces or any combination of three characters we chose. By further extension of this we can check four or any other number of conditions, where each is only checked if all those which precede it are true. This is often referred to as "nesting" if statements inside one another.

Let us start to fill in the non-SIMULA parts of the program. As we are going to look at texts in some detail in the next chapter we shall have to cheat by learning some information about them early. We shall also have to use some facts about reading and writing in SIMULA which we will not cover in detail until {{ book.Chapter7 }}.

Firstly we need to read in a line from the program's input. We will assume that all input is coming from a terminal, but if you are using a different system the same program will work, taking its input from the batch input stream. The default input is referred to as SysIn. This will be looked at in detail in chapter 7.

To read in from the normal input all we need to do is use a system procedure called InImage, which matches our output procedure OutImage. This reads in the next line (sometimes called the next "record") and places its characters in a special text location called Image. This is predefined, like system procedures, and does not need to be declared. We can then copy Image into T.

As Image is inside SysIn, we enclose references to it in an inspect statement, which specifies SysIn as the place to find it. If we did not do this the Image contained in SysOut, the default output, would be taken.

Now our program, as shown in example 4.4, is looking a bit healthier.

Using :=, we are not allowed to assign more characters to a text than it already contains. The use of the line

      T :- Blanks(Image.Length)
fills T with the same number of space characters as the total number of characters in Image, allowing the assignment of the actual contents of Image. Blanks is explained in detail in the next chapter, but for the moment we will use it without further comment.
Now we need to check the first character in T. To do this we will use a procedure which gives the next character in a text as a character value. Each time it does so it moves along the text, without changing the characters in the text. It is called GetChar and is a part of the text itself. When we first looked at texts I said that they contained more than just the characters assigned to them. One thing every text contains is a set of procedures of its own.

GetChar is different from the procedures we have seen so far in two ways. Firstly, unlike system procedures, it is part of a text rather than of the whole program. Secondly, when it is called it "returns a value" of type text. This means that it can be used to assign a text value. In fact the procedure InInt also returned a value, of type integer, in an earlier example in this chapter, but we did not investigate it fully. The whole subject of procedures will be considered in detail in chapter 6. For the moment the use of GetChar in this example should be clear enough.

To call a procedure which is inside an object like a text, we use the name of the object, T, followed by a dot, followed by the procedure's name, GetChar. Image.Length is another example of accessing an attribute inside a text.

Now our program, as shown in example 4.6, is nearing completion.

Finally we need to find a way to remove the first character from T. To do this we will use two more procedures which are found inside each text object, Length and Sub. The first returns an integer value, which is the number of characters currently held in the text. The second is a procedure which returns a text which is a section of the characters in the original text.

Length has no parameters, but Sub takes two, both integers. The first is the number of the first character in the text to be included in the text returned. The second is the number of characters from the original text to be included. Thus T.Sub(1,4) returns a text containing the first four characters of T. Combining Length and Sub we can finish our program, or at least produce one that will work for most cases.

Example 4.6: Almost complete double space remover.

      begin
         text T;
         InImage;
         inspect SysIn do
         begin
            T :- Blanks(Image.Length);
            T := Image
         end;
         if T.GetChar=' ' then         ! Is the first character a space?;
         begin
            if T.GetChar=' ' then      ! Is the next character a space?;
               Remove the first character from T
         end;
         OutText(T);
         OutImage
      end
Example 4.7: Complete double space remover.
          begin
             comment Double space removal program,
                     first version, Rob Pooley, March 1984;

             text T; ! Holds the text to be processed;

             InImage;                   ! Reads the text into SysIn.Image;
             inspect SysIn do           ! Refer to SysIn not SysOut;
             begin
                T :- Blanks(Image.Length); ! See the next chapter;
                T:=Image;                  ! Copies the characters into T;
             end;
             if T.GetChar=' ' then      ! First character is a space?;
             begin
                if T.GetChar=' ' then   ! Second character is also?;
                  T:=T.Sub(2,T.Length-1); ! Remove first character;
             end;

             comment Now write out the text;
             OutText(T);
             OutImage
          end Double space remover
Do not worry too much if this is not completely clear at first. With a little study you should get an idea of what is happening. An even better idea is to try compiling and running this program. Try various combinations of spaces at the start of the input you use.
###Exercises

4.1 Try compiling and running the program below.
         begin
            text T;
            T :- Blanks(1);
            T := " ";   ! Single space;
            if T.GetChar=' ' then
            begin
               if T.GetChar=' ' then
                  T := T.Sub(2,T.Length)
            end;
            OutText(T);
            OutImage
         end
Change this program so that whatever value is assigned to T it will not cause a runtime error.
4.2 What happens if the value assigned to T is NoText or ""? If your program does not cater for this case, change it.

4.3 Write a version of our original program which replaces three blanks at the start of a text with a single space.

4.4 Write a version of our original program which replaces two or three spaces with one.

4.5 Could we write a program which looks along the whole of a text and replaces all occurences of double blanks? This may or may not be possible with our present knowledge of SIMULA. What facilities would make it possible and/or easier?

4.6 The following program was intended to write a greeting, but it only prints a blank line. Fix it.

                begin 
                   comment This program is supposed to say hello
                   OutText ("Hello")
                   comment But all it does is print blank lines;
                   OutImage
                end of greeting
###Line breaker

Our second example breaks lines at a length of 60 characters. It is rather crude, but we can improve it. This is another development technique which is widely used - stepwise refinement. Stepwise refinement works by getting part of the job working and moving, one step at a time, towards a complete solution.
Again we start with an informal half SIMULA description, in example 4.8.

Example 4.8: Outline of a line breaker.

                begin
                   text T;
                   InImage;
                    inspect SysIn do
                   begin
                      T :- Blanks(Image.Length);
                      T:=Image
                   end;
                   if T.Length>60 then ! Line too long;
                      Break T into two lines and print them
                   else OutText(T); ! Otherwise write it out unchanged;
                   OutImage
                end
This time we have filled in more of the program to start with. As you become more familiar with SIMULA, you will be able to write large parts of a program straight away. Do not be tempted to fill in more than the most routine parts, however, as you may need to revise your design as you go.
You have probably noticed that the program uses an if-then-else statement. This is because the statement following the else is not used if the statement following the if is executed. T will not be printed if it is split.

You have probably also noticed the comparison and the new symbol it uses, >. This replaces the = and ne symbols that we have used before. It means "is greater than", as you may have guessed from its use. At the end of this chapter is a full list of the comparison operators or "relational operators" as they are known.

We are left to fill in the actions when the length of the line is greater than 60 characters. One immediate problem is that more than one action is required, yet only a single statement is allowed in this place in the if-then-else statement. Fortunately, we have already seen the answer.

###Compound statements and blocks as statements

If you think back I mentioned that a block in SIMULA can be used as a statement. More generally, we can use a sequence of statements enclosed in begin and end wherever we can use a simple statement.
In fact SIMULA uses the term "compound statement" for such a sequence if it contains only statements and the term block where it contains its own declarations. Let us rewrite our program using each in turn to see this. First with a compound statement in example 4.9. Note that we have used two new variables, which are only needed within the compound statement. We can rewrite this using a block and declaring these where they are used. This prevents accidental assignment to them elsewhere, since variables declared inside a sub-block are not visible outside of it. They are, however, visible inside sub-blocks which it encloses. In general it is safest to declare variables as close to the point where they are used as is possible.

Example 4.9: Line breaker with a compound statement.

               begin
                   text T, First, Second; ! Two new text variables used;
                   InImage;
                   inspect SysIn do
                   begin
                      T :- Blanks(Image.Length);
                      T:=Image
                   end;
                   if T.Length>60 then ! Line too long;
                   begin ! Break into two add print separately;
                      First :- Blanks(60); Second :- Blanks(T.Length-60);
                      First:=T.Sub(1,60); ! First 60 characters;
                      Second:=T.Sub(61,T.Length-60); !Second part;
                      OutText(First);
                      OutImage; ! To separate the lines;
                      OutText(Second)
                   end else OutText(T); ! Print unchanged;
                   OutImage
                end
Example 4.10: Line breaker with a block as a statement.
                begin
                   text T; ! No new text variables here;
                   InImage;
                   inspect SysIn do
                   begin
                      T :- Blanks(Image.Length);
                      T := Image
                   end;
                   if T.Length>60 then ! Line too long;
                   begin
                      text First,Second; ! Declare the variables here;
                      First :- Blanks(60); Second :- Blanks(T.Length-60);
                      First := T.Sub(1,60); ! First 60 characters;
                      Second := T.Sub(61,T.Length); ! Rest of text;
                      OutText(First); OutImage;
                      OutText(Second)
                   end else OutText(T); ! Print unchanged;
                   OutImage
                end
Table 4.1: Relational operators.

Here, as promised is a list of the simple relational operators in SIMULA.

         Symbol          Alternative             Meaning

          =                  eq           is equal to
          <>                 ne           is not equal to
          >                  gt           is greater than
          <                  lt           is less than
          >=                 ge           is greater than or equal to
          <=                 le           is less than or equal to
###Summary

We have seen the use of if and if-then-else statements to enable choices within our programs.
We have seen how top down design works and begun to outline the software that we shall build as our practical work in this book. Using if and if-then-else statements we have written programs which will form the basis for some parts of our system - double blank removal and line breaking.

We have seen the concept of stepwise refinement.

We have seen how to replace simple statements with compound statements and blocks.

###Exercises

4.7 Extend our line breaking program to cope with lines longer than 120 characters.
4.8 Could we extend the program to cope with lines even longer than this? How does this fit in with the problem in 4.5?