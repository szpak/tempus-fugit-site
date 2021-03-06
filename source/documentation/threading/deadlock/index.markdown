---
layout: page
title: "Deadlock Detection"
date: 2012-05-25 19:49
comments: false
sharing: true
footer: false
sidebar: false
categories: [Thread Utilities]
---

The `DeadlockDetector` class allows you to programmatically detect basic deadlocks in your Java code. You can output deadlocks using the following code (note that printing a thread dump using the `ThreadDump` class will automatically attempt to find any deadlocks).

{% codeblock lang:java %}
DeadlockDetector.printDeadlocks(System.out);
{% endcodeblock %}

There are various types of deadlock in concurrent systems, broadly speaking with regard to Java, they can be categorised as

  * Java monitor cyclic locking dependency
  * Java `Lock` cyclic locking dependency
  * External resource based dependency
  * Live lock

The `DeadlockDecector` class can only spot Java monitor cyclic locking problems. It's implementation is basically the same as that used by `jconsole` and `jstack` and suffers the same limitations. Java 1.6 versions of `jstack` and `jconsole` can additionally detect `Lock` based cyclic problems. The types of deadlock it can detect can be illustrated in the example below.



{% codeblock lang:java %}
 public void potentialDeadlock() {
     new Kidnapper().start();
     new Negotiator().start();
 }

 public class Kidnapper extends Thread {
     public void run() {
         synchronized (nibbles) {
             synchronized (cash) {
                 take(cash);
             }
         }
     }
 }

 public class Negotiator extends Thread {
     public void run() {
         synchronized (cash) {
             synchronized (nibbles) {
                 take(nibbles);
             }
         }
     }
 }
{% endcodeblock %}


Here, the `Kidnapper` is unwilling to release poor Nibbles the `Cat` until he has the `Cash` but our `Negotiator` is unwilling to part with the `Cash` until he has poor Nibbles back in his arms. The deadlock detector displays this woeful situation as follows.



    Deadlock detected
    =================

    "Negotiator-Thread-1":
      waiting to lock Monitor of com.google.code.tempusfugit.concurrency.DeadlockDetectorTest$Cat@ce4a8a
      which is held by "Kidnapper-Thread-0"

    "Kidnapper-Thread-0":
      waiting to lock Monitor of com.google.code.tempusfugit.concurrency.DeadlockDetectorTest$Cash@7fc8b2
      which is held by "Negotiator-Thread-1"



[Next, Thread Utilities: Thread Conditions &raquo;](/documentation/threading/conditions)
