#Chapter 1

##begin at the beginning......

###What is a computer program?

A computer program (program is always spelt in the American way) is a series of instructions which contains all the information necessary for a computer to perform some task. It is similar to a knitting pattern, a recipe or a musical score. Like all of these it uses a special shorthand, known in this case as a programming language.
This book describes how to write and understand programs written in the language SIMULA. The definition used is the 1985 SIMULA Standard, which extends and clarifies the language previously known as SIMULA 67. The 67 stood for 1967, the year in which this earlier version was first defined.

These should not be confused with the older language known as SIMULA 1, which is an ancestor of SIMULA 67 and thus of today's SIMULA.

A few old references to SIMULA (notably a 1966 article in CACM) will mean SIMULA 1. More often SIMULA 67 is intended. Many implementations of SIMULA will not yet have all the features given in the 1984 standard, although they will probably have more than those defined in 1967. Wherever sensible this book tries to warn its readers of features which may not be universally available. In addition, it is usually a good idea to check the documentation for the particular SIMULA system to be used, in case it has any peculiarities of its own. SIMULA is highly standardised, but differences may still be found.

You may already be familiar with other programming languages such as Fortran, Basic or Pascal. Even so you will find this introduction essential, since SIMULA views the world rather differently in some ways. In particular SIMULA expects you to organise or structure your program carefully. It also allows you to describe actions as if they were happening simultaneously or in parallel.

If the next paragraph seems confusing, read it now and see how much you can understand, then come back after each chapter until it makes complete sense. Do the same for any other parts of this book which are not clear at first reading. Remember that in learning a new computer language you are trying to take in many concepts which only make sense together, so that often several re-readings of what will later seem obvious points are necessary.

A SIMULA program is made up of sequences of instructions known as blocks, which act independently to varying degrees, but which are combined to produce the desired overall effect. The simplest programs contain only one block, known as the program block. All other blocks follow the same basic rules, so let us have a look at the single block program, example 1.1. Example 1.1: A simple example

                   begin
                     integer Int1;
                     comment The first SIMULA program written for this book;
                     Int1:=3;
                     OutInt(Int1,4);
                     OutImage
                   end
                   
This is not the simplest possible program. We could have written

                   begin  end
and had a complete program block. This tells us the first rule about all blocks. **A block always begins with the word begin and ends with the word end.**

beginand end are called "keywords", since they are reserved for special purposes and may not be used for anything else.

###A note on letters and "cases".

Although the programs shown in this book use bothcapital letters, which are known to computer programmers as "upper case" characters, and small, "lower case", letters. You are free to use whichever case you prefer. A SIMULA program is interpreted as if all its letters, which are not enclosed in double quotes, were first converted to upper case. You can even write BegIN or eNd.
###The right ingredients

So we have to put begin and end. What about the text in between?
This is made up of instructions to the SIMULA system, which are of two types; DECLARATIONS and STATEMENTS. There are also pieces of the text which are ignored by the SIMULA system and which simply help any human who reads the program to follow what is happening. These are called comments.

###Declarations

In example 1.1, the first instruction after the word begin is a declaration:

                  integer Int1;
Declarations in a block show how much of the computer's memory is going to be needed by that block. They also show into how many sections it is to be divided , how big each is to be and to what sort of use each will be put. Each of these sections is given a name, by which the program will refer to it.

The program block in example 1.1 has only one declaration. It starts with the "type" keyword integer, followed by a name or "identifier", Int1.

The type (integer in this example) says what the section of memory will be used for. In example 1.1 it will be used to hold a whole number, which is called an integer in SIMULA. Once a section of memory has been given its type in this way, it may not be used to hold something of a different type.

Since this location will hold an integer its required size will also be known to the SIMULA system. All items of the same type require the same amount of space in memory to hold them.

The identifier Int1 is now the name of the space reserved in this way. We may sometimes refer to the value held in this space as Int1 also. This is a useful shorthand, especially when we are not aware what the current contents are.

If we want to use more locations, we can declare them in the same way, being careful to give the correct type to each. Thus we might write

                begin
                  integer Int1;
                  real Real1;
                      etc.
Which gives us a location of type real and called Real1, which we can use in this block. The possible types and their uses are explained more fully in chapter 3.

If we want to have more than one location which can hold a whole number, then we can declare a second integer, with a different identifier. We might do this by writing

                begin
                  integer Int1;
                  integer Count;
                      etc.
or, alternatively, by writing

                begin
                  integer Int1, Count;
                      etc.
which many people find neater. This style is called a declaration list, with a series of identifiers of locations with the same type separated by commas.

###Comments

The next line is a comment. It begins with the keyword comment, is followed by some text and ends in a semi-colon.

      comment The first SIMULA program written for this book;
It has no effect on what the program does, but it contains some information on what the program is or does. In example 1.1 it tells us which version of the program we are looking at. This is often very useful.

As well as forming lines on their own, comments can also be used in certain places within instructions or combined on the same line as them. This will be shown in the next chapter.

When you are writing programs in independent sections, as we will be, you may not look back at a "working" component for some time. It is also possible that someone else may need to change what you have written. The design of the overall system should tell him what the component is supposed to do, but the details of how it works may not be obvious. To help everyone understand your programs, you should include comments in them.

###Statements

The other instructions in our example are all statements. They tell the SIMULA system what it is to do.
We shall be looking at the different sorts of statements which exist in SIMULA in detail later. For the moment, let us look briefly at those in the example to get a flavour of the possibilities.

The first one is an "assignment" statement. It tells the system to place something in one of the locations declared for use by this block. In this case the value three is to be stored in the integer location declared as Int1. Since this value is of a type which matches that declared for the location the statement is legal SIMULA.

The next statement uses something called OutInt, which is a "procedure" and is available in all SIMULA systems. OutInt will write out the first number in the parentheses - ordinary brackets - after it. It writes it as a whole number at the end of the line, or "image", which is currently being created. The second number is used to tell the system how much space to use on the line, when writing out the first number.

The final statement uses OutImage. Like OutInt, OutImage is a procedure and is available in all SIMULA systems. Such standard procedures are known as "system procedures". They are not keywords, since you may declare a different meaning for them within a block if you wish.

OutImage causes the line which is currently being created - the current image - to be written to the printer or terminal and a new line to be started. You have probably noticed that it does not have any parentheses or numbers after it. These numbers, known as "parameters", are only present when the procedure needs information from the program to carry out its task.

###What do we do with our program?

So now we have what we think is a legal SIMULA program. Unfortunately it is on paper and whilst we know what it should cause to happen, the SIMULA system cannot read from the page. How then do we get the SIMULA system to obey our program?
The first step is to write our program, known in full as the "source program", in some medium which the computer housing the SIMULA system can manipulate. Older machines may require you to punch the text on paper tape or cards, but today most machines will handle text files. These are stored in the computer's memory on some magnetic medium such as a disk or tape. You can usually create them by typing them into the system using a text editor. You will need to check the documentation for the computer that you intend to use, especially the SIMULA user's guide or programmer's reference manual.

Once you have created this "machine readable" version of the source program, the part of the SIMULA system known as the "compiler" must be used to read the source and create a sequence of "machine instructions", which tell the computer what the SIMULA program wants to do. This is known as "compiling" the program.

Machine instructions are different on different types of computer. If you want to move a SIMULA program from one computer to another type of computer, you must re-process the source, with the compiler of the SIMULA system on the second machine. This is sometimes referred to as recompiling the program on the second machine.

Once you have compiled the program, you may have to "link" the file of machine instructions which has been generated by the compiler. Not all computers require this step. The file produced by the compiler is known as the object file and the translation of your source program which it holds is known as the "object program".

Linking - sometimes called linkage editing - merges the object program version of your program with the standard programs already available in the SIMULA system - the "runtime system" or "runtime library". These allow reading and writing, standard maths functions and so forth to be used; OutInt is made available in this way. Where linking is not necessary, these standard procedures are found by the command which runs the program.

Details of how to compile and, if necessary, link your program should be available to you in the user's guide or programmer's reference manual for your SIMULA system. Where on-line "help" information is available on your computer, this may provide a useful summary. Where you have access to professional advisers or more experienced SIMULA users, you may be able to save a lot of time by asking them for help, but you should still read the documentation carefully, since not everyone uses all the features of a large programming language like SIMULA.

The final product of compilation and linking is a translation of your original SIMULA source into instructions which your computer can obey. You can now "run" the file containing these and see your program work. Again you should consult the documentation for your computer for details of how to do this.

Note: The ability to compile and run SIMULA programs depends on having access to a computer with a SIMULA system. If your computer currently does not, you may be able to buy one, but they are not available for all computers. The suppliers of those systems known to the author are listed at the back of this book. If your computer is not listed, write to SIMULA A.S., who may be able to help. Their address is given in the list. Readers in the United Kingdom might wish to contact the author.

###Summary

Each chapter will end with a brief summary of what the author thinks you should have learned from it.
In this chapter we have looked informally at what a program and a block are. We used a very simple program, made up of only one block to illustrate this.

We discovered that a block is a series of instructions, preceded by the keyword begin and followed by the keyword end.

Instructions are either declarations, which reserve and name locations, in which information of one particular type may be kept, or statements, which tell the SIMULA system to perform some action.

Lastly we looked at how to compile and run our program on a computer.

{{ book.Chapter2 }}

###Exercises

When the author was learning to write programs, he was told the most important rule, "There is no such thing as an armchair programmer". The simplest exercise can often teach even an experienced programmer something new. Avoid the temptation to miss out examples as they will often lead on to the material covered in the next chapter.
This chapter has introduced a lot of ideas. You may feel completely bewildered. Do not despair. By using the ideas in successive chapters and examples you will soon become familiar with them. To start with try three fundamental, but by no means unimportant, exercises.

1.1 Using this chapter and the documentation for your SIMULA system, compile and run our example. Note carefully the printed output from the compiler and what files are produced by it.

1.2 Nearly all compilers allow you to produce a printed listing of your program when it is being compiled. Most will allow you to specify things about the appearance of this listing, for instance whether the lines will be numbered for you and whether a cross referenced index of identifiers is to be appended. Try producing listings using any options available on your SIMULA system.

1.3 Try exercise 1.1 again changing the source program by:

    1. leaving out each line in turn,
    2. leaving out one of the semi-colons,
    3. adding in the extra declarations shown in this chapter.
    4. swapping lines around, especially moving begin, end and the declaration of Int1.
Note carefully the differences in messages from the compiler and when running the program in each case. See if the listing is different when messages are printed by the compiler.