#Chapter 3

##Type Cast Actors

###The importance of types

So far we have seen three types: integer, real and text. The only one we have looked at in any detail is integer. In fact SIMULA contains several other types as keywords or combinations of keywords. In addition it is possible for you to create combinations of these simple types and give them names, by using the class mechanism. In this chapter we will look at the simple types which are already defined in SIMULA.
###Types in assignments

A type is given in a declaration so that the SIMULA system knows how much space to allow for the declared quantity. The system checks whenever a new value is stored into this location that the value is of the same type. If the types are different then the system may do one of two things.

      1. It may "convert" the quantity being stored to that of the location. This is only possible between the types integer and real and their short and long variants.

      2. If conversion between the types is not specified in SIMULA, the system will report a semantic error.
SIMULA will only convert one type to another where this has a clear meaning. In practice this is only the case for arithmetic values, i.e. types which represent numbers. Even there it may need clarification. Unlike some languages, it is not enough that the two types take up the same amount of space in the computer. Try examples 3.1 and 3.2 to see how your SIMULA system handles such situations.

Example 3.1: A legal assignment of one type to another.

        begin
           integer IntVal;
           real RealVal;
           
           RealVal := 3.2;
           IntVal := RealVal;
           OutInt(IntVal,4);
           OutImage
        end
Example 3.2: An illegal assignment of one type to another.

        begin
           character CharVal;
           integer IntVal;
           
           CharVal := '3.2';
           IntVal  := CharVal;
           OutInt(IntVal,4);
           OutImage
        end
Clearly we must be very careful how we use types. Even where we are allowed to mix types, we must be careful that we understand what will happen when a value of one type is converted into a value of another type. For instance, what would happen in Example 3.1 if the value assigned to RealVal was 3.5 or 3.9? Is it rounded up or down?

Allowing the system to convert things in this way is sometimes known as "implicit conversion". SIMULA also provides procedures which can be used to carry out type conversion in a controlled way. In general it is safer to use these "explicit conversion" procedures, so that it is obvious when reading the program what is happening. We shall look at the rules used in implicit conversion at the end of this chapter.

###Types in expressions

The rules about mixing types in assignments also apply in expressions. For instance, we are not allowed to add together an integer and a text.
Example 3.3: A legal mixing of types in an expression.

      begin
         integer IntVal;
         real RealVal;
         
         IntVal := 2;
         RealVal := 69.54;
         RealVal := IntVal + RealVal - 4;
         OutFix(RealVal,2,6);
         OutImage
      end
Example 3.4: An illegal mixing of types in an expression.

      begin
         character CharVal;
         real RealVal;
         
         CharVal := '2';
         RealVal := 69.54;
         RealVal := CharVal + RealVal - "4";
         OutFix(RealVal,2,6);
         OutImage
      end
Again you should try compiling and running examples 3.3 and 3.4. To complete this brief look at types in expressions, here are some rules of thumb about what happens when you mix arithmetic types in expressions and implicit conversion is performed.

Where real and integer values are mixed in an expression, they are all converted to real values.

Where the real division operator, /, is used in an expression all values in the expression are converted to real values.

Where the integer division operator, //, is used in an expression, the values must be integer or an error is reported.

This subject is covered in more detail in the mathematical {{ book.AppendixB }}.

###Types of parameters

Exactly the same rules apply where values are passed as parameters as apply when they are assigned. In effect the parameter given is assigned to a location with the type specified for that parameter. This will be discussed further in {{ book.Chapter5 }}.
###Standard types

We shall now look at each of the simple types provided in SIMULA. For those who wish to use SIMULA for mathematical programming, {{ book.AppendixB }} contains more information on the use of arithmetic types.
###integer

As we have seen, values are of type integer if they are whole numbers. They may be positive, negative or zero.
On any particular SIMULA system there will be a largest positive and a smallest negative number which can be held in an integer location. The documentation for a particular system will tell you what these limits are. There are system constants which contain them and these may be used in your programs to check that your values do not exceed them. They are called MaxInt and MinInt respectively and are described in {{ book.Chapter20 }}. Such limits do not normally cause problems.

In our programs we have used integer constants to represent values being assigned to our integer locations. An integer constant is a whole number written as a sequence of decimal digits, i.e. any digit in the range 0-9. This may be preceded by a minus or, less commonly, a plus sign. A minus sign indicates a negative value; a plus sign has no effect.

Example 3.3: integer constants

        2
        45678231
        -432
        + 1245
Spaces after the plus or minus are ignored. Spaces between digits are not allowed.
It is also possible to give integer constants in other number bases. This is described in mathematical {{ book.AppendixC }}.

###real

A real value is a number which is not necessarily whole. It is held in a form which allows it to contain a fractional part. In common speech it is what is known as a 'decimal' number or decimal fraction, i.e. it is written with a decimal point separating the whole and fractional parts of the number.
A real value is restricted both by a largest/lowest range and by the number of significant decimal places which can be held. Again this will be explained in detail in the documentation for your SIMULA system. There are two system real constants, Maxreal and MInReal, which may be used to check them in your programs. These are described in {{ book.Chapter20 }}. Again they will not cause problems for most users.

Most of us are used to writing decimal numbers (decimal fractions) in what is technically known as "fixed point" notation. The examples of decimal constants used in examples so far are in this form. It can be described as two strings of decimal digits (in the range 0-9) separated by a full stop or period. Like integers they may be preceded by a minus or plus sign.

Example 3.4: Legal fixed point real constants.

        5.7
        236.0
        3246.8096
        -45.87
        +  46.876
The use of spaces is not allowed between digits or between a digit and the decimal point.
Mathematicians often use another notation to write decimal values, especially where these are very large or very small. This way of writing them is known as "floating point" and is also allowed for writing real constants in SIMULA. It is described in mathematical {{ book.AppendixB }}, since most programmers will never use it.

###character

A character holds a value which represents a single character. SIMULA allows you to use any of the characters in the "character set" of the computer that you are using plus the characters defined by the International Standards Organisation (ISO) character set standard. (This is sometimes known as the ASCII character set.) More details on character sets are given in {{ book.Chapter12 }}.
It is important to stress that a character has a different type from a text (see below).

We normally think of a character as something written on a page. In the computing world this sort of character is often referred to as a "printing character". It is probably easy enough for us to accept that a space is also a printing character. It is rather harder to grasp that a character can produce other effects, such as making the printer start a new line or a new page. These are non-printing "control characters". Some of these character values produce very complex effects in certain printers or terminals and no effects or different effects in others.

In general terms the values held in character locations are those which are sent as instructions to printers, terminals and other hardware devices. Most of these instructions control the printing and formatting of written information. The simplest merely instruct the device to print a visible character.

Character constants are normally written as single characters, enlosed in single quotes. Note carefully that a single character enclosed in double quotes is a text constant and may not be used as a character.

Control characters cannot be written in this way. They use their internal integer value (see chapter 12), written as an integer constant and enclosed in exclamation marks, inside single quotes. This notation can also be used to write printing characters, but is very clumsy.

Example 3.5: Legal character constants

        'A'
        'b'
        '4'
        '%'
        ' '
        '''
        '!3!'             Control character, enclosed in exclamation marks.
Note that case, i.e. the difference between capital and small letters, is significant in character constants. Thus 'A' and 'a' are not equivalent.
Note also how a single quote is represented as a character constant.

###Boolean

A Boolean quantity can only have two values. These are True and False.
The use of Boolean quantities may not be intuitively obvious and we merely mention them here. They will be considered in more detail when we look at conditional statements and loops.

A Boolean can be assigned the value of any conditional expression and can be used wherever a conditional expression might be used, e.g. in an if statement or a while loop.

Boolean constants can only be represented by the keywords True and False.

###text

A text variable is used to refer to and manipulate sequences of characters. A sequence of characters is often known as a "string". In the examples using text variables so far we have often assigned strings as the values to be placed in locations declared as type text. From this it might seem that a text and a string are the same thing. In fact a text is more complex than a string and we shall spend most of chapters 5 and 8 looking at what a text really holds.
Text constants are strings, i.e. sequences of characters, surrounded by double quotes. Each character in a string can be any of those in the ASCII (ISO) standard set.

A character in a string can also be represented by its internal integer value enclosed in exclamation marks. See {{ book.Chapter12 }} for details of internal representation.

When a string is too long to fit onto a single line of your SIMULA program it can be continued on the following line by typing a double quote at the end of the first line and again at the start of the second line. These quote characters are ignored and the value of the constant is the string on the first line followed by the string on the second line.

Example 3.6: Legal text constants.

        "The cat sat on the mat"

        "34.56"

        "%"
        
        "This string is typed on two lines, but will be treated as if it "
        "was on a single line, without the second and third double quotes."

        "This string has a control character !10! embedded"

        """"
Note that the last string shown in 3.6 will contain only one double quote. When you want to have a text constant which contains one double quote you must type two, so that the compiler knows that it is not the end of the string. Another example showing this is:
"This string contains only one "", but we must type two."
Note also that the single character text constants "%" and """" are not the same as the character constants '%' and '"'. They have different types.
###Initial values

An identifier of a certain type, which is not declared as a constant (see later in this chapter), is often referred to as a "variable" of that type, since, unlike a constant of the same type, its value can be changed by assigning to it. When we declare such variable, the SIMULA system places an initial value in the location identified. This value is the same on all SIMULA systems on all computers for all variables of a given type.
Thus it is quite legal, and meaningful, in SIMULA to write

      begin
         integer IntVal;
         OutInt(IntVal,4);
         OutImage
      end
since the initial value placed in the location identified by IntVal will be printed. This will be the value initially given to all integer locations.
The values placed in each type of location are given below.

           integer      0
     short integer      0
           real         0.0
      long real         0.0
           character    The ISO NULL character, which is 'invisible'.
           Boolean      False
           text         NoText, the empty text, referring to an empty string.
                        Equivalent to "".
                      
###Implicit conversion of reals and integers

When a real value is assigned to an integer location or vice versa we have said that the value will be converted to one with the type of the location. When integers are converted to reals, no problems are involved in understanding what will happen. The values 3 and 3.0 are usually thought of as identical and such a conversion presents no ambiguities.
On the other hand, when reals are converted to integers, the result depends on how we deal with the fractional part of the real value. How do we get get rid of the figures to the right of the decimal point?

If we go back to our earlier examples we might use them to produce example 3.7. Running this would show the outcome of implicit conversion of reals to integers.

Example 3.7: Implicit conversion and rounding.

      begin
         integer I1, I2, I3;
         I1 := 3.2;
         I2 := 3.9;
         I3 := 3.5;
         OutInt(I1,4);
         OutInt(I2,4);
         OutInt(I3,4);
         OutImage
      end
Clearly more than one outcome is possible.

If all reals are rounded up the output will be

        4   4   4
if down
        3   3   3
and if to the nearest integer value
        3   4   4
or
        3   4   3
the last two depending on whether 3.5 is regarded as nearer to 3 or 4.
Since no armchair programmers are allowed you should now try compiling and running the program and see for yourself what happens. What happened? It was clear enough, I hope. When implicit conversion is relied upon, SIMULA converts reals to integers by rounding to the nearest integer value. 3.5 is rounded up.

There is more on this subject in mathematical {{ book.AppendixB }}.

###Constant declarations

Constant declarations were introduced into SIMULA very late on and are still regarded as controversial by older Simula programmers. They should be used with some restraint if you want to move your programs to other systems. Older SIMULA systems will not have constant declarations.
A constant declaration allows an identifier to be assigned a value in its declaration. This identifier may not then be assigned another value. This can be very useful when using the same value frequently in a program, especially when the value is easily mistyped or has no obvious meaning.

Example 3.8 shows the use of a real constant declaration to represent Pi in a program which calculates the area and circumference of a circle whose radius is typed in.

Any declaration of an identifier of the types in this chapter followed by an equals sign followed by a constant of matching type is a legal constant declaration.

It is also legal to use an expression containing constants and identifiers from earlier constant declarations as the right hand side of such declarations. This can aid readability by showing the way in which some named constants are used to derive others.

Example 3.8: Constant declaration.

      begin
         real Pi = 3.14159;             ! A constant declaration;
         real Radius;                   ! A variable declaration;

         Radius := InReal;              ! Read in the radius;
         OutText("Radius = ");
         OutFix(Radius,6,12);
         OutText("Area = ");
         OutFix(Pi*(Radius**2),6,12);    ! Area=Pi times radius squared;
         OutImage;
         OutText("Circumference = ");
         OutFix(2*Pi*Radius,6,12);      ! 2 times Pi times Radius;
         OutImage
      end..of..program
###Summary

We have looked in some detail at the importance of the type associated with a value. In particular, we have noted that values can only be stored in locations of the same type. Attempts to store them in locations of a different type will result in implicit conversion to the type of the location or an error being reported.
We have seen that mixing of types in an expression is restricted in a similar way. So is the use of types of parameters.

We have looked at the standard types in SIMULA, namely integer, real, Boolean, character and text, defining them and looking at how constants of each type are written.

We have looked at the rules controlling implicit conversions between reals and integers.

We have seen the initial values given to locations of each standard type.

We have seen how constant identifiers may be declared.

{{ book.Chapter4 }}

###Exercises

Correct the following programs.
    1. begin
          integer IntVal;
          IntVal := '3'
       end
    2. begin
          character LETTER;
          LETTER := "J"
       end
    3. begin
          OutInt("34",2);
          OutImage
       end
    4. begin
          OutText('There's many a slip');
          OutImage
       end
    5. begin
          OutText("She whispered,"I love you."");
          OutImage
       end
What will be the value printed by each of the following? Compile and run them to check.
    6. begin
          integer IntVal;
          IntVal := 5.2 + 3.5;
          OutInt(IntVal,4);
          OutImage
       end
    7. begin
          real RealVal;
          RealVal := 3 / 2;
          OutFix(RealVal,2,6);
          OutImage
       end
    8. begin
          real RealVal;
          RealVal := 3 // 2;
          OutFix(RealVal,2,6);
          OutImage
       end
If you do not enjoy mathematical examples, please try these anyway. This is the last time I shall ask you to try any exercises of this kind, unless you are reading the mathematical appendices. From now on only the simplest of arithmetic will be used and only to help us do more interesting things.