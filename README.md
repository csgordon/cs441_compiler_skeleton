# CS 441 Compiler Skeleton Code

You are allowed to use any programming language you like for this class, as long as you wrap your build-and-run behind a script named ```comp```.

However, some of the initial structure is intimidating without some working example code for portions of the system.

This repository has a working ```Tokenizer``` and a ```Parser``` capable of parsing our language's expressions. You're welcome to build on this
directly for your project, if you want to use Java. Otherwise, take this as working example code that you can port to your implementation
language of choice.

## Building

Build the code with ```./gradlew compileJava```. This isn't necessary by itself if you run the code directly, in which case Gradle will compile it for you.

## Kicking the Tires on the Tokenizer

To experiment with the tokenizer, you can execute commands like
```
./gradlew run --args='tokenize ^&&x.f.g.m(4, @FOO)'
```
which will run the tokenizer on the expression you pass and display the sequence of tokens produced, such as:
```
$ ./gradlew run --args='tokenize ^&&x.f.g.m(4, @FOO)'

> Task :app:run
Caret[]
Ampersand[]
Ampersand[]
Identifier[name=x]
Dot[]
Identifier[name=f]
Dot[]
Identifier[name=g]
Dot[]
Identifier[name=m]
LeftParen[]
Number[value=4]
Comma[]
AtSign[]
Identifier[name=FOO]
RightParen[]

BUILD SUCCESSFUL in 726ms
2 actionable tasks: 1 executed, 1 up-to-date
```

## Kicking the Tires on the Parser
The parser *only* supports expressions right now, so there is a similar command for parsing an expression and printing the resulting AST:
```
$ ./gradlew run --args='parseExpr ^&&x.f.g.m(4, @FOO)'

> Task :app:run
Parsed arg: Constant[value=4]
Parsed arg: ClassRef[classname=FOO]
MethodCall[base=FieldRead[base=FieldRead[base=Variable[name=x], fieldname=f], fieldname=g], methodname=m, args=[Constant[value=4], ClassRef[classname=FOO]]]

BUILD SUCCESSFUL in 773ms
2 actionable tasks: 1 executed, 1 up-to-date
```
You'll need to implement parsing for statements, method and class declarations, and other things as well.

## Implementation Notes

This sample code makes aggressive use of some features in the last few Java releases which you may not have encountered before:

- [Record Classes](https://docs.oracle.com/en/java/javase/25/language/records.html) offer a concise way to define classes that mostly exist just to hold some data,
  which is good for ASTs and basic operations in your IR
- [Sealed Interfaces](https://docs.oracle.com/en/java/javase/25/language/sealed-classes-and-interfaces.html) are a way to explicitly state that
  an interface (or parent class) only has a fixed number of subtypes. This isn't strictly necessary, but plays nicely with the other feature...
- [Type Switches and Exhaustive Pattern Matching](https://docs.oracle.com/en/java/javase/25/language/switch-expressions-and-statements.html#GUID-8D92D54A-BAEB-452A-B84E-03188B18029B)
  offer a way for Java switch statements to (1) match based on dynamic type of an object, which removes a lot of annoying casting, and (2) check
  that all possible cases are covered, if the static type of the expression being switched on is a sealed type (and therefore there are a fixed number of possible subtypes, so a fixed number of cases)
