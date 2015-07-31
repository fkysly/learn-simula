#Chapter 2

#\- and end at the end

##The nature of correct programs

###Rules and penalties

Chapter one gave an example of a correct program. When you altered it as you were asked to in exercise 1.3 and recompiled it, some of the results will have been incorrect programs. (If you have not yet completed the exercises in chapter 1, you should do so now. It will be much easier for you to follow this chapter.)
The compiler will have printed error messages for some of these variations. It may also have printed warnings. Sometimes, though probably not in any of your attempts at 1.3, the compiler will report success in producing the machine instruction version, but the part of the SIMULA system which runs the program, which we call the runtime system, will print an error message or warning during the execution of the program.

Clearly there are rules to be followed in writing programs. In chapter 1 some were mentioned in passing, but only two were spelt out. One was that every block starts with the keyword begin and finishes with the keyword end; the other was that the rest of a program is made up of declarations and statements.

To be correct a program must follow all the rules of SIMULA. Sometimes the compiler can detect mistakes; sometimes they only show up when we run the program; sometimes, but very rarely, the compiler and runtime system both fail to detect an error, with unpredictable results.

Even when a program compiles and runs without producing error messages, the program is only correct if the actions performed are those intended. In this case we have written a correct SIMULA program, but not the correct program to perform the task required. This book will teach you to write correct SIMULA programs. It can only guide you towards writing programs which perform correctly. The rest is up to plain common sense, planning and persistence - the three Ps of programming.

When it comes to writing correct SIMULA programs, there are two sets of rules. Although it is not always sensible to separate them, the error messages from the compiler may refer to mistakes in this way and so we will look at them briefly now.

###Syntax rules

The commonest errors reported by a compiler are those which do not obey the grammar or "syntax" of the language. Often they are the result of typing errors. If you still have not managed exercise 1.1, this is probably because of a syntax error caused by mistyping.
The rule that a program block must start with begin and finish with end is a syntax rule.

###Syntax of declarations

The declarations

     integer Int1;
and

     integer Int1,Count;
both follow the syntax rules for declarations. A declaration has to be a keyword giving the type, followed by an identifier list. An identifier list is either a single identifier or a series of identifiers, separated by commas, with the option of as many spaces as desired either side of the commas.

The syntax rules for SIMULA, like those for most programming languages, are very strict. You cannot omit the space which indicates the end of the keyword integer, without breaking the syntax rules for a declaration.

Sometimes breaking the rules for one sequence, will produce a syntactically correct piece of SIMULA, with a different meaning. For instance, if we had mistyped the comma in the second declaration above, hitting the semi-colon key instead, we would have got the following

     integer Int1;Count;
which clearly violates the syntax rules for a declaration.

As we shall see later, this sequence could still be correct SIMULA, meaning the declaration of an integer called Int1, followed by a call on a procedure called Count. The grammar of the sequence is correct, but its meaning is now different and wrong. In this case the compiler would still probably report the error, but as one of the second category of compiler detected errors, which are dealt with below.

Before considering this second category, let us look at some more syntax rules. These still deal with our earlier examples and will probably answer some questions which have occurred to you already.

###Syntax of identifiers

We have used the word identifier as the technical term for the name given to something in our programs. So far we have not considered what an identifier must look like.
The identifiers which we have used so far are

                Int1
                Count
                OutInt
                OutImage
                Real1
Notice that procedure names are identifiers and follow the same rules. Keywords have the same syntax as identifiers, but they are not available for the programmer to define or redefine.

The meaning of an identifier is defined by a declaration. System identifiers, like OutInt and OutImage, are already defined outside the program, but can be redefined inside it. This, however, is straying into our second category of rules. The syntax rules are as follow.

An identifier is a sequence of letters, numbers and underline characters (the last are known sometimes as break characters). An identifier must start with a letter.

Some systems set a limit on the number of characters in an identifier. Others allow long identifiers, but only look at the first so many characters. You should consult the documentation for any system before using it, especially when moving a program from one system to another.

Letters are often called "alphabetic characters", numbers "numeric characters" or "digits". Mixtures of these two types are called "alphanumeric characters".

The following are valid identifiers

     TOTAL
     A1 
     NEW4SUB6
     MAX_SIZE 
     G43 
     I
###Syntax of blocks

We have already looked at the syntax of blocks informally, now let us be a little more formal. The rules for a block are as follow.
A block starts with the keyword begin, which, like all keywords, must have at least one space following it or be the last word on a line.

This keyword is followed by a list of declarations or a list of statements or a list of declarations followed by a list of statements. Statements and declarations are separated by semi-colons or keywords. All declarations must come before any statements in a block.

A block ends with the keyword end.

The following are valid blocks

    Example 2.1
      begin
      integer I
    end
    
    Example 2.2
  		begin
    	Result := 4
    end
    
    Example 2.3
	    begin
		    integer I;
		    real Arc;
		    I := 5;
		    Arc := 3.2;
		    OutInt(I,3);
		    OutImage
	    end
Although all these blocks are syntactically correct, 2.1 will not result in any actions, since it contains no statements. 2.2 will cause an error to be reported, since there is no declaration to set aside space and define a type for Result before it is used in the statement.

###Exercises

You are probably getting rather impatient with all these rules. Let us try out a few of them. In each of the following program blocks, find and correct the syntax errors.

    1.  begin
          integer I1.I2;
          I1 := 3;
          I2 := I1
          OutInt(I2,4);
          OutImage
        end

    2.  begin
          Res := 4;
          integer Res;
          OutInt(Res,6);
          OutImage
        end
    3.  begin
          OutInt(609;6);
          OutImage
        end
    4.  begin
          Outtext("LINE ONE");
          OutImage;
          Outtext"LINE TWO";
          OutImage
        end
You should also go back to the error messages you got in {{ book.Chapter1 }} and see if you can understand them better now.
###What about statements?

The only major component of our program blocks for which we have not given proper syntax rules is the statement. In fact statements can have many different forms and we need only concern ourselves with a few at a time. We have used two forms of statement so far - procedure calls and assignments - and we can give some rules about these. In addition we can note that a block can be used in place of a simple statement. In this case it is called a "sub-block" or a block which is "local" to the program block. The reason for such local blocks will become apparent later, but their use is a major feature of SIMULA.
A procedure call is an identifier (the name of the procedure) followed in some cases by a parameter list which is enclosed in brackets. The parameter list is a list of identifiers, constants and expressions, separated by commas.

We have already defined identifiers. We shall define constants properly in the next chapter. Expressions are sequences of SIMULA which give values. They are dealt with briefly when we consider assignments below and more fully in mathematical {{ book.AppendixA }}. Those of us who are not interested in mathematics should only need an informal understanding of expressions and can omit this appendix without missing anything.

Examples 2.4: Procedure calls.

	OutImage	No parameters. Moves output to next line.
	Outtext("HI")	One parameter, a text, which is printed on the current line. In this example a text constant is used as the parameter.
	OutInt(643,3)	Two parameters, both integers, separated by a comma. Prints out the first integer on the current line, padding on the left with spaces if this produces fewer figures than the second integer. Either or both the integer constants given as parameters in the example could have been replaced with identifiers or expressions.

Assignment statements have an identifier followed by optional spaces, followed by the assignment "operator", followed by optional spaces, followed by an expression. The assignment operator is the sequence colon followed by equal-sign, :=. Before giving an informal description of expressions, it is probably best to consider the examples of assignment statements given as 2.5.

Examples 2.5: Assignment statements.

    Res := 3

    Count := Last

    Count := Last + 1

    Message := "PLEASE TYPE YOUR NAME"

    Next   :=   Average*Number + 2
The use of spaces before and after the assignment operator has no effect on the correctness of the statement. This applies to all "operators" in SIMULA.

###Expressions

Several kinds of expressions are shown to the right of the assignment operator in these examples. The simplest of these is a constant, such as 3 or "PLEASE TYPE YOUR NAME". These are explained more fully in the next chapter.
An expression can also be a single identifier, such as Last in the second example.

The remaining examples show identifiers and constants separated by operators. Thus

         Last + 1
is the identifier Last followed by the addition operator followed by the constant 1.

         Average*Number + 2
is the identifier Average followed by the multiplication operator followed by the identifier Number followed by the addition operator followed by the constant 2. We shall not attempt a complete definition of expressions, but explain them as we need to use them. These examples should give a feel of what is meant.

###A note on operators

The commonest arithmetic operators are given below. For a complete outline of mathematical operators, see appendix A.

         +   Addition
         -   Subtraction
         *   Multiplication
         /   Division with non-whole numbers (reals)
         //  Division with whole numbers (integers)
         :=  Assignment
###Saying what you mean

As we looked at the rules for writing parts of SIMULA programs, we found that it was possible to write SIMULA whose syntax was correct but which was still illegal. There are extra rules to be obeyed. These spell out the meaning of a sequence of SIMULA. When the way we write the parts of such a sequence is correct, it is still possible that we have put these parts together in a way which has an illegal meaning. These rules of meaning are sometimes called the semantic rules or, more simply,semantics of the language.
English has syntax and semantics in the same way, although they are far more complex than those for SIMULA. Let us look at an example in English of a sentence whose syntax (or grammar) is correct, but whose semantics (or meaning) is nonsense.

"The dish ran away with the spoon."
This sentence obeys the normal rules of English grammar. The reason that it would be rejected as nonsense is that the nouns, "dish" and "spoon", are defined in such a way that they do not fit the verb, "ran away". By changing the nouns or the verb we can produce a sentence with the same syntax, but with a sensible meaning, such as

			"The dish belonged with the spoon."
In SIMULA we could produce the same effect in the following program block.

      begin
         text T1;
         T1 := 3
      end
Here the operator ":=" acts like the verb and "T1" and "3" act like nouns. "T1" and "3" represent types which cannot be assigned to one another. The statement "T1 := 3" is nonsense because the meaning of "T1" is wrong. Let us take one more example from English, before we spell out the most important rules for the SIMULA building blocks we have used so far.

			"Come to my arms my beamish boy."
Lewis Carroll delighted in writing syntactically correct nonsense, like Jabberwocky. The sentence above illustrates a favourite device. The only word that makes this nonsense is "beamish". From its position in the sentence we know that this must be an adjective describing "boy", but there is no such adjective. It has no known meaning. Our minds may be able to infer some meaning (this is a large part of the fun of nonsense poetry) but that does not make unambiguous sense out of nonsense.

The following SIMULA program block contains a similar use of a meaningless concept, i.e. one which has no properly defined meaning.

      begin
         T1 := 3
      end
This time we have not defined T1 as having a type which fails to match "3". T1 has not had any type defined for it at all. It has not been declared before being used and therefore no space has been reserved for it and no type has been allocated to it.

So, a few rules about how we must write SIMULA to avoid semantic errors.

###Semantics of declarations

**The same identifier cannot be declared twice in a block.**
The identifier is used to name a space in the computer's memory which is large enough to hold a value of the type specified. Whenever the identifier is used subsequently, it refers to this space and this type of value.

###Semantics of procedure calls

**A procedure call must have the correct number of parameters.**
Each parameter must be of the correct type. This is explained in more detail in the next chapter.

The actions of the procedure are performed, using the parameters to provide information for the procedure if necessary.

###Semantics of assignments

**The type of the expression to the right of the assignment operator must be "compatible" with the type of the identifier on the left.**
The value of the expression on the right will be stored in the location reserved for the identifier on the left.

###Semantics of expressions

**The types of the quantities in an expression must be compatible. The type associated with the value of an expression is determined by the types of the quantities in it, except when the division operators are used. In this case the type is the same as the operator, i.e. integer for "//" and real for "/".**
The working out of an expression by the SIMULA system, known as its "evaluation", is done in the following order:

Working from left to right all multiplications and divisions are performed;

Working from left to right all additions and subtractions are performed.

This is explained more fully in {{ book.AppendixA }}.

###Semantics of blocks

Any identifier used in a statement in a block must already have been declared.
The statements in the block are performed in the order indicated.

###Syntax and semantics of a comment

**A comment is a special sequence in SIMULA which is treated as a space by the compiler. It contains a message which explains what is going on around it in human terms. Its syntax is quite simple.**
A comment is:

The keyword comment, followed by any sequence of characters ending at the first semi-colon, ;.

or

the character !, followed by any sequence of characters ending at the first semicolon.

or

Any sequence of characters following the keyword end, ending at the first occurence of the keyword end, else, when or otherwise or a semi-colon or the end of the program.

Example 2.6 shows a program which uses all of these forms. It is very fully commented. In practice you will probably not need this sort of detail, but better too many comments than too few. In our worked examples I shall include comments, partly to help you understand things and partly to get you into the habit of using them. They will help you see what is going on, but only in the important places.

A word of warning is needed here. Be very careful to end your comments properly. If you forget a semi-colon or keyword, as appropriate, you will lose some of the following program, since it will be treated as part of the comment. In particular, remember that the end of a line does not end a comment, as the first instance in example 2.6 shows.

To be consistent we need the semantic rules for comments. Comments have no meaning in the program.

Example 2.6: The use of comments.

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
###Summary

This has been a longer chapter and has introduced a lot of important ideas. You should not worry if you do not see the point of some of it yet. Whenever we come to look at a new feature in SIMULA you will see how the concepts of syntax and semantics help define the ways in which the feature can be used. Within a few chapters you will find that you are using words like syntax without noticing. In the meantime, you may need to refer to this chapter while reading some of the following chapters, until you feel confident.
We have looked in this chapter at the rules that tell you how you must write your program for the compiler to be able to make sense of it. These are called the "syntax" of SIMULA.

We have defined the syntax of comments, identifiers, declarations, some types of statements and blocks. We have looked at, but not given any definite rules for, expressions.

We have looked at how meaningless programs can be written, even when the individual parts of the program are correct. The rules that define the meaning of the program are called the "semantics" of SIMULA.

We have seen the important semantic rules for those parts of SIMULA whose syntax we had described earlier.

We have looked at the rules for comments, which have no meaning, only syntax.

###Exercises

Correct the errors in the following program blocks.
    1.  begin
          integer First;
          First := 3;
          Second := First
        end

    2.  begin
          integer Next;
          Next := 4;
          OutText(Next);
          OutImage
        end

    3.  begin
          text Line;
          Line := "MESSAGE ONE";
          OutImage(Line)
        end

    4.  begin
          integer I;
          I := "3";
          OutInt(I+2,4);
          OutInt(I-4,I,3);
          OutImage
        end

    5.  begin
          integer One,Zero;
          One := 1;
          Zero := 0;
          One := One // Zero
      end
What is different about 5?