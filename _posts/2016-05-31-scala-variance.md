---
title: Scala Variance - Covariance, Contravariance and Nonvariance
layout: default
---

{{ page.title }}
================

<head>
<style>
table, th, td {
    border: 1px solid black;
    border-collapse: collapse;
    padding: 1px;
}
th { background-color: #99ccff; }
tr:nth-child(even) {background-color: #e6f2ff}
</style>
</head>

Variance as defined in [Programming in Scala](https://www.artima.com/pins1ed/glossary.html#g-1249575311):

*A type parameter of a class or trait can be marked with a variance annotation, either covariant (+) or contravariant (-). Such variance annotations indicate how subtyping works for a generic class or trait. For example, the generic class List is covariant in its type parameter, and thus List[String] is a subtype of List[Any]. By default, i.e., absent a + or - annotation, type parameters are nonvariant.*

Variance as defined in Wikipedia:
*Variance refers to how subtyping between more complex types (list of Cats versus list of Animals, function returning Cat versus function returning Animal, ...) relates to subtyping between their components.*

Transforming definition above to code as below. I have defined a generic class CellNonvariant with type parameter T. The type parameter is nonvariant.
```scala  class CellNonvariant[T] { } ```


<h2>Learning through code:</h2>

```scala

// Defining a few type constructor class with various variance type parameter 
class Nonvariant[T] { }     // default behaviour is nonvariant subtyping
class Covariant[+T] { }     // subtyping is covariant
class Contravariant[-T] { } // subtyping is contravariant

```

<table>
<tr>
 <th>Variance Type</th>
 <th>Code Sample</th>
 <th>Comments</th>
</tr>
<tr>
 <td>Nonvariant - assign subtype</td>
 <td>
   <pre lang="scala">
   val nv1String = new Nonvariant[String]
   val nv2Any: Nonvariant[Any] = nv1String
   </pre>
 </td>
 <td>
  <font color="red">ERROR.</font> type mismatch;  found   : Nonvariant[String]  required: Nonvariant[Any] 
  <br>As this is nonvariant subtyping, it can only be substituted with the <u><b>exact</b></u> type parameter defined at construction. In this case Any.
 </td>
</tr>
<tr>
 <td>Nonvariant - assign super/parent type</td>
 <td>
   <pre lang="scala">
   var nv1String1 = new Nonvariant[String]
   val nv3Any1 = new Nonvariant[Any]
   nv1String1 = nv3Any1
   </pre>
 </td>
 <td>
  <font color="red">ERROR.</font> type mismatch;  found   : Nonvariant[Any]  required: Nonvariant[String] 
  <br>As this is nonvariant subtyping, it can only be substituted with the <u><b>exact</b></u> type parameter defined at construction. In this case String.
 </td>
</tr>
<tr>
 <td>Nonvariant - assign exact type</td>
 <td>
   <pre lang="scala">
   val nv1String = new Nonvariant[String]
   val nv2String: Nonvariant[String] = nv1String
   </pre>
 </td>
 <td></td>
</tr>
<tr>
 <td>Covariant - assign subtype</td>
 <td>
   <pre lang="scala">
   val cv1String = new Covariant[String]
   val cv2Any: Covariant[Any] = cv1String
   </pre>
 </td>
 <td></td>
</tr>
<tr>
 <td>Covariant - assign super/parent type</td>
 <td>
   <pre lang="scala">
   val cv3Any = new Covariant[Any]
   val cv4String: Covariant[String] = cv3Any
   </pre>
 </td>
 <td>
  <font color="red">ERROR.</font> type mismatch;  found   : Covariant[Any]  required: Covariant[String] 
  <br>As this is covariant subtyping, it can only be substituted with the <u><b>exact</b></u> type parameter or its <u><b>subtype</b></u> as defined at construction. In this case, it can only be substituted with String or subtype of String.
 </td>
</tr>
<tr>
 <td>Covariant - assign exact type</td>
 <td>
   <pre lang="scala">
   val cv3Any = new Covariant[Any]
   val cv5Any: Covariant[Any] = cv3Any
   </pre>
 </td>
 <td></td>
</tr>
<tr>
 <td>Contravariant - assign subtype</td>
 <td>
   <pre lang="scala">
   val ctv1String = new Contravariant[String]
   val ctv2Any: Contravariant[Any] = ctv1String
   </pre>
 </td>
 <td>
  <font color="red">ERROR.</font> type mismatch;  found   : Contravariant[String]  required: Contravariant[Any] 
  <br>As this is contravariant subtyping, it can only be substituted with <u><b>super or parent</b></u> parameter type as defined at construction. In this case, it can only be substituted with instance who is parent of Any type.
 </td>
</tr>
<tr>
 <td>Contravariant - assign super/parent type</td>
 <td>
   <pre lang="scala">
   val ctv3Any = new Contravariant[Any]
   val ctv4String: Contravariant[String] = ctv3Any
   </pre>
 </td>
 <td></td>
</tr>
<tr>
 <td>Contravariant - assign exact type</td>
 <td>
   <pre lang="scala">
   val ctv3Any = new Contravariant[Any]
   val ctv5Any: Contravariant[Any] = ctv3Any
   </pre>
 </td>
 <td></td>
</tr>
</table>

<h2>Variance Defined</h2>

Variance is about substitutability of parameter type defined in generic class or trait. For instance, by defining a class List[T] and constructing an instance of List[Animal]. Can an instance of List[Animal] be a substitute of List[Dog], given Dog is a subtype of Animal? or vice-versa? As List is a container, what type of instance can be put in List[Dog]? Can it only accept Dog instances or any sub-type of Dog such as Terrier or Bulldog? Can it accept any super (parent) type of Dog such as Animal?

Variance is important for developer to specify clearly their intention. Without it, the intention of the developer will be vague and has a potential of creating buggy code. Take for instance Java arrays which is covariance, the below code is a contrived example in which it is possible to creates runtime exception error. 

```java
Integer[] ints = new Integer[] { 1, 5, 10 };
Object[] objs = ints;
objs[0] = 3.56;
Integer myInt = ints[0];
```
When executed, the following error occur:

```
Exception in thread "main" java.lang.ArrayStoreException: java.lang.Double
      at org.brian.disruptor.initial.Test1.main(Test1.java:9)
```

In Scala, when one create a generic class/trait, one can define the variance of type parameter to be either nonvariant, covariant or contravariant.

<table>
 <tr>
   <th>Variance Type</th>
   <th>Definition</th>
 </tr>
 <tr>
   <td>Nonvariant</td>
   <td>Can only assign an exact instance type as per construsted parameterized type<br>
    If I have a List[Dog], if it can only be substituted by another List[Dog] and no other type, then this kind of variance is called Nonvariant
    <pre lang="scala">
    val animal:List[Dog] = List[Dog]()
    </pre>
   </td>
 </tr>
 <tr>
   <td>Covariant</td>
   <td>
    Can only assign an sub type or exact instance type as per construsted parameterized type. <br>
    If I have a List[Animal], can it be substituted by List[Dog]? If this is true, then the this kind of variance is called Covariant.
    <pre lang="scala">
    val animal:List[Animal] = List[Dog]()
    </pre>
   </td>
 </tr>
 <tr>
   <td>Contravariant</td>
   <td>
    Can only assign an exact instance type as per construsted parameterized type. <br>
    If I have a List[Dog], can it be substituted by List[Animal]? If this is true, then the this kind of variance is called Contravariant.
    <pre lang="scala">
    val animal:List[Dog] = List[Animal]()
    </pre>
   </td>
 </tr>
</table>

<h2>Real-life example use case of Variance?</h2>
* Use case for Covariant - a sort method which accept Comparable interface in Java. Given the sort method operates on Comparable compareTo method  which produce an int indicating order relativity, the sort method can operates on any subtype of Comparable.
* Use case for Contravariant - can only be used to annotate write-only values.
 

<h2>How does variance annotation impact method argument or method return type?</h2>
Scala specification defines how variance behaves at various constructs. The variance of a method parameter is the opposite of the variance position of the enclosing parameter clause. This means if the enclosing is covariance, the opposite is contravariance. However, if the enclosing is nonvariance the opposite is to be itself.

Read-only data types (sources) can be covariant; write-only data types (sinks) can be contravariant. Mutable data types which act as both sources and sinks should be invariant.

```scala

class BoxNonvariant[T](xObj: T) {
  private[this] var obj = xObj
  def get = obj
  def set(xObj: T) { obj = xObj }
}

class BoxCovariant[+T](xObj: T) {
  private[this] var obj = xObj
  def get = obj
// covariant type T occurs in contravariant position in type T of value x  
// def set(xObj: T) { obj = xObj }
//  def set[U>:T](x: U) {  }
}

class BoxContravariant[-T](initial: T) {
  private[this] var current = initial
// contravariant type T occurs in covariant position in type => T of method get
//  def get = current
  def set(x: T) { current = x }
}

```



<h2>Useful references</h2>
1. [Artima - Type Parameterization](http://www.artima.com/pins1ed/type-parameterization.html)
2. [http://blog.kamkor.me/Covariance-And-Contravariance-In-Scala/](http://blog.kamkor.me/Covariance-And-Contravariance-In-Scala/)
3. [https://blog.codecentric.de/en/2015/04/the-scala-type-system-parameterized-types-and-variances-part-2/](https://blog.codecentric.de/en/2015/04/the-scala-type-system-parameterized-types-and-variances-part-2/)
4. [http://typelevel.org/blog/2016/02/04/variance-and-functors.html](http://typelevel.org/blog/2016/02/04/variance-and-functors.html)
5. [Scala Specification - variance-annotations](http://www.scala-lang.org/files/archive/spec/2.11/04-basic-declarations-and-definitions.html#variance-annotations)
6. [https://en.wikipedia.org/wiki/Covariance_and_contravariance_(computer_science)](https://en.wikipedia.org/wiki/Covariance_and_contravariance_(computer_science))














