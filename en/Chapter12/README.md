#CHAPTER 12 - A Language with Character

##Character handling, switches and jumps

###Working with texts

Most of our examples have been concerned with some sort of manipulation of characters in the form of texts. Occasionally we have used GetChar and PutChar to manipulate the individual characters in them. At other times we have used Getint, PutFix etc. to manipulate groups of characters within texts. Any text processing or editing programs are heavily dependent on the manipulation of individual characters as well as their combinations. This chapter shows those features of SIMULA designed to help in this.
Most of these features are system procedures. We start with two simple ones, shown in example 12.1. This shows a program which finds all the numbers in a text containing a mixture of digits and letters. It is assumed that no other characters will be present.

###Procedures Letter and Digit

SIMULA provides two Boolean procedures, Letter and Digit, both taking a single, character parameter. If this parameter is one of a-z or A-Z the procedure Letter will return the value True, otherwise it will return the value False.
If its parameter is one of 0-9, Digit will return True, otherwise False.

Example 12.1 shows both.

Example 12.1 : Sorting letters and numbers.

    begin
       text Input, LettersOut, NumbersOut;
       character Next;
       InImage;
       Input:-SysIn.Image;
       LettersOut :- Blanks(80);
       NumbersOut :- Blanks(80);
       while Input.More do
       begin
          Next:=Input.GetChar;
          if Letter(Next) then LettersOut.PutChar(Next) else
             if Digit(Next) then NumbersOut.PutChar(Next)
       end;
       OutText(Input);
       OutImage;
       OutText(LettersOut);
       OutImage;
       OutText(NumbersOut);
       OutImage
    end
###Representing characters

Computers store characters in their memory as numbers. Each character is represented by a different integer value.
Unfortunately there is no single system for this. The numbers representing each character can vary from machine to machine. In practice most machines use either the EBCDIC system or the International Standards Organisation (ISO) system. The ISO system is often called by its earlier name, ASCII. The two systems are shown in tables 12.1 and 12.2. These tables are known as the collating sequences for the two systems.

###Using the internal values

It is sometimes useful to be able to find the internal number representing a character or to be able to convert a number into the corresponding character. SIMULA provides procedures for both of these.
Example 12.2 shows a program which converts characters in a text which uses ISO characters into an equivalent text containing EBCDIC characters. This is often necessary when reading files transferred from another computer.

###Rank

System integer procedure Rank takes a single character value parameter and returns the number representing the character.
In 12.2, the characters inside ISOText are in the ISO form and so the call

      Rank(ISOText.GetChar)
will return the internal number of the next ISO character in the text, following table 12.1.
Example 12.2 : Character set conversion.

    begin
       integer Count, ISONumber, EBCDICNumber;
       character ISO, EBCDIC;
       text ISOText, EBCDICText;
       integer array EBCDICChar (0:255);
        for EBCDICNumber := 0,
          1,    2,    3,   55,   45,   46,   47,   22,    5,   37,
         11,   12,   13,   14,   15,   16,   17,   18,   19,   60,
         61,   50,   38,   24,   25,   63,   39,   28,   29,   30,
         31,   64,   79,  127,  123,   91,  108,   80,  125,   77,
         93,   92,   78,  107,   96,   75,   97,  240,  241,  242,
        243,  244,  245,  246,  247,  248,  249,  122,   94,   76,
        126,  110,  111,  124,  193,  194,  195,  196,  197,  198,
        199,  200,  201,  209,  210,  211,  212,  213,  214,  215,
        216,  217,  226,  227,  228,  229,  230,  231,  232,  233,
         74,  224,   90,   95,  109,  121,  129,  130,  131,  132,
        133,  134,  135,  136,  137,  145,  146,  147,  148,  149,
        150,  151,  152,  153,  162,  163,  164,  165,  166,  167,
        168,  169,  192,  106,  208,  161,    7,   32,   33,   34,
         35,   36,   21,    6,   23,   40,   41,   42,   43,   44,
          9,   10,   27,   48,   49,   26,   51,   52,   53,   54,
          8,   56,   57,   58,   59,    4,   20,   62,  225,   65,
         66,   67,   68,   69,   70,   71,   72,   73,   81,   82,
         83,   84,   85,   86,   87,   88,   89,   98,   99,  100,
        101,  102,  103,  104,  105,  112,  113,  114,  115,  116,
        117,  118,  119,  120,  128,  138,  139,  140,  141,  142,
        143,  144,  154,  155,  156,  157,  158,  159,  160,  170,
        171,  172,  173,  174,  175,  176,  177,  178,  179,  180,
        181,  182,  183,  184,  185,  186,  187,  188,  189,  190,
        191,  202,  203,  204,  205,  206,  207,  218,  219,  220,
        221,  222,  223,  234,  235,  236,  237,  238,  239,  250,
        251,  252,  253,  254,  255 do
       begin
          EBCDICChar(Count) := EBCDICNumber;
          Count := Count + 1
       end;
       InImage;
       ISOText:-SysIn.Image;
       EBCDICText:-Blanks (Image.Length);
       while ISOText.More do
       begin
          ISO:=ISOText.GetChar;
          ISONumber:=Rank(ISO);
          EBCDICNumber:=EBCDICChar(ISONumber);
          EBCDIC:=Char(EBCDICNumber);
          EBCDICText.PutChar(EBCDIC)
       end
    end
The integer array EBCDICChar has one hundred and twenty seven characters, corresponding to the ordinary control and printing characters in the two character sets used. Each element of the array is set so that its value is the internal EBCDIC representation of the character whose ISO representation is the number of the index to that element.
As an example, the internal representation of a space character is 64 in EBCDIC and 32 in ISO. Thus element 32 of the array is set to 64.

To convert the ISO values returned by Rank above into EBCDIC, the value returned is used to index the array in a subscripted variable and the value of this will be the EBCDIC representation of the same character.

Using the same example, if Rank (ISOText.GetChar) returns 32, having found a space in the text, this is used to index EBCDICChar, i.e. EBCDICChar(32). The value of element 32 is 64, the EBCDIC representation of a space.

Thus the use of GetChar, Rank and indexing of the array EBCDICChar has found the value of the EBCDIC representation of an ISO character in our text. Now we need to convert this into a character.

###Char

System character procedure Char takes a single integer value parameter, whose value must be legal as the internal representation of a character on that system. (This range should be specified in the documentation for the SIMULA system you are using). Char returns a character whose internal representation is the number passed as a parameter.
It is important to note that Rank and Char do not concern themselves with which character set is being used, only with moving a number, held in some form such as a binary number, from a location reserved for a character to one reserved for an integer or vice versa. Char objects only if the integer is too large to fit into the, usually, smaller space used for a character. Interpreting characters according to ISO, EBCDIC or whatever is only done by some reading and writing procedures.

Thus, in the example, the EBCDIC representation of the character obtained from ISOText, is passed to Char, which returns a character with this as its internal representation. This can then be written as the EBCDIC translation into EBCDICText, using PutChar.

###Avoiding character set problems

When writing character handling programs to run on any computer, it is very inconvenient to have to allow for the possible character sets on each particular machine. This makes it nearly impossible, in fact, to write truly portable programs using the procedures Char and Rank. Fortunately SIMULA has a way of avoiding this.
On any but the oldest SIMULA systems, two more system procedures are supplied. They are called ISOChar and ISORank. They match Char and Rank exactly except that they work entirely in terms of the ISO character set.

For ISORank, this means that the value returned is converted from the internal representation of the character parameter in the machine's own character set, to the ISO internal representation.

For ISOChar, it means that when the integer parameter is converted into a character, it is first converted into the internal representation of the character that it would represent in the ISO set.

If we take our space character on a machine using EBCDIC as its internal character set, we would find the following results with ISOChar and ISORank.

Eample 12.3 : Lower to upper case conversion using ISORank and ISOChar

    begin
       text Buffer, Update;
       integer Convert, Factor;
       character Next;
       Buffer:- Copy("This little piggy was Fred");
       Update:- Buffer;
       Factor := ISORank('A') - ISORank('a');
       while Buffer.More do
       begin
          Next:= Buffer.GetChar;
          Convert:= ISORank(Next);
          if Convert GE ISORank('a') and Convert LE ISORank('z') then
          begin
             Convert:= Convert + Factor;
             Next:= ISOChar(Convert)
          end;
          Update.PutChar(Next)
       end;
       OutText(Buffer);
       OutImage;
       OutText(Update);
       OutImage
    end

If we call Rank (' '), we get 64 returned, which is the local, EDCDIC, internal representation of a space. A call of ISORank (' '), on the other hand, will return us 32, the ISO equivalent.
Conversely, to get Char to return a space, we need to call Char(64), but using ISOChar we must call ISOChar(32).

Using these procedures we can write totally portable character handling programs. Example 12.3 shows how to write a portable program to convert all the lower case characters in a text into upper case. This is sometimes called "folding" the text into upper case.

The difference between ISORank('A') and ISORank('a') is the same as the difference between the internal representations of each upper case character and its corresponding lower case character, in the ISO character set. Thus the value assigned to Factor will enable us to convert any lower case character's ISORank to that of its upper case equivalent.

The program checks each character to find if it is a lower case letter. Lower case letters in the ISO sequence are represented by consecutive numbers, starting with 'a' and ending with 'z'. This makes it easy to check whether the Rank of each character is within this range. (This would not work with EBCDIC. See table 12.2.)

Note how useful and is in these checks. We can use a single if statement, instead of two nested ones. Checking that a number lies within a certain range is a very common use of and.

When the program finds a lower case letter it adds Factor to its ISORank, putting the result in Convert.

The program uses the technique, seen earlier, of creating two references to the same text frame. By reading through one reference and writing through the other, at an equal rate and left to right, we update the text frame without the need to copy into another and back again.

Note that this program would not work for the EBCDIC character set, if Char and Rank replaced ISOChar and ISORank. This demonstrates the usefulness of the ISO procedures in writing portable programs rather neatly.

###Exercises

12.1 Because of hardware problems, a file has been corrupted. It now contains a number of unprintable characters. Assuming that letters, digits, spaces, full stops, commas, colons and semi-colons are the only characters which should be present, write a program which will remove the others.
12.2 Write a program which converts all upper case letters to lower and all lower to upper in a file.

12.3 Go back to exercise 7.8. Using this add the capability to your editor to change the case of the next letter from its current position.

###Making quick decisions

When writing our text formatting program in chapter eleven, we were forced to use more and more deeply nested if statements to check which directive was being read. This becomes rather clumsy and difficult to read when more than a few choices are possible. It is also slow to run when lots of checks must be made before a course of action is selected.
The help with this sort of situation, SIMULA contains a feature called a switch. To see how this works, consider example 12.4. This is example 11.1 rewritten with a switch.

Example 12.4 : The use of a switch

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
                   Contents(Count):=InText(Width)
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

             switch Action := TitleB, TextB, DiagB;
             Character ActionCode;

             Directive :- InText(2);
             while Directive ne "$E" do
             begin
                Directive.SetPos(2);
                ActionCode := Directive.GetChar;
                go to Action(ISORank(ActionCode) - ISORank('A'));
       TitleB :    ! Directive = $B - New banner;
                Len := InInt;
                InImage;
                Add(New Title_Block(80,Len,InText(80)));
                go to Repeat;
       TextB :     ! Directive = $C - New content;
                Add(new Text_Block (80,Inint));
                go to Repeat;
       DiagB :     ! Directive = $D - New diagram;
                Len := InInt;
                InImage;
                Add(new Diagram(80,Len,InText(80)));
                go to Repeat;
       Repeat: 
                Directive :- InText(2)
             end.of.while.loop 
          end--of--Page;
          new Page
       end.of.program
The letters in the directives are in alphabetic sequence starting with 'B'. Thus they follow the ISO collating sequence. Thus, by subtracting ISORank ('A') from the ISORank of each directive's letter, we can obtain a value between one and three, inclusive. Each integer value so obtained represents one of the three directives.
###switch declarations

The switch declaration is unusual, in that it contains the value assignment operator, :=, as well as identifiers. The syntax of such a declaration is the keyword switch, followed by an identifier giving the name of the switch, followed by the value assignment operator, followed by a list of so called "designational expressions".
Designational expressions can take a number of forms. In the example they all have the commonest form, a simple identifier.

The identifiers used in such a list specify places in the program to which a "jump" may be made. If you look at the example, three identifiers are listed. (The minimum is one and the maximum will be different on different SIMULA systems.) Further on in the program, each of these identifiers occurs again, followed by a colon. This second occurence is called a label.

###Label declarations

An identifier followed by a colon is a declaration of a label for the following statement. Such a declaration is different from those of any other type, since it can occur in the middle of a sequence of statements, rather than before any statements. The example contains four label declarations. The first three, TitleB, TextB and DiagB are used to label the next statement. The fourth, Repeat, appears to label the keyword, end. end is not a statement and so to preserve the rule that label declarations always precede statements, there is said to be an imaginary statement between the colon and end.
###go to statements with switches

The switch, Action, is used in a statement starting with the keywords go to. In fact these can be written as a single keyword goto, if you prefer. This is the only combination of keywords where this is allowed.
A go to statement is the keyword(s) go to followed by a designational expression. In the example the switch identifier, Action followed by an integer value in parentheses is used. This is the other form of designational expression that we shall use. This integer value is an index to the list of designational expressions in the switch declaration above. Thus it can be used to identify a label declaration and through this the next statement to be executed.

In the example, we have seen that the value generated when we evaluate

      ISORank(Action Code) - ISORank('A')
should be an integer in the range 1-3. The designational expressions in a switch declaration are assumed to be numbered consecutively starting with 1. Thus, if the directive $C is found, the index to Action in the go to statement will be 2, and so the second label in the declaration of Action, TextB, will mark the next instruction to be executed.
This leads us to the meaning of a go to statement. It causes the program to move to the statement whose label is identified by the designational expression in the go to statement. No other statements are executed before this jump. After the jump the program continues with the statement following the label. Such a jump may be forwards, as in all the cases shown, or backwards.

To see this, consider again the $C directive. The first go to is followed by

      Action(ISORank(Action Code) - ISORank('A')),
which, as we have seen, will equal 2. This means that the label to use is identified by the second item in the list of designational expressions in the declaration of Action. This is the identifier TextB, which is the name of a label. The label TextB is further on in the program. The program therefore misses the intervening statements and continues from the statement

      New Text-Block (80,InInt).
If the directive $D is encountered, the program jumps to the third label in the switch declaration, DiagB. If $B is encountered it jumps to the first. Check the logic of this for yourself.
go to statements with labels

A go to statement makes the program jump to a label specified by a designational expression. A subscripted switch variable is one form of designational expression, but we have seen a simpler one. This is a simple label identifier.
In the example three statements have the form of go to followed by an identifier. As it happens, the identifier is the same in each case. The effect is again to jump to the statement labelled with the identifier found from the designational expression. In the case of a simple identifier this is very straightforward.

These three statements all cause the program to jump to the statement labelled by Repeat. This takes the program to the InImage at the end of the compound statement of the while loop. In other words, once the actions for that directive are complete, the program jumps to start processing the next line, which is assumed to contain the next directive.

###Exercises

12.4 By extending the list for the switch declaration, moving the position of Repeat and adding a new label declaration, rewrite example 12.4 without the while loop.
12.5 What would happen in example 12.4 if the go to Repeat statements were missing? Try removing them to check. Note the effect carefully.

###Notes and warnings

Some people write very intricate programs, which use lots of go to statements. In certain, rather limited, programming languages this is necessary. In SIMULA it is almost never needed.
The use of too many go to statements makes programs very hard to read and understand. They should only be used when absolutely necessary or in the sort of situation shown in example 12.4, where a switch can simplify a program and make it easier to extend.

It is important in a program using a switch to provide label declarations to match all the designational expressions in the switch declaration. It is not illegal to declare a switch which leads to non-existent labels. It is a runtime error to try to jump to one. In fact it is often best to check before the go to statement that the value of the expression used in the subscripted switch variable is not too large for the list in the declaration and to print an error message or warning if necessary.

###A very special jump

A rather new feature in SIMULA, which may not exist in some older systems, is the system procedure Terminate_Program. This causes a jump to the very end of the program, regardless of the current position. This can be useful in providing warnings of disastrous errors and then stopping the program.
Terminate_Program is the only way for a program using a prefixing separately compiled class containing an @i(inner) statement (see chapter 16), to stop itself without executing the instructions following that @i(inner). It is the only way to stop a program dead from anywhere within it and guarantee that no further actions will be carried out.

###Switches and labels as parameters

It is possible to pass switches and labels as parameters to procedures. The default mode is reference and name is also legal.
Using such a parameter, or a switch or label from a block enclosing the one the program is currently in, it is possible to jump out of a block. The normal rules for ending the appropriate type of block will apply.

###Summary

We have looked at how characters are represented by numbers and seen the procedures which allow us to use this.
We have seen the use of switches and labels, in go to statements and as parameters.

We have learned the benefits and, most importantly, the dangers of go to statements.

We have seen the system procedure Terminate_Program.

Table 12.1: ISO character set - printing characters.

        0   nul    1   soh    2   stx    3   etx    4   eot    5   enq
        6   ack    7   bel    8    bs    9    ht   10    lf   11    vt
       12    ff   13    cr   14    so   15    si   16   dle   17   dc1
       18   dc2   19   dc3   20   dc4   21   nak   22   syn   23   etb
       24   can   25    em   26   sub   27   esc   28    fs   29    gs
       30    rs   31    us   32 space   33     !   34     "   35     #
       36     $   37     %   38     &   39     '   40     (   41     )
       42     *   43     +   44     ,   45     -   46     .   47     /
       48     0   49     1   50     2   51     3   52     4   53     5
       54     6   55     7   56     8   57     9   58     :   59     ;
       60     <   61     =   62     >   63     ?   64     @   65     A
       66     B   67     C   68     D   69     E   70     F   71     G
       72     H   73     I   74     J   75     K   76     L   77     M
       78     N   79     O   80     P   81     Q   82     R   83     S
       84     T   85     U   86     V   87     W   88     X   89     Y
       90     Z   91     [   92     \   93     ]   94     ^   95     _
       96     `   97     a   98     b   99     c  100     d  101     e
      102     f  103     g  104     h  105     i  106     j  107     k
      108     l  109     m  110     n  111     o  112     p  113     q
      114     r  115     s  116     t  117     u  118     v  119     w
      120     x  121     y  122     z  123     {  124     |  125     }
      126     ~  127   del  128   ctl  129   ctl  130   ctl  131   ctl
      132   ctl  133   ctl  134   ctl  135   ctl  136   ctl  137   ctl
      138   ctl  139   ctl  140   ctl  141   ctl  142   ctl  143   ctl
      144   ctl  145   ctl  146   ctl  147   ctl  148   ctl  149   ctl
      150   ctl  151   ctl  152   ctl  153   ctl  154   ctl  155   ctl
      156   ctl  157   ctl  158   ctl  159   ctl  160   ctl  161   ctl
      162   ctl  163   ctl  164   ctl  165   ctl  166   ctl  167   ctl
      168   ctl  169   ctl  170   ctl  171   ctl  172   ctl  173   ctl
      174   ctl  175   ctl  176   ctl  177   ctl  178   ctl  179   ctl
      180   ctl  181   ctl  182   ctl  183   ctl  184   ctl  185   ctl
      186   ctl  187   ctl  188   ctl  189   ctl  190   ctl  191   ctl
      192   ctl  193   ctl  194   ctl  195   ctl  196   ctl  197   ctl
      198   ctl  199   ctl  200   ctl  201   ctl  202   ctl  203   ctl
      204   ctl  205   ctl  206   ctl  207   ctl  208   ctl  209   ctl
      210   ctl  211   ctl  212   ctl  213   ctl  214   ctl  215   ctl
      216   ctl  217   ctl  218   ctl  219   ctl  220   ctl  221   ctl
      222   ctl  223   ctl  224   ctl  225   ctl  226   ctl  227   ctl
      228   ctl  229   ctl  230   ctl  231   ctl  232   ctl  233   ctl
      234   ctl  235   ctl  236   ctl  237   ctl  238   ctl  239   ctl
      240   ctl  241   ctl  242   ctl  243   ctl  244   ctl  245   ctl
      246   ctl  247   ctl  248   ctl  249   ctl  250   ctl  251   ctl
N.b. Ranks less than 32 and greater than 126 are not visible printing characters. Where appropriate the standard control meaning is supplied. Ranks greater than 127 are normally used for graphics or alternative character fonts.
Table 12.2: EBCDIC character set - printing characters.

        0   nul    1   soh    2   stx    3   etx    4          5   ht
        6          7   del    8          9         10         11   vt
       12    ff   13    cr   14    so   15    si   16   dle   17   dc1
       18   dc2   19   dc2   20         21         22   bsp   23     
       24   can   25    em   26         27         28    fs   29    gs
       30    rs   31    vs   32         33         34         35     
       36         37    lf   38   etb   39   esc   40         41     
       42         43         44         45   enq   46   ack   47   bel
       48         49         50   syn   51         52         53     
       54         55   eot   56         57         58         59     
       60   dc4   61   nak   62         63   sub   64 space   65      
       66         67         68         69         70         71      
       72         73         74         75     .   76     <   77     (
       78     +   79     |   80     &   81         82         83      
       84         85         86         87         88         89      
       90     !   91     $   92     *   93     )   94     ;   95     ~
       96     -   97     /   98         99        100        101      
      102        103        104        105        106     |  107     ,
      108     %  109     _  110     >  111     ?  112        113      
      114        115        116        117        118        119      
      120        121     `  122     :  123     #  124     @  125     '
      126     =  127     "  128        129     a  130     b  131     c
      132     d  133     e  134     f  135     g  136     h  137     i
      138        139        140        141        142        143      
      144        145     j  146     k  147     l  148     m  149     n
      150     o  151     p  152     q  153     r  154     ^  155      
      156        157        158        159        160        161     ~
      162     s  163     t  164     u  165     v  166     w  167     x
      168     y  169     z  170        171        172        173     [
      174        175        176        177        178        179      
      180        181        182     ^  183        184        185      
      186        187        188        189     ]  190        191      
      192     {  193     A  194     B  195     C  196     D  197     E
      198     F  199     G  200     H  201     I  202        203      
      204        205        206        207        208     }  209     J
      210     K  211     L  212     M  213     N  214     O  215     P
      216     Q  217     R  218        219        220        221      
      222        223        224     \  225        226     S  227     T
      228     U  229     V  230     W  231     X  232     Y  233     Z
      234        235        236        237        238        239      
      240     0  241     1  242     2  243     3  244     4  245     5
      246     6  247     7  248     8  249     9  250        251      
      252        253        254        255
N.b. EBCDIC character sets vary from machine to machine. This table is only one variant, example 12.2 uses an alternative mapping between EBCDIC and ISO to allow a complete conversion.

{{ book.Chapter13 }}