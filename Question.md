# Lab 3: Simulation 3

- Deadline: 19 February 2021, Friday, 23:59 SST
- Marks: 3%

## Prerequisite:

- Completed Lab 2
- Caught up to Unit 22 of Lecture Notes
- Familiar with CS2030S Java style guide

## Goal

This is a continuation of Lab 2.  Lab 3 changes some of the requirements of Lab 2 and adds some new things to the world that we are simulating.  The goal is to demonstrate that, when OO-principles are applied properly, we can adapt our code to changes in the requirement with less effort.

Lab 3 also involves writing your own generic class.

## Queueing at The Counters

Despite adding an entrance queue, the shop is still loosing customers.  With CNY coming, the shop decided to rearrange the layout and make some space for queues at the counters.  With that, customers can now wait at individual counters.

In this lab, we will modify the simulation to add a counter queue to each counter.  If the counter are busy when a customer arrives, the customer will join the queue and wait.  When a counter becomes available, the customer at the front of the queue will proceed to the counter for service.  Each counter queue has a maximum queue length of l.  If every counter queue has reached its maximum capacity of l, then an arriving customer has to wait at the entrance queue.

Just like Lab 2, the entrance queue has a maximum queue length of m.  If there are already m customers waiting in the entrance queue, an arriving customer will be turned away.

With the addition of counters, there is a change to the customer behavior in choosing which counter to join:

- If there are more then one counters available, a customer will go to the counter with the smallest id (just like Lab 2)
- If none of the counter is available, then the customer will join the counter with the shortest queue.  If there are two counters with the same queue length, we break ties with their id.

Note that, when a counter is done serving a customer, one customer from the entrance queue may join the counter queue of that counter.

## Building on Lab 1

You are required to build on top of your Lab 2 submission for this lab.  

Assuming you have `lab2-<username>` and `lab3-<username>` under the same directory, and `lab3-<username>` is your current working directory, you can run
```
cp -i ../lab2-<username>/*.java .
rm -i Lab2.java
```

to copy all your Java code over.

If you are still unfamiliar with Unix commands to navigate the file system and processing files, please review [our Unix guide](https://nus-cs2030s.github.io/2021-s2/unix-essentials.html).

You are encouraged to consider your tutor's feedback and fix any issues with your design for your Lab 2 submission before you embark on your Lab 3.

## Skeleton for Lab 3

We only provide three classes for Lab 3, the main `Lab3.java` (which is simply `Lab2.java` renamed), `QueueTest.jsh` to test your `Queue<T>` class, and `ArrayTest.jsh` to test your `Array<T>` classes.

All three files should not be modified for this lab.

## Your Tasks

We suggest you solve this lab in the following order.

### 1. Make `Queue` a generic class

The class `Queue` given to you in Lab 2 stores its elements as `Object` references, and therefore is not type-safe.  Now that you have learned abuot generics, you should update `Queue` to make it a generic class `Queue<T>`.

The file `QueueTest.jsh` helps to test your `Queue<T>` class (see "Running and Testing" section below)

### 2. Create a generic `Array<T>` class

Let's call the class the encapsulates the counter as `ServiceCounter` (you may name it differently).  We have been using an array to store the `ServiceCounter` objects.  In Lab 3, you should replace that with a generic wrapper around arrays.  In other words, we want to replace `ServiceCounter[]` with `Array<ServiceCounter>`.  You may build upon the `Array<T>` class from [Unit 22](https://nus-cs2030s.github.io/2021-s2/22-unchecked.html).

Make `Array<T>` takes in only types that implement the `Comparable<T>` interfaces as type arguments.  That is, we want to put only types that can compare with itself into our `Array<T>` class.

The `Array<T>` class should additionally support the `min` method, with the following signature:
```
T min()
```

which returns the minimum element (based on the order defined by the `compareTo` method).

Note that in implementing `Array<T>`, you will find another situation where using raw type is necessary.

The file `ArrayTest.jsh` contains test cases to test your `Array<T>` class (see "Running and Testing" section below).

### 3. Make Your `ServiceCounter` Comparable to Itself

Your class that encapsulates the service counter should now implements the `Comparable<T>` interface so that it can compare with itself and we can use `ServiceCounter` as a type argument for `Array<T>`.  

You should implement `compareTo` in such a way that `counters.min()` returns the counter that a customer should join.

### 4. Update Your Simulation

By incorperating `Queue<T>`, `Array<T>`, `ServiceCounter`, modify your simulation so that it implements the shop with counter queues as described above.

### 5. Other Changes Needed

We also need to make the following changes to the input and output of the program.

1. There is an additional input parameter, an integer l, indicating the maximum allowed length of the counter queue.  This input parameter should be read immediately after reading the number of service counters and before the maximum allowed length of the entrance queue.

2. Now that we have two types of queues, if a customer joins the entrance queue, the customer along with the queue _before_ joining should be printed as such:
```
1.400: C3 joined shop queue [ C1 C2 ]
```

3. The counter queue will be printed whenever we print a counter.
```
1.200: C2 joined counter queue (at S0 [ C1 ])
2.000: C0 service done (by S0 [ C1 C2 ])
```

## Following CS2030S Style Guide

Like Lab 2, you should also make sure that your code follows the [given Java style guide](https://nus-cs2030s.github.io/2021-s2/style.html)

## Assumptions

We assume that no two events involving two different customers ever occur at the same time (except when a customer departs and another customer begins its service, or when a customer is done and another customer joins the counter queue from the entrance queue).  As per all labs, we assume that the input is correctly formatted.

## Compiling, Testing, and Debugging

### Compilation

To compile your code,
```
javac *.java
```

### Running and Testing

You may test your simulation code similarly to how you test your Lab 2.  

In addition, to test your `Queue<T>` and `Array<T>` class, you can run:
```
jshell -q < ArrayTest.jsh
jshell -q < QueueTest.jsh
```

The expected outputs from running the command above are provided in `ArrayTest.out` and `QueueTest.out` under the `outputs` directory.  Similar to Lab 2, you can redirect the output to a file and compare your output with the expected output with `vim -d`.

The integrated `test.sh` now runs the two tests above before proceeding to test `Lab3` on the given test cases. 

### Test Cases

A series of test cases `Lab3.x.in` and `Lab3.x.out` are provided.  Test cases for `x` = 1 to 10 duplicate the corresponding test cases of Lab 2, with the input format updated to allow additional input of l (max counter queue length).   We set l to 0 in all these test cases. After your update your simulation to add counter queues, you code should still work for the scenarios in Lab 2 (except small differences in the input and output format).

Test case x = 11 to 13 test cases where there is no entrance queue. The rest of the test cases test scenarios with both entrance and counter queues.

## Grading

This lab is worth 16 marks and contributes 4% to your final grade.  The marking scheme is as follows:

- `Queue<T>`: 1 mark
- `Array<T>`: 2 marks
- Comparable counters: 1 mark
- Using Queue, Array, counters correctly in simulation: 1 mark
- Style: 2 marks
- Correctness: 3 marks
- OO Design: 6 marks
