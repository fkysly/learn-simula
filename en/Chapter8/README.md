#CHAPTER 8 - Item by Item

##Item oriented reading and writing and for loops

###Reading and writing numbers

This chapter will consider the mechanisms in SIMULA for translating numbers into sequences of characters within texts and for translating sequences of characters within texts into numbers. This is clearly a useful thing to be able to do. In fact, we have already used some of these features to make our programs possible. Now we will look in detail at the attributes of text and File which are used for this purpose.
Essentially, this is an extension of the "item oriented" concept used in describing InChar, OutChar, InText and OutText. Instead of considering a sequence of characters as just part of the text in which they occur, we are going to interpret them as integers or reals. Instead of adding to a text some sequence of characters from another text, we are going to write an integer or a real value as such a sequence.

###Adding numbers to a text

There are four procedures which are attributes of text and can be used to add sequences of characters, which represent numbers, to the text. In each the number is converted to a sequence of characters, representing its value as the appropriate type. Apart from decimal points where appropriate, there are no spaces, commas or other separators in such sequences, except when using PutFrac. If the number is negative, a minus sign precedes the first digit, with no intervening space.
This sequence of characters is added to the text, so that the last character is in the rightmost position in the text. If the sequence added is shorter than the text being used, characters in front of the first character added are filled with spaces.

If the text refers to NoText, the attempt to add the sequence causes a runtime error. If the number of characters in the text is smaller than the length of the sequence to be added, the value is not written. Instead the remainder of the text is filled with asterisk characters.

The current position is moved to the end of the text.

###PutInt

PutInt has a single integer value parameter. It converts this value to a sequence of digits representing it as a whole number and adds it to the text as described above.
Example 8.1: The use of PutInt.

      begin
         text T1;
         T1 :- Copy("1234");
         OutText(T1);
         OutImage;
         T1.PutInt(4321);
         OutText(T1);
         OutImage
      end
###PutFix

Oddly, SIMULA uses the name PutFix for the more natural of the two procedures in text which add numbers as reals in texts. As we have seen earlier with OutFix, numbers which are represented as fixed point real numbers consist of a sequence of digits, representing the integer part of the number, followed, if necessary, by a decimal point and a sequence of digits, representing the fractional part of the number.
PutFix has two parameters, a real value one, which holds the value to be added to the text, and an integer value one, which contains the number of places of decimals to be printed. When the value is converted to a sequence of characters, it is rounded to give the required number of digits after the decimal point. If the required number of decimal places is zero, no decimal point is printed and PutFix acts like PutInt. If the number of significant digits after the decimal point is too small, zeros are added to give the required number.

Example 8.2: The use of PutFix.

      begin
         text T2;
         T2 :- Blanks(80);
         T2.Sub(1,6).PutFix(3.2438,4); ! Prints all the significant figures;
         T2.Sub(8,8).PutFix(432.45678,3); ! Prints rounded to 3 places;
         T2.Sub(16,8).PutFix(21.93,4); ! Prints with two trailing zeros;
         T2.Sub(25,4).PutFix(367.487,0); ! Prints as a whole number;
         OutText(T2);
         OutImage
      end
###PutReal

PutReal represents real values in floating point form. This is useful to scientists, engineers and mathematicians, since it allows very large or very small numbers to be represented. PutReal is described in appendix B.
###PutFrac

SIMULA allows integers to be represented as "grouped items". This is occasionally useful, when representing such things as amounts of money. It also allows orders of magnitude to be assessed at a glance.
A grouped item is a string of digits, separated into groups of three by spaces. There may also be a decimal point as a separator, somewhere in the sequence.

Where there is no decimal point, the sequence is broken into groups starting at the right, as shown in examples 8.3. Where the number of digits is not an exact multiple of three, the leftmost group will have less then three digits.

Example 8.3: Grouped items without a decimal point.

        416 379 408
             16 063
          9 327 524
Where there is a decimal point, the sequence to the left of the decimal point is as described above. The sequence to the right is divided into groups working from the decimal point towards the right. Thus the rightmost group after a decimal point may contain less than three digits.
Example 8.4: Grouped items with a decimal point.

        287 901.386 974 203
         12 592.769 23
         32 006.641 974 05
PutFrac has two integer value parameters. The first of these is the number to be represented as a grouped item. The second is the number of places which it should occupy after the decimal point.
Example 8.5: Use of PutFrac.

      begin
         text T5;
         T5 :- Blanks(80);
         T5.PutFrac(23478912,4); ! Should give 2 347.891 2;
         OutText(T5);
         OutImage
      end
###Exercises

8.1 Use PutFrac in a program to calculate a weekly wage, where the first forty hours are paid at #2.30 per hour and subsequent overtime at the normal rate plus half. The program should read in the hours worked and print out the wages for normal working, overtime and as a total.
8.2 What would the representation of the following calls be? Write a program to check your answers.

   1. PutInt(965)
   2. PutFix(43.2,2)
   3. PutFrac(492871409217,5)
   4. PutFix(43.2056,2)
   5. PutFrac(492871409217,6)
   6. PutFix(43.2049,2)
   7. PutFrac(492871409217,4)
   8. PutInt(96.7)
   9. T :- Blanks(4); T.PutInt(721964)

###Reading numbers from texts

Corresponding to the procedures for adding numbers to texts as sequences of characters, there are attributes of text which are procedures for interpreting the next sequence of characters, which must have the format used to represent the type required, as a number of that type.
In all the following procedures, reading always starts at the first character of the text being scanned. The value of Pos is ignored.

Any spaces and tabs at the start of the text are skipped. Any other characters before the start of the numeric item cause a runtime error to be reported. The sequence translated is the longest one that fits the format for the appropriate type. If the sequence ends without fully matching the required format, a runtime error is reported.

The current position in the text is moved one character past the item read. If the text is a constant this change is lost immediately.

###GetInt

GetInt is an integer procedure. It takes a sequence of digits and interprets them as an integer value, returning this value. The sequence is deemed to end at the first non-digit or at the end of the text. Only spaces at the start of the sequence are skipped.
In example 8.6, you should note the need to use Sub to identify the sub-text remaining after each GetInt. This is because scanning always starts at the first character of the text and so repeated calls of GetInt on the same text will always return the first sequence matching an integer in the text. GetChar, however, starts from the current Pos and so Sub is not needed when it is called.

Example 8.6: Use of GetInt.

      begin
         text T6;
         T6 :- Copy("12 345.678 12");
         OutText("FIRST ITEM");
         OutInt(T6.GetInt,4);       !  12;
         OutText("SECOND ITEM");
         T6 :- T6.Sub(T6.Pos,T6.Length-T6.Pos+1);
         OutInt(T6.GetInt,4);       ! 345;
         OutText("THIRD ITEM");
         OutChar(T6.GetChar);       ! . ;
         OutText("FOURTH ITEM");
         T6 :- T6.Sub(T6.Pos,T6.Length-T6.Pos+1);
         OutInt(T6.GetInt,4);       ! 678;
         OutText("FIFTH ITEM");
         T6 :- T6.Sub(T6.Pos,T6.Length-T6.Pos+1);
         OutInt(T6.GetInt,4);       !  12;
         OutImage
      end
###GetReal

Real procedure GetReal is the only way of reading items as reals. It accepts either the format used by PutFix or that used by PutReal. In either case the sequence must be complete. For the fixed point format, which we are considering here, this means that the sequence must be:
A simple sequence of digits, as for an integer;
A sequence of digits, followed by a decimal point and a further sequence of digits;
A decimal point followed by a sequence of digits;
A sequence of digits, followed by a decimal point, but with no further digits is not legal.
For floating point representations allowed, see appendix B.

Example 8.7: Use of GetReal.

      begin
         text T7;
         real R1;
         T7 :- Copy("12 345.678 12");
         OutText("FIRST ITEM");
         R1 := T7.GetReal;
         OutFix(R1,3,8);        ! 12;
         OutText("SECOND ITEM");
         T7 :- T7.Sub(T7.Pos,T7.Length-T7.Pos+1);
         R1 := T7.GetReal;
         OutFix(R1,3,8);        ! 345.678;
         OutText("THIRD ITEM");
         T7 :- T7.Sub(T7.Pos,T7.Length-T7.Pos+1);
         R1 := T7.GetReal;
         OutFix(R1,3,8);        ! 12;
         OutImage
      end
###GetFrac

Grouped items, as described for PutFrac, are read in by GetFrac. This is an integer procedure, which returns the value of the grouped item as if it were an integer, ignoring any spaces or the decimal point if it is present.
Example 8.8: Use of GetFrac.

      begin
         text T8;
         integer I8;
         T8 :- Copy("12 345.678 12");
         I8 := T8.GetFrac;
         OutText("ONLY ITEM");
         OutInt(I8,12);               ! 1234567812;
         OutImage;
         OutFrac(I8,5,20);
         OutImage
      end
###Exercises

8.3 Write a program to read a sequence of real numbers, ending with a negative number, and write them as suitably grouped items. The decimal point should be in the correct place.
8.4 Write a program to read the marks obtained in examinations in English, Mathematics, History and French, in that order, for a class of thirty students. The marks will be given as integers, separated with commas between each subject and with full stops at the end of each student's marks. You may assume that each student's marks are on a new line. Extend your program to write out the results in six columns, with appropriate headings. The first four should give the marks for each subject. The fifth should contain the student's total. The last should contain the student's average, to two decimal places.

###Fixed repetition

The problem in exercise 8.4 shows a different type of loop to those that we have used before. Your solution will probably have used the while loop that we learned in chapter 5. The while loop is very powerful, allowing us to write loops which continue for as many repetitions as are necessary to complete the task. Yet in exercise 8.4 we usually knew the number of times that we wanted to repeat the loop. This meant using a counter to keep track of how many times the loop had been performed.
There is nothing wrong with such a solution, but SIMULA allows us to write the same loops more concisely, using the for loop. Here is the reading part of exercise 8.4, using a while loop and then using a for loop.

Example 8.9: Fixed repetitions using while.

   begin
      character Char1;
      integer C1, C2, Val;
      C1 := 1;
      inspect SysIn do
         while C1 le 30 do
         begin
            text T1;
            InImage;
            T1 :- Image;
            C2 := 1;
            while C2 < 4 do
            begin
               Val := T1.GetInt;
               Char1 := ' ';
               while Char1 ne ',' do Char1 := T1.GetChar;
               T1 :- T1.Sub(T1.Pos,T1.Length-T1.Pos+1);
               C2 := C2 + 1;
            end;
            Val := T1.GetInt;
            C1 := C1 + 1;
         end
   end
Example 8.10: Fixed repetitions using for.

   begin
      character Char1;
      integer C1,C2,Val;
      inspect SysIn do
         for C1:=1 step 1 until 30 do
         begin
            text T1;
            InImage;
            T1 :- Image;
            for C2:=1 step 1 until 3 do
            begin
               Val:=T1.GetInt;
               Char1:=T1.GetChar;
               while Char1 ne ',' do Char1:=T1.GetChar;
               T1 :- T1.Sub(T1.Pos,T1.Length-T1.Pos+1);
            end;
            Val := T1.GetInt
         end
   end
Note the structure of the for loop. The keyword for is followed by a sequence known as a for clause, which is in turn followed by the keyword do and a statement. Thus, the while and condition of the while loop statement are replaced by a for and a for clause.
The for clause specifies a variable and a series of values which are to be assigned to it. The values are assigned in turn and the statement following the do is performed once after each of these assignments.

There are two commonly used forms of the for clause. Example 8.10 uses the step-until form. This specifies:

   1. a starting value, which follows the assignment operator, :=,
   2. an amount to be added to the variable next and each succeeding time, which follows the keyword step,
   3. the upper (or lower) limit for the value of the variable, which follows the keyword until.

The variable to which the values are assigned is known as the "controlled variable". Its value, compared to the limit value, controls the number of times that the loop will be executed.

Consider what will happen in the outer for loop in example 8.10. When the line containing

      for C1:=1 step 1 until 30 do
is first reached, the value 1 is assigned to the controlled variable, integer C1. The statement following the keyword do, which is a compound statement in this example, is then performed.
The value in C1 is then compared with the limit value, and if C1 is the greater, the for loop is complete and the statement following for is skipped.

When all the actions of the compound statement are complete, the program comes back to the keyword for and adds the value following step, 1 in this example, to the value in the controlled variable, C1. C1 now contains the value 2. If this is now greater than the limit, the rest of the for loop is skipped and the program moves to the next statement or, in this case, the end of the block or compound statement containing the for loop.

In this example the program will cause the value of C1 to be set to 1 and increased by 1 twenty nine times, reaching 30. After each change in C1, the statement following do will be performed. Thus this statement will be performed thirty times.

The first value does not have to be 1, neither does the step value. They may even be negative. The statement in example 8.11 uses a negative step to copy the characters from one text to another in reverse.

When the step value is negative, the controlled variable decreases each time round the loop and the check made is that it is not yet less than the limit value.

Example 8.11: Negative steps in a for loop.

      begin
         integer C1;
         text T1,T2;
         T2:-Blanks (12);
         T1:-Copy ("ABCDEFGHIJKL");
         for C1:=12 step -1 until 1 do
         begin
            T1.SetPos(C1);
            T2.PutChar (T1.GetChar)
         end;
         OutText (T1);
         OutImage;
         OutText (T2);
         OutImage
      end
It is also possible to use a real variable as the controlled variable and to use variables or expressions of the appropriate type for any of the values. Examples of some of the many possible variations are given in 8.12.
Example 8.12: Variations on the for clause.

a: Using real values.

      begin
         real R1;
         for R1:=0.1 step 0.3 until 1.0 do OutFix(R1, 2, 4);
         OutImage
      end
b: Using variables as step and limit values.
      begin
         integer I1, I2, I3, I4;
         I2:=4;
         I3:=6;
         I4:=28;
         for I1:=I2 step I3 until I4 do OutInt (I1, 3);
         OutImage
      end
###A word of caution

The three values obtained from the expressions in the step-until sort of for clause are checked each time round the loop. It is possible to assign to them inside the loop, which will disturb the normal sequence of values. In fact some very "clever" programs have been written to exploit this. It is, however, a very unsafe practice and you should normally be very careful not to change the values except in the for clause itself. If you use type procedures in these expressions you may produce unexpected side effects too. Keep your programs simple if you want them to work.
###for clauses using lists of values.

The step-until for clause is very useful when we want to increase or decrease the value of the controlled variable by some constant amount. By using a variable as the step value and altering the value of this during the statement following the do, the change in the controlled variable can be varied, but this is rather dangerous and rarely useful. A simpler means of assigning a series of values, which are not obtainable by repeated additions or subtractions of a step value, is to use a list as the for clause. Consider example 8.13.
Example 8.13: for clause with a simple list.

      begin
         text T;
         character C;
         T:-Blanks(5);
         for C:='B', 'E', 'G', 'I', 'N' do
         begin
            T.PutChar(C);
            OutText(T);
            OutImage
         end
      end
This program will assign the characters in the list to C in the order they are given. After each assignment the compound statement following do will be performed. If you compile and run the program your output should be
      B
      BE
      BEG
      BEGI
      BEGIN
which is fairly self explanatory. One important point is that non-arithmetic values may be used, such as characters and texts. This is not possible in the step-until form.
###Steps in lists

The permutations allowed in the for clause are often only of academic interest. We do not need to consider most of them here. If you are really keen to explore them, I suggest that you refer to the SIMULA Standard [1]. It may be useful to note that a step-until contruction may be used as one element in a list in a for clause. This is shown in example 8.14. Try it and see the effect.
Example 8.14: Mixing steps and lists.

      begin
         integer I;
         for I:=4 step 1 until 6, 3, 8 step 2 until 20 do
         begin
            OutInt (I,3);
            OutImage
         end
      end
###Exercises

8.5 Rewrite exercise 8.4 using for loops where possible.
8.6 Write a program to print out the multiplication tables from two to twelve, in the following format

         2          3          4          5       etc.
        1x2 = 2    1x3 = 3    1x4 = 4    1x5 = 5
        2x2 = 4    2x3 = 6    2x4 = 8    2x5 =10
                   etc.
8.7 Write a program which reads in a series of words and prints them five to a line, with a blank line after every six lines.
8.8 Extend your answer to 8.7 to print successively the letters a, b, c, d, e, f before the lines in each block and to number the blocks.

###Item oriented I/O with Files.

Matching the item oriented procedures for reading and writing in a text are procedures for reading and writing sequences of characters which represent numbers in Files. Those for output are local to OutFile, and thus PrintFile, while those for input are local to InFile.
###Output of numeric items

The numeric item procedures in OutFile output are OutInt, OutFix, OutReal and OutFrac. Each has the same parameters as its Put equivalent, plus an additional width parameter, which is an integer and comes after the others.
Basically each procedure creates a sequence of characters in the Image of the OutFile, starting at the current position. These are of the same forms as for the corresponding Put procedures, but their positioning is controlled by the width parameter.

###The meaning of the width parameter

The width parameter specifies how many characters in the Image will be used by the numeric item. If the actual item is shorter than this width, space characters are added on the left of the item to achieve the required length. Thus the items are said to be "right aligned" within the length of text specified by their width fields, since their last character always fills the rightmost space specified by the width parameter.
If the actual item is longer than the width specified, it is not output. Instead a sequence of asterisks of the specified length is printed. This is known as an editing overflow and many SIMULA systems will report the number of such overflows at the end of the program. By not allowing more than the specified number of characters to be output, the alignment of columns of figures is preserved. By printing asterisks, the user is warned that insufficient space was allowed for printing the required values.

Before writing an item, the SIMULA runtime system will check that there is sufficient space left in the current Image. If not, an OutImage is performed to copy the current Image to the actual File or output device, and the item is written at the start of the new Image. If the item is longer than Image.Length, a runtime error is reported.

Example 8.15 shows the use of some of the output procedures. It shows an overflow and an implicit OutImage.

Example 8.15: Item oriented output to a File.

      begin
         ref (OutFile) OutPut;
         
         OutPut:-new OutFile ("TABLES");
         
         inspect OutPut do
         begin
            Open(Blanks(40));
            OutInt(203, 10);                !3 characters in 10 spaces;
            OutFix(283.42, 5, 10);          !8 characters in 10 spaces;
            OutFrac(10348215, 3, 10);       !10 characters in 10 spaces;
            OutInt(9654, 5);                !4 characters in 5 spaces;
            OutInt(103694, 4);              !6 characters in 4 spaces->overflow;
            OutInt(3, 2);    !Not enough space left in Image->implicit OutImage;
            OutImage;
            Close
         end*of*inspect
      end
###Exercises

8.9 Rewrite your answer from 8.6 using OutInt.
8.10 Write a program which reads and prints the name, age in years and weight of a given number of people. The program should:

ask for and read the number of people to be dealt with.
ask for each item in turn for each person.
print out each person's details on a separate line, with the columns correctly aligned.
###Input of numeric items.

The input of numeric items is done by InInt, InReal and InFrac. No parameters are required. Each is a procedure of the appropriate type.
Thus InInt reads the next sequence in the current Image as an integer, returning its value. InReal reads it as a real, returning its value. InFrac reads it as a grouped item, returning the corresponding integer value.

As with the Get procedures in a text, any spaces are skipped. If the first sequence of non-spaces encountered is not in the correct form for the required type, a runtime error is reported.

The end of the current Image ends the item. If no non-space character is found before the end of the current Image, an InImage is performed and the search continues.

Example 8.16 shows the input procedures in use.

If a number is read which is larger than the largest positive value or smaller than the smallest negative value of the appropriate type which can be held on a particular computer, a runtime error, either integer overflow or floating point overflow, should be reported. If a real value which is too near to zero is read a runtime error, floating point underflow, should be reported. The limits for each SIMULA system are given in the appropriate Programmer's Reference Manual or User Guide.

###Text concatenation

As we have now completed the attributes of text, a word on the recently introduced text concatenation operator is perhaps appropriate. This operator, &, combines two text frames, producing a reference to a new text frame.
The combined text has a frame consisting of a copy of the characters from the text to the left of the operator, followed by a copy of the characters from the text to the right. Example 8.17 shows the use of the concatenation operator. Only up to date SIMULA systems will have this feature.

Example 8.16: Item oriented input from a File.

      begin
         ref (InFile) InPut;
         real R1; integer I1;
         InPut:-new InFile ("SOURCE");
         
         inspect InPut do
         begin
            Open(Blanks(80));
            OutInt (InInt, 4);   ! Output goes to SysOut;
            R1:=InReal;
            I1:=InFrac;
            Close
         end*of*inspect*input;
         OutImage
      end
Example 8.17: Text concatenation operator.
   begin
      text T1, T2, T3;
      T1 :- "Left";
      T2 :- "Right";
      T3 :- T1&T2;
      OutText(T3);
      OutImage
   end
###Exercise

8.11 Rewrite the exam result question using all the new features which are appropriate.
###Summary

We have extended the idea of item oriented input and output to cover the reading and writing of sequences of characters which represent arithmetic values in texts.
We have seen the attributes of text which allow such operations on integer, real and grouped items.

We have seen how to write for loops, which allow us to perform a task a certain number of times or once for each member of a list of values. We have also noted certain dangers with this.

We have seen the equivalents of the text item oriented attributes which exist for InFile and OutFile.

The text concatenation operator has been described.

{{ book.Chapter9 }}