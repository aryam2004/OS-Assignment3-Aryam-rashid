# Assignment 3 - Complete Documentation

**Student Name**: [Aryam rashid]  
**Student ID**: [445052037]  
**Date Submitted**: [May 2, 2026]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: 445052037_Assignment3_Synchronization.mp4

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [Date, Time]

I forked the repository, cloned it using Visual Studio Code, configured Git with my university email, and updated the student ID inside SchedulerSimulationSync.java.

Challenges encountered:
I initially had issues configuring Git global user settings.

How I solved it:
I corrected the git config commands and verified the settings using git config --global --list.

Testing approach:
I compiled and ran the program to verify successful execution.

Time spent:
1 hour

### Entry 2 - [Date, Time]
**What I implemented**: 

I implemented ReentrantLock for shared counter protection.

Challenges encountered:
I needed to determine which variables required synchronization.

How I solved it:
I analyzed all shared mutable variables and identified contextSwitchCount, completedProcessCount, and totalWaitingTime.

Testing approach:
I executed the scheduler several times and verified counters updated correctly.

Time spent:
2 hours
---

### Entry 3 - [Date, Time]
**What I implemented**: 

I implemented logLock for executionLog synchronization.

Challenges encountered:
Understanding how concurrent writes affect ArrayList consistency.

How I solved it:
I wrapped executionLog modifications inside critical sections using lock() and unlock().

Testing approach:
I monitored execution logs for corruption or missing entries.

Time spent:
1.5 hours

### Entry 4 - [Date, Time]

I implemented binary semaphore for CPU access control.

Challenges encountered:
Ensuring permits were always released.

How I solved it:
I used try-finally blocks around acquire() and release().

Testing approach:
I verified sequential CPU execution.

Time spent:
2 hours
---

### Entry 5 - [Date, Time]
**What I implemented**: 

Final testing and verification.

Challenges encountered:
Validating synchronization correctness.

How I solved it:
I ran multiple tests and compared outputs.

Testing approach:
Executed program 5 times.

Time spent:
1 hour
---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

The first race condition exists in shared counter variables such as contextSwitchCount. Multiple threads may increment the counter simultaneously, causing lost updates.

The second race condition exists in executionLog. Since ArrayList is not thread-safe, simultaneous writes can corrupt internal state or throw ConcurrentModificationException.

Without synchronization, incorrect context switch counts and incomplete logs could occur.

Using ReentrantLock ensures atomic access to these shared resources.
---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:
ReentrantLock provides mutual exclusion for critical sections by allowing one thread to lock a code section.

Semaphore controls access using permits.

In my implementation:

ReentrantLock was used for counters and execution log.
Semaphore was used for CPU access control.

Locks protect data consistency, while semaphores control resource availability.
---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:
Deadlock occurs when threads wait indefinitely for resources held by each other.

Two prevention techniques:

Using try-finally to guarantee release
Maintaining consistent resource acquisition

In my implementation, every lock and semaphore acquisition is followed by release inside finally blocks.

This ensures resources are always released.

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

I used one shared lock for all three counters (coarse-grained locking).

I chose this approach because it simplifies synchronization logic and reduces implementation complexity.

The trade-off is reduced concurrency, since only one thread can update any counter at a time.

Fine-grained locking would improve concurrency by allowing independent updates.

However, because counter updates are very short operations, coarse-grained locking introduces negligible overhead.

For maximum scalability, fine-grained locking would provide better concurrency.
---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 

contextSwitchCount, completedProcessCount, totalWaitingTime

Why they need protection:
They are modified by multiple threads.

Synchronization mechanism used:
ReentrantLock
**Code snippet**:
counterLock.lock();
try {
    contextSwitchCount++;
} finally {
    counterLock.unlock();
}
**Justification**: Ensures atomic updates and prevents race conditions.

---

### Critical Section #2: Execution Log

**What resource**: 

executionLog ArrayList

Why it needs protection:
ArrayList is not thread-safe.

Synchronization mechanism used:
ReentrantLock

**Code snippet**:
logLock.lock();
try {
    executionLog.add(message);
} finally {
    logLock.unlock();
}
**Justification**: 
Prevents concurrent modification.
---

### Critical Section #3: CPU Semaphore
Purpose of semaphore:
Restrict CPU access to one process.

Number of permits and why:
1 permit because CPU executes one process at a time.

Where implemented:
run() and runToCompletion()

Code snippet:
SharedResources.cpuSemaphore.acquire();

try {
   // execution
} finally {
   SharedResources.cpuSemaphore.release();
}

**Effect on program behavior**: 
Ensures serialized CPU execution.
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check

What I tested:
Program consistency across runs

Testing procedure:
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync

Results:
Consistent successful execution.

Why synchronization is necessary:
Without it, counters and logs may become inconsistent.

Conclusion:
Synchronization worked correctly.
---

### Test 2: Exception Testing
**What I tested**:
ConcurrentModificationException prevention

Testing procedure:
Repeated rapid execution.

Results:
No exceptions occurred.

What this proves:
executionLog synchronization is effective.
---

### Test 3: Correctness Verification
What I tested:
Final statistics correctness

Expected values:
11 completed processes

Actual values:

Completed Processes: 11
Context Switches: 18
Total Waiting Time: 349289ms

Analysis:
Results matched scheduler expectations.
---

### Test 4: Different Scenarios
Scenario tested:
Different random process burst times

Purpose:
Validate scheduler adaptability

Results:
Correct execution in all scenarios

What I learned:
Synchronization remains reliable under varying workloads.

## Part 5: Reflection and Learning

#What I learned about synchronization:

This assignment taught me how concurrent threads interact with shared resources.

I learned how race conditions occur and how synchronization solves them.

ReentrantLock provides thread-safe access to shared variables.

Semaphore models controlled resource access.

Try-finally is essential for safe release.

Testing concurrent systems requires repeated execution.

Synchronization improves reliability.

This assignment strengthened my practical Java concurrency skills.
---

### Real-world applications:
Example 1:
Banking transaction systems

Example 2:
Database connection pools
---

### How I would explain synchronization to others:

Synchronization is like organizing students entering a classroom.

Without rules, everyone enters at once causing chaos.

Locks act like a door key.

Semaphore acts like limiting the number of students allowed inside.

This ensures order and prevents conflicts.
---

## Part 6: GitHub Repository Information

(https://github.com/aryam2004?tab=repositories) 

**Number of commits**: 
4
**Commit messages**: 
Set student ID and initial setup
Implemented counter synchronization
Added execution log locking
Implemented CPU semaphore and final testing

---

## Summary

Total time spent on assignment:
7.5 hours

Key takeaways:

Synchronization prevents race conditions
Locks ensure atomic operations
Semaphores control shared resource access

Most challenging aspect:
Understanding lock granularity design

What I'm most proud of:
Successfully implementing all synchronization mechanisms with correct testing verification
---

**End of Documentation**
