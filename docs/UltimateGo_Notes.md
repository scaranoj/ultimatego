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