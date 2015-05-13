---
title: NonOverridingEquals
layout: bugpattern
category: JDK
severity: WARNING
maturity: MATURE
---

<!--
*** AUTO-GENERATED, DO NOT MODIFY ***
To make changes, edit the @BugPattern annotation or the explanation in docs/bugpattern.
-->

<div style="float:right;"><table id="metadata">
<tr><td>Category</td><td>JDK</td></tr>
<tr><td>Severity</td><td>WARNING</td></tr>
<tr><td>Maturity</td><td>MATURE</td></tr>
</table></div>

# Bug pattern: NonOverridingEquals
__equals method doesn't override Object.equals__

## The problem
Defining a method that looks like `Object#equals` but doesn't actually override
`Object#equals` is dangerous.  The result of the comparison could differ
depending on the declared type of the argument passed into the `equals` call.

For example, consider this code:

```java
public class Example {
  private int value;

  public Example(int value) {
    this.value = value;
  }

  public boolean equals(Example other) {
    return this.value == other.value;
  }

  public static void main(String[] args) {
    Example exampleA = new Example(1);
    Example exampleB = new Example(1);
    System.out.println(exampleA.equals(exampleB));
  }
}
```

This will print `true`.  Suppose you refactor it so that the variable
`exampleB` is declared as an `Object` instead.  Now this code will print 
`false`, because Java's overload resolution will choose the default 
`equals(Object)` implementation instead of the `equals(Example)` method defined 
in this class.

If this equals method is intended to be a type-specific helper for an `equals` 
method that *does* override `Object#equals`, either inline it into the 
overriding `equals` method or rename it to something other than `equals` to 
avoid ambiguity in overload resolution.

If you don't want to write and maintain `equals` and `hashCode` methods 
by hand, consider rewriting this class to use [AutoValue]
(https://github.com/google/auto/tree/master/value).

## Suppression
Suppress false positives by adding an `@SuppressWarnings("NonOverridingEquals")` annotation to the enclosing element.