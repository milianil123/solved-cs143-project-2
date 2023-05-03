Download Link: https://assignmentchef.com/product/solved-cs143-project-2
<br>
Programming assignments II–V will direct you to design and build a compiler for Cool. Each assignment will cover one component of the compiler: lexical analysis, parsing, semantic analysis, and code generation. Each assignment will ultimately result in a working compiler phase which can interface with other phases. You will have an option of doing your projects in C++ or Java.

For this assignment, you are to write a lexical analyzer, also called a <em>scanner</em>, using a <em>lexical analyzer generator</em>. (The C++ tool is called flex; the Java tool is called jlex.) You will describe the set of tokens for Cool in an appropriate input format, and the analyzer generator will generate the actual code (C++ or Java) for recognizing tokens in Cool programs.

On-line documentation for all the tools needed for the project can be found in the Other Materials section of the course website. This includes manuals for flex and jlex (used in this assignment), the documentation for bison and java cup (used in the next assignment), as well as the manual for the spim simulator.

<h1>1           Introduction to Flex/JLex</h1>

Flex allows you to implement a lexical analyzer by writing rules that match on user-defined regular expressions and performing a specified action for each matched pattern. Flex compiles your rule file (e.g., “lexer.l”) to C (or, if you are using JLex, Java) source code implementing a finite automaton recognizing the regular expressions that you specify in your rule file. Fortunately, it is not necessary to understand or even look at the automatically generated (and often very messy) file implementing your rules. Rule files in flex are structured as follows:

%{

Declarations

%}

Definitions

%%

Rules

%%

User subroutines

The Declarations and User subroutines sections are optional and allow you to write declarations and helper functions in C (or for JLex, Java). The Definitions section is also optional, but often very useful as definitions allow you to give names to regular expressions. For example, the definition

DIGIT                 [0-9]

allows you to define a digit. Here, DIGIT is the name given to the regular expression matching any single character between 0 and 9. The following table gives an overview of the common regular expressions that can be specified in Flex:

<table width="375">

 <tbody>

  <tr>

   <td width="62">x</td>

   <td width="313">the character “x”</td>

  </tr>

  <tr>

   <td width="62">“x”</td>

   <td width="313">an “x”, even if x is an operator.</td>

  </tr>

  <tr>

   <td width="62">x</td>

   <td width="313">an “x”, even if x is an operator.</td>

  </tr>

  <tr>

   <td width="62">[xy]</td>

   <td width="313">the character x or y.</td>

  </tr>

  <tr>

   <td width="62">[x-z]</td>

   <td width="313">the characters x, y or z.</td>

  </tr>

  <tr>

   <td width="62">[^x]</td>

   <td width="313">any character but x.</td>

  </tr>

  <tr>

   <td width="62">.</td>

   <td width="313">any character but newline.</td>

  </tr>

  <tr>

   <td width="62">^x</td>

   <td width="313">an x at the beginning of a line.</td>

  </tr>

  <tr>

   <td width="62">&lt;y&gt;x</td>

   <td width="313">an x when Lex is in start condition y.</td>

  </tr>

  <tr>

   <td width="62">x$</td>

   <td width="313">an x at the end of a line.</td>

  </tr>

  <tr>

   <td width="62">x?</td>

   <td width="313">an optional x.</td>

  </tr>

  <tr>

   <td width="62">x*</td>

   <td width="313">0,1,2, … instances of x.</td>

  </tr>

  <tr>

   <td width="62">x+</td>

   <td width="313">1,2,3, … instances of x.</td>

  </tr>

  <tr>

   <td width="62">x|y</td>

   <td width="313">an x or a y.</td>

  </tr>

  <tr>

   <td width="62">(x)</td>

   <td width="313">an x.</td>

  </tr>

  <tr>

   <td width="62">x/y</td>

   <td width="313">an x but only if followed by y.</td>

  </tr>

  <tr>

   <td width="62">{xx}</td>

   <td width="313">the translation of xx from the definitions section.</td>

  </tr>

  <tr>

   <td width="62">x{m,n}</td>

   <td width="313">m through n occurrences of x</td>

  </tr>

 </tbody>

</table>

The most important part of your lexical analyzer is the rules section. A rule in Flex specifies an action to perform if the input matches the regular expression or definition at the beginning of the rule. The action to perform is specified by writing regular C (or Java) source code. For example, assuming that a digit represents a token in our language (note that this is not the case in Cool), the rule:

{DIGIT} { cool_yylval.symbol = inttable.add_string(yytext); return DIGIT_TOKEN;

}

records the value of the digit in the global variable cool_yylval and returns the appropriate token code. (See Sections <strong>?? </strong>and <strong>?? </strong>for a more detailed discussion of the global variable cool_yylval and see Section <strong>?? </strong>for a discussion of the inttable used in the above code fragment.)

An important point to remember is that if the current input (i.e., the result of the function call to yylex()) matches multiple rules, Flex picks the rule that matches the largest number of characters. For instance, if you define the following two rules

[0-9]+    { // action 1} [0-9a-z]+ { // action 2}

and if the character sequence 2a appears next in the file being scanned, then action 2 will be performed since the second rule matches more characters than the first rule. If multiple rules match the same number of characters, then the rule appearing first in the file is chosen.

When writing rules in Flex, it may be necessary to perform different actions depending on previously encountered tokens. For example, when processing a closing comment token, you might be interested in knowing whether an opening comment was previously encountered. One obvious way to track state is to declare global variables in your declaration section, which are set to true when certain tokens of interest are encountered. Flex also provides syntactic sugar for achieving similar functionality by using state declarations such as:

%Start COMMENT

which can be set to true by writing BEGIN(COMMENT). To perform an action only if an opening comment was previously encountered, you can predicate your rule on COMMENT using the syntax:

&lt;COMMENT&gt; {

// the rest of your rule …

}

There is also a special default state called INITIAL which is active unless you explicitly indicate the beginning of a new state. You might find this syntax useful for various aspects of this assignment, such as error reporting. We strongly encourage you to read the documentation on Lex written by Lesk &amp; Schmidt linked from the Other Materials section on the class webpage before writing your own lexical analyzer.

<h1>2           Files and Directories</h1>

To get started with the programming assignments, download the starter code from the OpenClassroom website and extract it to a convenient directory on your local machine. Make sure you download the tarball that matches your particular machine architecture. You may also download the pieces of this assignment individually from the Resources page, but we strongly recommend that you download and use the complete tarball as is.

Once you have a working copy of the programming assignment source tree, change into the directory for the current assignment. For the C++ version of the assignment, navigate to

[cool root]/assignments/PA2/

For Java, navigate to

[cool root]/assignments/PA2J/

(notice the “J” in the path name). Typing make in this directory will set up the workspace and copy a number of files to your directory. Some of the files in the directory will be read-only (using symbolic links). You should not edit these files. In fact, if you make and modify private copies of these files, you may find it impossible to complete the assignment. See the instructions in the README file. The files that you will need to modify are:

<ul>

 <li>flex (in the C++ version) / cool.lex (in the Java version)</li>

</ul>

This file contains a skeleton for a lexical description for Cool. There are comments indicating where you need to fill in code, but this is not necessarily a complete guide. Part of the assignment is for you to make sure that you have a correct and working lexer. Except for the sections indicated, you are welcome to make modifications to our skeleton. You can actually build a scanner with the skeleton description, but it does not do much. You should read the flex/jlex manual to figure out what this description does do. Any auxiliary routines that you wish to write should be added directly to this file in the appropriate section (see comments in the file).

<ul>

 <li>cl</li>

</ul>

This file contains some sample input to be scanned. It does not exercise all of the lexical specification, but it is nevertheless an interesting test. It is not a good test to start with, nor does it provide adequate testing of your scanner. Part of your assignment is to come up with good testing inputs and a testing strategy. (Don’t take this lightly—good test input is difficult to create, and forgetting to test something is the most likely cause of lost points during grading.)

You should modify this file with tests that you think adequately exercise your scanner. Our test.cl is similar to a real Cool program, but your tests need not be. You may keep as much or as little of our test as you like.

<ul>

 <li>README</li>

</ul>

This file contains detailed instructions for the assignment as well as a number of useful tips. You should also edit this file to include the write-up for your project. You should explain design decisions, why your code is correct, and why your test cases are adequate. It is part of the assignment to clearly and concisely explain things in text as well as to comment your code.

Although these files are incomplete as given, the lexer does compile and run (make lexer).

<h1>3           Scanner Results</h1>

In this assignment, you are expected to write Flex rules that match on the appropriate regular expressions defining valid tokens in Cool as described in Section 10 and Figure 1 of the Cool manual and perform the appropriate actions, such as returning a token of the correct type, recording the value of a lexeme where appropriate, or reporting an error when an error is encountered. Before you start on this assignment, make sure to read Section 10 and Figure 1 of the Cool manual; then study the different tokens defined in cool-parse.h. Your implementation needs to define Flex/Jlex rules that match the regular expressions defining each token defined in cool-parse.h and perform the appropriate action for each matched token. For example, if you match on a token BOOL CONST, your lexer has to record whether its value is true or false; similarly if you match on a TYPEID token, you need to record the name of the type. Note that not every token requires storing additional information; for example, only returning the token type is sufficient for some tokens like keywords.

Your scanner should be robust—it should work for any conceivable input. For example, you must handle errors such as an EOF occurring in the middle of a string or comment, as well as string constants that are too long. These are just some of the errors that can occur; see the manual for the rest.

You must make some provision for graceful termination if a fatal error occurs. Core dumps or uncaught exceptions are unacceptable.

<h2>3.1         Error Handling</h2>

<em>All </em>errors should be passed along to the parser. You lexer should not print anything. Errors are communicated to the parser by returning a special error token called <strong>ERROR</strong>. (Note, you should ignore the token called <strong>error </strong>[in lowercase] for this assignment; it is used by the parser in PA3.) There are several requirements for reporting and recovering from lexical errors:

<ul>

 <li>When an invalid character (one that can’t begin any token) is encountered, a string containing just that character should be returned as the error string. Resume lexing at the following character.</li>

 <li>If a string contains an unescaped newline, report that error as ‘‘Unterminated string constant’’ and resume lexing at the beginning of the next line—we assume the programmer simply forgot the close-quote.</li>

 <li>When a string is too long, report the error as ‘‘String constant too long’’ in the error string in the <strong>ERROR </strong> If the string contains invalid characters (i.e., the null character), report this as ‘‘String contains null character’’. In either case, lexing should resume after the end of the string. The end of the string is defined as either

  <ol>

   <li>the beginning of the next line if an unescaped newline occurs after these errors are encountered;or</li>

   <li>after the closing <strong>” </strong></li>

  </ol></li>

 <li>If a comment remains open when EOF is encountered, report this error with the message ‘‘EOF in comment’’. Do <em>not </em>tokenize the comment’s contents simply because the terminator is missing. Similarly for strings, if an EOF is encountered before the close-quote, report this error as ‘‘EOF in string constant’’.</li>

 <li>If you see “*)” outside a comment, report this error as ‘‘Unmatched *)’’, rather than tokenizing it as * and ).</li>

</ul>

<h2>3.2         String Table</h2>

Programs tend to have many occurrences of the same lexeme. For example, an identifier is generally referred to more than once in a program (or else it isn’t very useful!). To save space and time, a common compiler practice is to store lexemes in a <em>string table</em>. We provide a string table implementation for both C++ and Java. See the following sections for the details.

There is an issue in deciding how to handle the special identifiers for the basic classes (<strong>Object</strong>, <strong>Int</strong>, <strong>Bool</strong>, <strong>String</strong>), SELF TYPE, and self. However, this issue doesn’t actually come up until later phases of the compiler—the scanner should treat the special identifiers exactly like any other identifier.

Do <em>not </em>test whether integer literals fit within the representation specified in the Cool manual—simply create a Symbol with the entire literal’s text as its contents, regardless of its length.

<h2>3.3         Strings</h2>

Your scanner should convert escape characters in string constants to their correct values. For example, if the programmer types these eight characters:

” a b  n c d ”

your scanner would return the token <strong>STR CONST </strong>whose semantic value is these 5 characters:

a b 
 c d

where 
       represents the literal ASCII character for newline.

Following specification on page 15 of the Cool manual, you must return an error for a string containing the literal null character. However, the sequence of two characters

 0

is allowed but should be converted to the one character

0 .

<h2>3.4         Other Notes</h2>

Your scanner should maintain the variable <strong>curr lineno </strong>that indicates which line in the source text is currently being scanned. This feature will aid the parser in printing useful error messages.

You should ignore the token <strong>LET STMT</strong>. It is used only by the parser (PA3). Finally, note that if the lexical specification is incomplete (some input has no regular expression that matches), then the scanners generated by both flex and jlex do undesirable things. <em>Make sure your specification is complete</em>.

<h1>4           Notes for the C++ Version of the Assignment</h1>

If you are working on the Java version, skip to the following section.

<ul>

 <li>Each call on the scanner returns the next token and lexeme from the input. The value returned by the function <strong>cool yylex </strong>is an integer code representing the syntactic category (e.g., integer literal, semicolon, if keyword, etc.). The codes for all tokens are defined in the file cool-parse.h. The second component, the semantic value or lexeme, is placed in the global union <strong>cool yylval</strong>, which is of type YYSTYPE. The type YYSTYPE is also defined in cool-parse.h. The tokens for single character symbols (e.g., “;” and “,”) are represented just by the integer (ASCII) value of the character itself. All of the single character tokens are listed in the grammar for Cool in the Cool manual.</li>

 <li>For class identifiers, object identifiers, integers, and strings, the semantic value should be a <strong>Symbol </strong>stored in the field <strong>cool yylval.symbol</strong>. For boolean constants, the semantic value is stored in the field <strong>cool yylval.boolean</strong>. Except for errors (see below), the lexemes for the other tokens do not carry any interesting information.</li>

 <li>We provide you with a string table implementation, which is discussed in detail in <em>A Tour of the Cool Support Code </em>and in documentation in the code. For the moment, you only need to know that the type of string table entries is <strong>Symbol</strong>.</li>

 <li>When a lexical error is encountered, the routine <strong>cool yylex </strong>should return the token <strong>ERROR</strong>. The semantic value is the string representing the error message, which is stored in the field <strong>cool yylval.error msg </strong>(note that this field is an ordinary string, not a symbol). See the previous section for information on what to put in error messages.</li>

</ul>

<h1>5           Notes for the Java Version of the Assignment</h1>

If you are working on the C++ version, skip this section.

<ul>

 <li>Each call on the scanner returns the next token and lexeme from the input. The value returned by the method <strong>next </strong><strong>token </strong>is an object of class <strong>java cup.runtime.Symbol</strong>. This object has a field representing the syntactic category of a token (e.g., integer literal, semicolon, the if keyword, etc.). The syntactic codes for all tokens are defined in the file TokenConstants.java. The component, the semantic value or lexeme (if any), is also placed in a <strong>java cup.runtime.Symbol </strong>object. The documentation for the class <strong>java cup.runtime.Symbol </strong>as well as other supporting code is available on the course web page. Examples of its use are also given in the skeleton.</li>

 <li>For class identifiers, object identifiers, integers, and strings, the semantic value should be of type</li>

</ul>

<strong>AbstractSymbol</strong>. For boolean constants, the semantic value is of type <strong>java.lang.Boolean</strong>. Except

for errors (see below), the lexemes for the other tokens do not carry any interesting information. Since the <strong>value </strong>field of class <strong>java cup.runtime.Symbol </strong>has generic type <strong>java.lang.Object</strong>, you will need to cast it to a proper type before calling any methods on it.

<ul>

 <li>We provide you with a string table implementation, which is defined in java. For the moment, you only need to know that the type of string table entries is <strong>AbstractSymbol</strong>.</li>

 <li>When a lexical error is encountered, the routine <strong>next token </strong>should return a <strong>java cup.runtime.Symbol </strong>object whose syntactic category is <strong>TokenConstants.ERROR </strong>and whose semantic value is the error message string. See Section <strong>?? </strong>for information on how to construct error messages.</li>

</ul>

<h1>6           Testing the Scanner</h1>

There are at least two ways that you can test your scanner. The first way is to generate sample inputs and run them using lexer, which prints out the line number and the lexeme of every token recognized by your scanner. The other way, when you think your scanner is working, is to try running mycoolc to invoke your lexer together with all other compiler phases (which we provide). This will be a complete Cool compiler that you can try on any test programs