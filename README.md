# String-Solver
Scala version of Flash-Fill for Excel 2013 by Gulwani et Al. See http://rise4fun.com/QuickCode/dates

Build using sbt 0.13 and scala 2.10.3.

## Key features:

- Supports incremental numbering.
- "..." continues an expression if there is a loop.

## File renaming

StringSolver includes a nice renaming tool.

[![ScreenShot](https://raw.github.com/GabLeRoux/WebMole/master/ressources/WebMole_Youtube_Video.png)](http://youtu.be/F9mUIPK7h-I)

- Build the project using `sbt one-jar`.
- Use the following alias to rename file using the tool:
```
alias mv='java -jar "[/path/to/target/scala/]stringsolver_2.10-1.0-one-jar.jar" move'
```
- Do your renaming normally, e.g. `mv file1 file2`

## API:

```Scala
import ch.epfl.lara.synthesis.stringsolver._

object Test {
   val c = StringSolver()
   c.add("file499.pdf -> 01file.pdf")
   c.add("report_761.pdf -> 02report.pdf")
   c.add("credits##.pdf -> 03credits.pdf")
   
   /* Prints:
	* a 2-digit number incrementing starting at 1 continuing the numbers
	* of previous first output until [the end of the first number]
	* + the first input until [the end of the first lowercase word]
	* + the first input starting at [the first '.']
	*/ 
   println(Printer(c.solve().get))
   
   /* Prints:
	* 04input.pdf
	**/
   println(c.solve("input%^$.pdf"))
}

object Test2 {
   val c = StringSolver()
   c.add(List("Alg1.txt"), "convert Alg1.txt Alg001.pdf")
   
   /* Prints:
	* the constant string 'convert ' + the first input
	  + the constant string ' ' + the first input until [the end of the first word]
	  + a 3-digit number incrementing starting at 1 continuing the first number in previous output
	  + the constant string '.pdf'
	*/ 
   println(Printer(c.solve().get))
   
   /* Prints:
	* "convert Math2.txt Math002.pdf"
	*/ 
   println(c.solve("Math2.txt"))

   
   val c2 = StringSolver()
   c2.add(List("Alg001.pdf", "Alg002.pdf", "Alg003.pdf"), "convert Alg001.pdf Alg002.pdf... AlgBook.pdf")
   
   /* Prints:
	* the constant string 'convert ' + concatenates all inputs separated by ' '
	* + the constant string ' ' + the first input until [the end of the first word]
	* + the constant string 'Book' + the first input starting at [the last non-number]
	*/
   println(Printer(c2.solve().get))
   
   /* Prints:
	* convert Math1.pdf Math2.pdf Math3.pdf Math4.pdf MathBook.pdf
	*/
   println(c2.solve("Math1.pdf | Math2.pdf | Math3.pdf | Math4.pdf"))
   // prints "
}
```

## Providing input/output examples

The other ways to add input/output examples in StringSolver are the following, given that c is a StringSolver instance.

```Scala
// Exactly one input and one output
c.add("input1 -> output1")
// c.add returns a set of solution programs. You can .takeBest on it

// Three inputs and two outputs
c.add("input1 | input2 | input3 | output1 | output2", 3)

// Three inputs and two outputs
c.add(List("input1", "input2", "input3"), List("output1", "output2"))

// Three inputs and one output
c.add(List("input1", "input2", "input3"), "output1)
```

## Solving new input

To solve and print an existing StringSolver instance, do the following:
```Scala
val c = StringSolver()
c.add(List("a","b"),List("ab","ba"))

// If one output was provided (equivalent to c.solve(0))
c.solve() match {    // The first input + the second input
  case Some(prog) => println(Printer(prog))
  case None =>
}

// Retrieve the second output program (0-index based)
c.solve(1) match {   // The second input + the first input
  case Some(prog) => println(Printer(prog)) 
  case None =>
}

// Returns "cd | dc"
c.solve("c | d")

// Returns List("cd", "dc")
c.solve(List("c", "d"))
```

## Options

Most of the StringSolver usage is fully automated, and does not require to change the following options.
For some cases, they can be useful to trigger on/off.

```Scala
val c = StringSolver()

/** Use dots ... to trigger manual loop research */
c.setUseDots(b: Boolean)

/** Use numbering from previous input option */
c.setUseNumbers(b: Boolean)

/** Loop level. 0 will not look for loops */
c.setLoopLevel(i: Int)

/**
 * Timeout in seconds to add a new input/output example.
 * This is approximate. Default is 30s
 */
c.setTimeout(seconds: Int)

/** If looking for loops, what could be the maximum separator length */
c.setMaxSeparatorLength(length: Int)
```
