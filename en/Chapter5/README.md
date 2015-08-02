#Chapter 5 - Would you mind repeating that?

##Texts and while loops

###Don't stop until I tell you

In the last chapter we twice considered the question of how to extend programs which assumed a maximum number of repeated tests, so that any number could be dealt with, as the data required. I hope you managed to deal with triple spaces and triple length lines without too much trouble. Obviously it would be more of the same to cope with four spaces or lines longer than 180 characters and so on. But how can we cope with any number of repetitions?
This situation is obviously going to crop up in most real programs. To deal with it SIMULA has a statement known as a while loop. Example 5.1 shows it in use.

Example 5.1: A while loop.

       begin 
          comment Read and Total a stream of Positive numbers,
                  ending when a negative number is found;
          integer Total,Next;
          Next := InInt;            ! Read in an integer;
          while Next>=0 do          ! Only perform the next statement if 
                                           condition is true;
          begin
             Total := Total + Next; ! Update total;
             Next := InInt;         ! Read in the Next integer;
          end;                      ! Go back to the start of the while loop;
          OutInt(Total,8);          ! When the condition fails, print the total;
          OutImage
       end
This is a simple program and it is explained by its comments. Let us look at the syntax of the while loop statement, which may be used wherever any simple statement may be used.

A while loop is the keyword while, followed by a condition, followed by the keyword do, followed by a statement.

In our example the statement following the do is a compound statement, but any kind of statement may be used.

This is very similar to the if statement, with while instead of if and do instead of then. The real difference is in the meaning - the semantics.

The condition is tested and, like an if statement, if it is not true, the rest of the while loop is skipped and the program continues after the while statement.

If, on the other hand, the condition is true, the remainder of the while statement, i.e. the statement following do, is executed, again like an if statement. When this statement has been completed, however, the program moves back to the while and tests the condition again. Thus the statement following the do is executed repeatedly for as long as the condition remains true.

To see this more clearly, let us consider some possible streams of input.

The simplest is where the first number is negative. In this case the condition Next>=0 is false the first time it is tested and the statement following the do is never executed. If we had used an if statement the effect would have been the same.

Another possible stream is one positive number followed by a negative number. Here the condition is true the first time and the number is added to Total. When the next number has been read the program moves back to while. This time the condition is false and so the statement after do is skipped this time and the total will be the value of the first number.

###Exercise

5.1 Work through the case where two positive numbers are input, followed by a negative one. It is known as "dry checking" when you try the program "by hand" in this way.
Compile and run the program to make sure you were right.

###Line breaker revisited

We can now extend our line breaker to deal with any length of line. Let us write it out informally, to get our design right.
The important things to consider when writing programs using while loops are:

  1. What condition is to be tested?
  2. Have all values used in the test been set the first time the test is carried out?
  3. Are all the possible cases covered?
  4. Are at least some of the values used in the condition potentially updated within the loop before each subsequent test?

Bearing this in mind let us start with the program in example 5.2.
Example 5.2: General outline of a program using a while loop.

                begin
                   Set up initial values;
                   while condition do
                   begin
                      Actions;
                      Update values tested in condition
                   end;
                   Final actions
                end
The initial values are set by reading the line into Image as before and assigning its Length to an integer, LenLeft. The condition to be tested is that the unprinted part of Image is longer than 60 characters. Once this length is 60 characters or less, we want to print the remainder of Image as a separate line and this makes up the final actions.

This gives us example 5.3.

Example 5.3: Linebreaker 1.

                begin
                   integer LenLeft;
                   inspect SysIn do
                   begin
                      InImage;
                      LenLeft:=Image.Length;
                      while LenLeft>60 do
                      begin
                         Actions;
                         Update tested variables
                      end;
                      OutText(Image.Sub(1,LenLeft));
                      OutImage
                   end..of..inspect..SysIn
                end
Which leaves the actions and updating within the loop to be filled in. The actions are to print the current first 60 characters of Image as a separate line. The updating must reduce LenLeft, otherwise the loop will go on indefinitely. The simplest solution is to remove the first 60 characters from Image and subtract 60 from LenLeft.
Thus our complete program is example 5.4.

Example 5.4: Linebreaker 2.

                begin
                   integer LenLeft;
                   inspect SysIn do
                   begin
                      InImage;
                      LenLeft:=Image.Length;
                      while LenLeft>60 do
                      begin
                         OutText(Image.Sub(1,60));
                         OutImage;
                         LenLeft := LenLeft - 60;
                         Image:=Image.Sub(61,LenLeft)
                      end;
                      OutText(Image);
                      OutImage
                   end..of..inspect..SysIn
                end
This time I have used a more concise way of printing the first 60 characters and, since Image takes the place of Second in the examples in chapter four, no extra text variables are used.

###Exercise

5.2 Are there any line lengths which are not catered for? If you can think of any, modify the program to cater for them.
###What is a text?

In the last chapter we saw some properties of text, which make it a much more complex type than integer, for example. Let us consider further what exactly a text is and what properties it has. In fact it is so complex that we shall only look at half of its properties here and leave the rest until chapter 8.
So far we have seen that a text has a string of characters. This may be of any length from zero to some very large number. The maximum length is system dependent and you should consult the User's Guide or Programmer's Reference Manual for the SIMULA system you are using. It is unlikely to be too small for you.

We have also seen that a text contains procedures, which are inside it or "local to it", to use the technical term. In fact a text variable is not quite what it seems.

Strictly speaking the location reserved for the text variable holds a reference to a sequence of characters. These characters are known as a text frame. A text reference contains a pointer to its text frame plus related variables and procedures. The use of these will, hopefully, soon become clear.

###A text reference

The variables inside a text reference are not visible outside it. They are used by the procedures which form the visible attributes of a text, along with the text frame.
Here are the major parts of a text, with examples of their use.

###Text frame

The value part of a text is the string of characters to which its reference variable points. This is known as the text frame referenced by that variable. It is a location in the computer's memory holding a sequence of characters and has a fixed length.
Two text frames may overlap and share some part or all of the same sequence of characters and, by implication, of the same location.

###Length

The length of the string of characters in the text frame is held in a hidden integer in the text reference variable. Its value can be obtained by calling the local integer procedure Length. We have used this in some earlier examples to find the length of a line of input.
Example 5.5: Use of Length

                begin
                   integer Length;
                   InImage;
                   inspect SysIn do Length := Image.Length;
                   OutInt(Length,8);
                   OutImage
                end
Note that the name given to the integer is irrelevant to the call of Image.Length. I have called it Length to demonstrate that the procedure Image.Length is different from any use of Length on its own, in the program itself.
###Pos

The position of the character which will be read next in the text frame is held in another hidden integer variable within the text reference. It can be read by calling the integer procedure Pos. When a text object is first created Pos will give the value 1. After calling GetChar for the first time it will give 2 and so on.
Example 5.6: Use of Pos.

                begin
                   InImage;
                   inspect SysIn do
                   begin
                      OutInt(Image.Pos,4);
                      OutImage;
                      Image.GetChar;
                      OutInt(Image.Pos,4);
                      OutImage
                   end
                end
You should get
                1
                2
as output when you run this.
###More

More tells you when you have reached the end of a text. It returns either true or false, i.e. it is a Boolean procedure.
More gives the value true as long as the value given by Pos is between 1 and the value given by Length. Thus, once the last character has been read, More returns the value false.

Example 5.7: Use of More.

begin
                   InImage;
                   inspect SysIn do
                      while Image.More do
                      begin
                         OutInt(Image.Pos,4);Image.GetChar;
                         OutImage
                      end;
                end
When you run 5.7 you should get output of the following form.
                1
                2
                3
                4
                etc. until Image.Length
###Constant

Boolean procedure Constant is a late addition to the definition of text. It returns the value false unless the text frame for its text reference is non-writeable, in which case it returns true. Only text frames generated by reference assignment of a constant string (see below) or sub-texts generated from these will cause Constant to return True. Any attempt to alter the contents of a text frame whose reference returns true for Constant will cause a runtime error to be reported.
As a very new feature, Constant will not be recognised by many older systems.

Example 5.8: Use of Constant.

      begin
         text T;
         T :- "Constant frame";
         if T.Constant then OutText("Correct") else OutText("Wrong");
         T :- Copy("Writeable");
         if T.Constant then OutText("Wrong") else OutText("Correct");
      end
###Sub and Start

We have already used Sub. Sub returns a reference to a text frame made up of a sequence of characters which are part of the original text. It has two integer parameters. The first specifies the number of the character in the original text frame which will be the start of the subtext. The second specifies the number of characters which will be in the subtext.
Example 5.9 will print out the following:

                Miser
                cord
                Mire
Make sure you understand why. Try running it for yourself.
Note that Sub does not copy the characters from the original text. It merely creates a text reference to part of the same location.

Example 5.9: The use of Sub.

                begin
                   text T;
                   T:-"Misericordia";
                   OutText(T.Sub(1,5));
                   OutImage;
                   OutText(T.Sub(7,4));
                   OutImage;
                   OutText(T.Sub(1,2));
                   OutText(T.Sub(5,1));
                   OutText(T.Sub(4,1));
                   OutImage
                end
Recently, integer procedure Start has been added to the definition of text. It is used in conjunction with Sub, to keep track of where the current text frame starts within the original text frame of which it forms part. Only text frames generated by calls on Sub can cause Start to return anything but 1.
When a new text reference is generated by Sub, the value of Start in the new reference will be the value of Start for the text passed to Sub plus the offset within this of the text frame of the sub-text, i.e. the firstt parameter of Sub minus 1. Start reflects the cumulative effects of any Sub calls which have produced the text frame of the current text reference.

Older systems will have Sub but not Start.

Example 5.10: Use of Start.

      begin
         text T1, T2, T3;
         T1 :- "123456789";
         T2 :- T1.Sub(3,7);
         T3 :- T2.Sub(3,5);
         OutInt(T1.Start,4);   ! 1;
         OutInt(T2.Start,4);   ! 1+3-1=3;
         OutInt(T3.Start,4);   ! 3+3-1=5;
         OutImage
      end
###Text assignments

The oddest thing about texts is that there are two different kinds of assignment statement, using two different assignment operators. We have already used both, but now we can explain their differences.
There are text reference assignments and text value assignments. Examples are:

                T :- Blanks(60); ! Reference assignment;
                T := "Hello"   ; ! Value assignment;
The difference is very simple, but very important.
A text reference assignment will replace the pointer in the text reference location, which originally points to one text frame, with a different reference, which may or may not point to a different text frame. All the parts of the original text reference, including its current Pos, Length etc. are replaced by the corresponding parts of the new text reference. Thus calls on T.Length and T.Pos may give different values before and after a reference assignment to T.

A text value assignment will only replace the characters held in the text frame. None of the other parts are replaced and so calls on T.Length or T.Pos will return the same values before and after a value assignment to T.

Since a value assignment leaves the Length unchanged we must be careful that the number of new characters being assigned is not greater than Length. In fact three cases are possible.

The number of characters being assigned is the same as Length. In this case the new characters simply overwrite the old contents of the text.
The number of characers being assigned is less than Length. In this case the leftmost characters in the text are overwritten by the new characters and the remainder by spaces. The value of Left.Length is unchanged in example 5.11b.
The number of characters being assigned is greater than Length. This will be a runtime error. The program will compile successfully, but at runtime will report an error.
Note that text value assignment to a constant text frame is always illegal.

Examples 5.11: Possible cases of text value assignment.

a) Equal length texts.

                begin
                   text Left,Right;
                   Left :- Copy("FRED"); ! Length of Left becomes 4;
                   Right :- Copy("DAVE"); ! Length of Right becomes 4;
                   Left:=Right;
                   OutText(Left);OutImage;
                   OutInt(Left.Length);OutImage
                end
b) Left hand text longer than right.
                begin
                   text Left,Right;
                   Left :- Copy("FRED"); ! Length of Left becomes 4;
                   Right :- Copy("JIM"); ! Length of Right becomes 3;
                   Left:=Right;
                   OutText(Left);OutImage;
                   OutInt(Left.Length);OutImage
                end
c) Left hand text shorter than right.
                begin
                   text Left,Right;
                   Left :- Copy("FRED"); ! Length of Left becomes 4;
                   Right :- Copy("CHARLIE"); ! Length of Right becomes 7;
                   Left:=Right; ! Causes a runtime error;
                   OutText(Left);OutImage;
                   OutInt(Left.Length);OutImage
                end
###NoText

When you create a text variable it is set to point to NoText. NoText is an imaginary text frame with no characters. Thus it has a Length of zero and any value assignment to it will fail, except when the value is the empty text value, "". This is the reason we had to use Blanks in our early examples, to make what you should now recognise as a text reference assignment to any text variable, before any value assignments were made to it.
###Creating text objects

In general, we must make a reference assignment to a text variable before we use it for almost anything else. This points it at a text frame and sets the values for Pos, Length etc., but where does this object come from in the first place? In fact we have used several methods of text object generation already, without explaining them in detail. Here is a more detailed look.
###Blanks

The standard procedure Blanks creates a new text object containing only spaces. It has one integer parameter, which specifies the number of space characters it is to contain and, thus, sets its Length. Start and Pos will initially be 1. More will be true. Constant will be false.
Example 5.12: Use of Blanks for text generation.

                begin
                   text Empty;
                   Empty :- Blanks(60);
                   comment Point Empty at a text frame containing 60 spaces;
                   OutInt(Empty.Length,4);
                   OutImage
                end
###Reference assignment of text constants

We have seen how text constants, or strings, are represented. When a text reference assignment, whose right hand side is such a constant, is made, a text frame is created containing the characters in the string.
One important property of the text frame created in this way is that it is regarded as constant or "read only". Any attempt to overwrite its characters will cause a runtime error.

The value of Length will be the number of characters in the string. Start and Pos will be 1. More will be true. Constant will be true.

The text frame generated for each text constant in a program is regarded as occupying a unique location. This is important when we come to "reference comparison" of texts. The only exception is an occurence of the empty string, "", which is always assumed to generate a reference to the imaginary location of NoText.

Example 5.13: Text constant reference assignment.

                begin
                   text Full;
                   Full:-"Any old string you like";
                   OutInt(Full.Length,4);
                   OutImage
                end
###Copy

In older SIMULA systems, this reference assignment of text constants was not allowed. If you are using such a system you will have to use the standard procedure Copy to do the same thing. Copy will still work on newer systems, so if you may need to move your programs around a lot it might be safer to use it from the start.
Copy differs from constant text reference assignment in that the text frame created is alterable or "writeable". It is possible to overwrite its characters. Thus Copy may be used for many purposes where text reference assignment of constants would not be suitable, even on newer systems.

Copy can also be used to generate a reference to a copy of the text frame of another text reference, not just a string. The second use of Copy in 5.14 shows this.

Rewriting example 5.13 using Copy, we get the first part of example 5.14. This should have exactly the same effects, except that attempts to overwrite Full would now be successful.

In the reference generated by Copy, Length is the length of the string or of the text frame being copied. Start and Pos are 1. More is true. Constant is false.

Example 5.14: Text generation using Copy.

                begin
                   text Full, NewFrame;
                   Full :- Copy("Any old string you like");
                   OutInt(Full.Length,4);
                   OutImage;
                   NewFrame :- Copy(Full);
                   OutInt(NewFrame.Length,4);
                   OutImage
                end
###Line breaker again

Using the facts about reference assignment of text variables that we have just seen, it is possible to write our line breaking program more neatly. It should now be obvious why we used the integer LenLeft to hold the length of Image which remained. It is now possible to use Image.Length instead.
This version has another advantage, which probably is not apparent. In the version using value assignment, the Length of Image remains unchanged throughout, with more space characters following the visible characters after each assignment of Image.Sub. These characters are not present after the reference assignments in the new version. Since some output devices make a printing movement even for a space, their presence may slow down output. They also take up space in the computer's memory which is freed in the new version.

Example 5.15: Linebreaker using reference assignment of texts.

begin
                   InImage;
                   inspect SysIn do
                   begin
                      while Image.Length>60 do
                      begin
                         OutText(Image.Sub(1,60));
                         OutImage;
                         Image :- Image.Sub(61,Image.Length-60);
                         comment Note the use of reference assignment which
                                 updates Image.Length, unlike value assignment;
                      end;
                      OutText(Image);
                      OutImage
                   end
                end
###Exercises

We have had quite a lot to absorb recently, so here are some examples to help your digestion. Then we will have another look at space removal and a final look, for the moment, at text objects.
5.2 Write a program to draw a Christmas tree, like the one below.

                        *
                       ***
                      *****
                       ***
                      *****
                     *******
                    *********
                      *****
                     *******
                    *********
                   ***********
                        *
                        *
5.3 Write a program to count the number of characters before the first space in a text.
5.4 Write a program to count the number of words in a text, assuming that only spaces separate words.

5.5 Write a program to calculate the cost of sending a telegram, assuming that the whole message is contained in a single text. Assume that each word costs 10 pence.

5.6 Rewrite the last program assuming that words longer than 5 characters cost double and that the minimum charge is 50 pence.

5.7 Use Sub to write the message

    Merry Christmas
using parts of the string "May Charlie stay for curry?".
5.8 Is it possible to rewrite the word counting program to allow full stops (periods) and commas to be word separators? What extra SIMULA facility would be useful for this?

###Double blank remover, mark two

The problem with our double blank removing program is that it will only work when the double blank is at the start of the text. What we really need is a program to read along the whole of a text and remove any double blanks. Even better, it should remove treble and other multiple blanks as well.
Using our informal SIMULA description method, we can start with the program in example 5.16.

Example 5.16: Informal description of multiple space remover.

                begin
                   text T1;
                   character Char;
                   InImage;
                   inspect SysIn do
                   begin
                      T1 :- Image;
                      while T1.More do 
                      begin 
                         Char := T1.GetChar;
                         if multiple space then replace with single space
                      end;
                      Remove trailing spaces;
                      OutText(T1);
                      OutImage
                   end
                end
The problems to be solved are how to detect multiple blanks and how to replace them with a single space character. The first of these is fairly easy.
If the current character is a space, check to see if the next one is also. If it is not, carry on around the loop. If it is, check whether the next character is also a space and so on. When a non-space is found, move the remainder of the text to the left until only one space remains. Clearly the scanning along multiple spaces is another while loop. Let us write out the program so far, in example 5.17.

Example 5.17: Second stage of multiple space remover.

            begin
               text T1;
               character Char1, Char2;
               InImage;
               inspect SysIn do
               begin
                  T1 :- Image;
                  while T1.More do
                  begin
                     Char1 := T1.GetChar;
                     if Char1=' ' then
                     begin
                        integer Position1, Position2;
                        if T1.More then
                        begin
                           Position1 := T1.Pos; ! Remember current position;
                           Char2 := T1.GetChar; ! Check Next character;
                           while Char2=' ' do
                              if T1.More then Char2 := T1.GetChar else
                              begin
                                 Char2 := '#';
                                 Reset Pos to Position1
                              end;
                           Position2 := T1.Pos; ! Remember final position;
                           if Position1 ne Position2 then move the rest left;
                           Reset Pos to first character after blank
                        end
                     end
                  end;
                  Remove trailing spaces);
                  OutText(T1);
                  OutImage
               end
            end
This is getting rather complicated, so let us have a look at what is going on in the case where a blank is encountered. It is always sensible to "dry check" new parts of your program when they become more than trivial extensions.
First we check that this is not the end of the line, by using T.More. If we try to use GetChar when we are already at the end, it will cause a runtime error to be reported.

If there is more to read, we note the current position in Position1. This is the position of the first character after the space and so we remove any spaces from here to the next non-space character.

We now read the Next character into Char2 and begin our blank removal loop. As long as Char2 is a space we read on, checking that we have not reached the end of the text. If we have reached the end, we do not need to trouble with this sequence of blanks, since they are at the end of the text. We shall see how to deal with such "trailing blanks" in a moment.

If we do reach the end of the text while skipping through a sequence of blanks, we want to leave the loop and avoid further processing of this text. To achieve this we set a dummy value in Char2, which is not a space character, thus ending the while loop. Since we are not interested in removing any blanks, we reset our position to Position1.

Having completed the while loop, we check to see if Position2 is still the same as Position1. If it is then we have either not found any spaces following the first one or the current sequence of spaces is at the end of the text. In both these cases no spaces need be removed. If Position2 is different, we have skipped over some spaces in the while loop and want to remove them.

Having removed any extra spaces, we reset our position to that immediately following the single space, i.e. to Position1, and continue reading through the text looking for the next space, in the outer while loop.

Try following some texts through the program on paper. In particular try to think of all the different cases which might occur. The choice of test data for your programs is very important. If you forget a case then your program will almost certainly fail the first time it is used in earnest.

We still have two parts of the program to fill in. Before we can do so we must look at some more properties of text objects.

###GetChar and PutChar

We have seen how GetChar works, but let us quickly define exactly what it does. GetChar returns the character at the current position in a text and increments the position counter by one. Thus successive calls on GetChar will scan through a text character by character.
PutChar overwrites the character at the current position in a text with the value passed to it as a parameter. It has a single character parameter. It increments the position counter by one each time it is called.

In example 5.18 the program joins the character contents of two texts together. This is sometimes referred to as concatenating them.

Example 5.18: Text concatenation using GetChar and PutChar.

                begin
                   text T1, T2, T3;
                   InImage;
                   inspect SysIn do
                   begin
                      T1 :- Copy(Image.Strip); ! Strip is explained next;
                      InImage;
                      T2 :- Copy(Image.Strip);
                      T3 :- Blanks(T1.Length + T2.Length); ! Create enough space;
                      while T1.More do T3.PutChar(T1.GetChar);
                      comment T3.Pos is now T1.Length + 1;
                      while T2.More do T3.PutChar(T2.GetChar);
                      OutText(T3);
                      OutImage
                   end
                end
###SetPos

One of our problems in our space removal program is solved immediately by the procedure SetPos. This takes one integer parameter. SetPos resets the current position in a text to the value passed to it as a parameter. Using this we can rewrite our text concatenation program more simply.
Example 5.19: Text concatenation with SetPos.

                begin
                   text T1, T2, T3;
                   InImage;
                   inspect SysIn do
                   begin
                      T1 :- Copy(Image.Strip);
                      InImage;
                      T2 :- Copy(Image.Strip);
                      T3 :- Blanks(T1.Length + T2.Length);
                      T3 := T1;
                      T3.SetPos(T1.Length + 1);
                      while T2.More do T3.PutChar(T2.GetChar);
                      OutText(T3);
                      OutImage
                   end
                end
###Strip

Another small problem is solved by the procedure Strip. This returns a text reference to the original text with any trailing spaces, i.e. any spaces at the right hand end of the text, removed. Try the program in example 5.20 to see this at work.
Example 5.20: Use of Strip.

                begin
                   text T;
                   T :- Blanks(60);
                   T := "FOUR";
                   OutInt(T.Length);
                   T :- T.Strip;
                   OutInt(T.Length);
                   OutImage
                end
###Main

The last part of a text object that we need to consider at this point is the procedure Main.
When a text variable is pointed at a subtext, by using Sub or Strip, the result is that the text variable points at a new text frame. This shares some of the characters in the original, main text, but has its own local Pos, Length and other attributes. Pos will be 1 and Length will be the length of the subtext.

It is still possible to refer to the main text from the subtext, however, by using the attribute Main. This procedure returns a reference to the text frame of which the sub-text forms a part. For a text reference whose text frame is is not a subtext of a larger one, Main points to that frame itself.

The reference generated by Main has Pos and Start equal to 1 and Length equal to the Length of the main text frame.

Example 5.21 should produce the following output:

   is the time for ever
   Now is the time for every good man
   the time for ever
   Now is the time for every good man
Example 5.21: The use of Main.
                begin
                   text T1, T2, T3;
                   T1 :- "Now is the time for every good man";
                   T2 :- T1.Sub(5,20);
                   T3 :- T2.Sub(4,17);
                   OutText(T2);
                   OutImage;
                   OutText(T2.Main);
                   OutText(T3);
                   OutImage;
                   OutText(T3.Main);
                   OutImage
                end
Example 5.22: Adding SetPos and Strip to multiple space deletion.
            begin
               text T1;
               InImage;
               inspect SysIn do
               begin
                  T1 :- Image;
                  while T1.More do
                  begin
                     character Char1, Char2;
                     Char1 := T1.GetChar;
                     if Char1=' ' then
                     begin
                        if T1.More then
                        begin
                           integer Position1, Position2;
                           Position1 := T1.Pos;
                           Char2 := T1.GetChar;
                           while Char2=' ' do
                              if T1.More then Char2 := T1.GetChar else
                              begin
                                 Char2 := '#'; ! Force loop to end;
                                 T1.SetPos(Position1); ! Ignore spaces;
                              end;
                           Position2 := T1.Pos;
                           if Position1 ne Position2 then remove spaces;
                           T1.SetPos(Position1)
                        end
                     end
                  end;
                  T1 :- T1.Strip;
                  OutText(T1);
                  OutImage
               end
            end
###Multiple spaces finally removed

First consider example 5.22, where SetPos and Strip are added to our program. We have now catered for everything except the actual deletion process.
Example 5.23 adds the final stage,the removal of those blanks. Watch closely, as we are going to use a very powerful property of text reference assignment.

Example 5.23: The complete multiple space remover.

            begin
               text T1;
               InImage;
               inspect SysIn do
               begin
                  T1 :- Image;
                  while T1.More do
                  begin
                     character Char1, Char2;
                     Char1 := T1.GetChar;
                     if Char1=' ' then
                     begin
                        if T1.More then
                        begin
                           text T2;
                           integer Position1, Position2;
                           Position1 := T1.Pos;
                           T2 :- T1; ! T2 holds current Pos, Length etc.;
                           Char2 := T1.GetChar;
                           while Char2=' ' do
                              if T1.More then Char2 := T1.GetChar else
                              begin
                                 Char2 := '#';
                                 T1.SetPos(Position1)
                              end;
                           Position2 := T1.Pos;
                           if Position1 ne Position2 then
                           begin
                              T2.PutChar(Char2);
                              while T1.More do T2.PutChar(T1.GetChar);
                              while T2.More do T2.PutChar(' ');
                              T1 :- T2
                           end;
                           T1.SetPos(Position1)
                        end
                     end
                  end;
                  T1 :- T1.Strip;
                  OutText(T1);
                  OutImage
               end
            end
The point to note is that when the text variable T2 is pointed at the text frame already pointed to by T, the effect is that a new reference is created with all its current values the same as T. As in a subtext, some of the characters are shared by both frames; in this case it is all the characters which are shared. When T.GetChar is used, it is only the Pos of T that is affected. That of T2 is unaffected.
When we are shuffling the characters to the left to remove unwanted spaces, we are able to pretend that T and T2 are entirely different texts. If we did not do this, we would have to copy into another text frame and then back again. This way of manipulating the characters in a text is very powerful, but must be fully understood and used with great care. It is only when the part of the text that we are copying from is ahead of the part that we are copying to that this method will work.

###Summary

We are now writing programs which really do things.
In this chapter we have seen the while loop and how it can allow an action or sequence of actions to be repeated as often as necessary.

We have used informal SIMULA descriptions to allow us to develop our programs in stages.

We have learned some of the attributes of a text object - Length, Pos, Sub, Start, Constant, Main, Strip, GetChar, PutChar and SetPos.

We have learned the difference between a text variable and the text frame it points to.

We have learned the difference between text value assignment and text reference assignment.

We have seen how text objects are generated by Blanks, Copy or reference assignment of a text constant, noting that those generated by reference assignment of a constant may not be overwritten.

We have seen how two text variables pointing at the same sequence of characters can have different current positions.

{{ book.Chapter6 }}

###Exercises

5.9 A computer fault has changed some small letter a's into ampersands, '&'. Write a program to scan a text and correct this.
5.10 A similar fault has changed every occurence of the word "and" to the word "boe". Write a program to correct texts which have suffered this fate.