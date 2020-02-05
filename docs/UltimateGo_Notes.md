# Ultimate Go (2/3/2020)

## Lesson 1 - Design Guidelines


* 1.1. Prepare your mind
    * no large codebase
    * no large abstractions. We want to focus on thin layers of decoupling that are precise. 
    * this (software development industry) correctly asks students to learn to write before they can read
    * goal of go is to reduce the amount of code to make it easie to manage and maintain
    * the hardware *is* the platform (not a VM)
    * every decision comes with a cost. Engineering is about understanding the cost of the decisions that you make (not understanding is the cost is hacking"). Hacking can have it's place (e.g. prototyping)
    * "If most computer people lack understanding and knowledge, then what they will select will also be lacking"
    * "There are two kinds of software projects, those that fail, and those that turn into legacy horrors" - Peter Weinberger (inventor of AWK)
    * Must have a good mental model of the code. Debugger isn't going to help if the mental model used when writing the code isn't sound.


* 1.2 Productivity vs. Performance
    * The question we should be asking is: "Can I write a go program that's *fast enough*"

* 1.3 Correctness vs. Performance
    * We never guess about performance. There is enough tooling to help with performance.
    * Need to focus on correctness vs. performance
    * "Make it correct, make it clear, make it concise, make if fast. In that order" - *Wes Dyer*
    * "Good engineering is less about finding the perfect solution and more about **understanding the tradeoffs and being able to explain them**" *-JBD*
    * The idea of us Software Devs having to write perfect code all of the time has to go out the door. The idea is that we are always "drafting code" (e.g. reviewing it, revising it, refactoring it, etc.) until it's at point that it can be published.
    * The correctness is what matters. Don't write for performance becuase then you would be guessing. 

* 1.4 Code Reviews
    * All code does 3 things: allocates memory, reads memory, and writes to memory (i.e. reading/writing integers all day). Before optimizing for correctness, need to set rules on what that means. Rules should be:
        1. integrity
            2. micro
                3. memory allocation (read/write) has to be accurate, consistent, and efficient. 
            4. macro
                5. Every problem we solve is a data transformation problem (**you must understand the data to solve problems**). DOD (Data Oriented Design) - we're all data scientists. Write less code, which will lead to less errors. For every 20 lines of code, there's a bug. and handle errors. If you were to pair program with the instructor, you would likely spend 30% of the time writing new code, and 70% refactoring that code toward readability, integrity, and mental models. A study regarding critical failures in software (e.g. Redis, Cassandara, Map Reduce, etc.) found that 92% of failures could've been prevented by better error handling.
        3. readability
            4. Structure systems to be more comprehensible. There is a subjective part and a measurable part. The subjective part is that everyone on the team needs to be able to read code. Go could be considered an object oriented language (Rob Pike has described it as a "better OO lang), but the instructor doesn't consider it a OO lang, but rather a Data Oriented Language (becuase Go doesn't have the features of a traditional OO Lang). The avg dev should be able to have a full mental model of your codebase. Don't hide cost (e.g. abstracting on top of abstractions) 
        5. simplicity
            6. "Making this easy to do is a false economy. Focus on making things easy to understand and the rest will follow." *-Peter Bourgon
            7. Simplicity is not something that you do on day one, it's something that needs to be refactored into code. Simplicity is about hiding complexity w/o losing readability.

At the end of the day, the code review process is done in the above 3 stages. Many devs may feel they don't have the time to refactor this way, but the problem is that you don't have time not to. Refactor is not something you set a sprint for, it's something that you do daily.


* 1.5 If Performance Matters
    * 4 Top Reasons Software Running Slow (or faster than it could be)
        1. External Latencies
            * system calls and network latencies usually in **milliseconds**. Microservice architecture (i.e. jumping processes) already incurs latency of response time.
        2. Internal Latencies
            * Usually microseconds of latency. Garbage Collector and Syncronization and Orchestration (with multi-threaded software)
        3. How Data is Accessed on the Machine (usually nanoseconds to microseconds)
        4. Algorithm Efficiencies
            * Can be a bit inefficient if it allows for better readability



## Lesson 2 - Memory and Data Semantics

Intro: Memory

types, struct types, pointers, data semantics (micro-level). Cost is really important, and memory plays a big role in understanding cost.

* 2.1 Variables

    * Type is the amount of memory 
    * There are 3 classes of types
        * built-in types
        * reference-types
        * user define struct types

    * Built-in types
        * numeric
        * string
        * bool

    Example: `var c float64` - tells us both that this is a 64 bit (8 byte) wide, and that those 8 bytes represent an IEEE754 binary decimal (i.e. float)
    
    Example: `var d bool` - just gives us a representation (1 byte of allocation - true or false)
    
    With Integers, multiple precision width is available (e.g. int8, int16, int32 are available...but we just use int for most apps)
    
    Every line of code you write is either allocating, reading, or writing...and is integer-based memory. We  want to be sympathetic to the hardware arch. You would want to use the most efficient integer. The compiler will use the correct integer arch (int8, int16, etc.) based on the hw arch. So if you're on a AMD64 arch, that would be the int to use would be int64 (i.e. 8 bytes).
    
    **Word** (size) - generic allocation that can hold an integer or an addresss
    
    **Zero Value** (allocation) - memory (once allocated) has to be initialized. If we don't specify the initialization state, then the compiler will initialize it to a Zero state (erases any bit pattern that was initialized in there).
    
    * Declaring and Assigning variables to valuesare
        * Opinion: There are too many ways to declare variables in Go. When you declare variables, you need to determine whether you want to give it a Zero value, or something else. You can declare a variable using the var syntax (e.g. `var a int`, `var b string`, etc.) to denot a zero value constuction.

    * Strings - two word values ("word" is a generic allocation of memory based on your machine's arch which can represent an integer or an address). The first word is a pointer (which is 'nil' when set to a zero value via declaration by `var`), the 2nd word is an integer that reprents the number of bytes for a string (though it will be zero in this case), referred to a zero valued string or an "empty string" in go. Just know that a string type in go is made up of multiple data structures.

    * Short variable declarations operator - A short variable declarations operator (`:=`) declares AND constructs the variable at the same time. You can use this in your code if preferred but be sure to be consistent about it throughout the codebase. The instuctor's opinion is that you should use var for zero value declarations and the short variable declaration for any non-zero declarations.

    * Conversion Overcasting (e.g. "casting") - performance-based operation that involves writing and overlay of larger memory space to accomodate more bytes. Can be complicated and produce bugs, better to do Conversion.

    * Conversion - copying memory into new memory space that is larger
    
    
* 2.2 Struct Types

    * Remember that type provides 2 pieces of information, the size and what that space in memory represents.

    * Struct Type (e.g. `type example struct {`) - it's a composite type (based on other types). Struct types help us define the data, which is a big deal (as mentioned above - DOL - data oriented language)
        * For example: If you have a struct made up of a bool, int16, and a float32...you could visualize it something like:

            

| bool | int16 | int32 | 
| -------- | -------- | -------- |
| 1 byte     | 2 bytes     | 4 bytes|

HOWEVER, the above byte count would be wrong. Alignments (from the compiler) put a "padding byte" betweem the bool and the int16. This is for the sake of mechanical sympathies with the hw. So the above would not be 7 bytes, but rather 8 (because of the "padding byte") for an aligned 8 byte boundry "word"

To help remove some (if not all of) this "padding," you want to order your fields from largest to smallest which allows everything to align evenly from beginning to end. However, don't worry about ordering unless it's a problem.

*reminder: the reason we're even talking about memory space and byte allocation is because of **cost**, which we as programmers should care about*


Struct Literal (aka Compact Construction) - you construct a value of a struct type *without* a value of 0. Using the short variable declaration operator, and inside the curly brackets setting values to a non-zero operator (empty). You could initialize the values/fields to 0 by adding an **empty literal** (`{}`) at the end of the struct line (e.g. `e2 := example{}`), though the instructor would ask you to switch back to `var` to initialize the struct to 0 since he suggests avoiding the emtpy literal as much as possible (however there are exceptions to this rule, like everything else in Go). But if we're ever going to be constructing to a variable, then we want to use `var` to initialize to 0, otherwise we'll use the literal construction short variable declaration operator.

*Note: the instructor isn't consistent with his vocabulary (e.g. struct literal == empty literal == literal construction)*

Similar to other languages, dot operators are used between a variable and a field (e.g. `fmt.Println("Flag", e2.flag)`)

The above values are referred to as a named type. The below is a literal type (values based on types that don't have a name).

*Example of a named type:*
```
var e1 struct {  
        flag    bool
        counter int16
        pi      float32
}
```
*Question: 3/4 through the video, the instructor performs an explicit assignment via conversion of two identical structs. Not sure exactly what happened*. 

Functions are first class citizens. They are literally typed or non-named typed variables.

Our struct types are allowing us to (user) define the data. We can define these new types on a name or a literal. By defining these structs, we're helping the compiler with the type in terms of the memory amount and the representation of that memory (e.g. the composite and dynamic address location or "word" - remember the alignments and paddings) 

*We are covering struct types in detail because we are going to be using them a lot throughout this course.*





