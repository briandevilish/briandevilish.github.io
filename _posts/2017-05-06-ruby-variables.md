---
title: Ruby - various type of variables
layout: default
---

{{ page.title }}
================
<head>
<style>
table, th, td {
    border: 1px solid black;
    border-collapse: collapse;
    margin: 5px 0;
    text-align: left;
    vertical-align: top;
}
th { background-color: #99ccff; }
tr { background-color: #e6f2ff; }
pre {
    display: block;
    font-family: monospace;
    white-space: pre;
    margin: 1em 0;
}
</style>
</head>

<h2>Differences...</h2>
<ol>
 <li>Instance variable of a class's object</li>
   <ul>
     <li>a regular instance variables that happen to belong to an object of class &lt;class_in_which_it_is_defined&gt;</li>
     <li>if one define @my_instance in class Entity, the @my_instance does not belong to an instance of class Entity, but to the "class object" Entity, which is an instance of class Class.</li>
     <li>accessible by an instance or by a subclass</li>
     <li>this similar to Java object instance variable</li>
   </ul>
 <li>Instance variable of a class</li>
   <ul> 
    <li>a regular instance variables that happen to belong to an object of class Class</li>
    <li>can only be accessed by the class itself</li>
    <li>not accessible by an instance or by a subclass</li>
    <li>different from Java's static fields.</li>
   </ul>
 <li>Class Variable</li>
   <ul>
    <li>a variable in a class or more strictly to class hierarchies</li>
    <li>can be access by an instance or by a subclass</li>
    <li>it is akin to global variable as if a class variable is defined in class Object, all its descendants will have access too. This means that if one is unaware of the existence of the class variable, it will inadvertently override the value</li>
   </ul>
</ol>

<h2>Differences by code</h2>
<table border="1">
<tr>
 <th>Instance variable of a class's object</th>
 <th>Instance variable of a class</th>
 <th>Class Variable</th>
</tr>
<tr>
 <td>
   <pre lang="ruby">
class InstanceVariableInRuby
  def set_instance_variable
    puts "set_instance_variable\n"\
	     "  @my_instance_variable=10"
    @my_instance_variable=10
  end

  def access_instance_variable
    puts "access_instance_variable:"\
         "#{@my_instance_variable}"
  end
end

class SubClassOfInstanceVariableInRuby < InstanceVariableInRuby
  def access_super_instance_variable
    puts "access_super_instance_variable:"\
         " #{@my_instance_variable}"
  end
end

iv = InstanceVariableInRuby.new
iv.set_instance_variable
iv.access_instance_variable
sciv = SubClassOfInstanceVariableInRuby.new
sciv.set_instance_variable
sciv.access_super_instance_variable
   </pre>
 </td>
 <td>
   <pre lang="ruby">
class ClassInstanceVariableInRuby
  @my_class_instance_variable

  def self.set_class_instance_variable
    puts "set_class_instance_variable\n"\
         "  @my_class_instance_variable=20\n"\
         "  @my_class_instance_variable2=40"
	@my_class_instance_variable=20
	@my_class_instance_variable2=40
  end

  def self.access_class_instance_variable
    puts "access_class_instance_variable"\
         " @my_class_instance_variable: "\
         "#{@my_class_instance_variable}"
    puts "access_class_instance_variable "\
         "@my_class_instance_variable2: "\
         "#{@my_class_instance_variable2}"
  end
end

ClassInstanceVariableInRuby.set_class_instance_variable
ClassInstanceVariableInRuby.access_class_instance_variable
   </pre>
 </td>
 <td>
   <pre lang="ruby">
class ClassVariableInRuby
  @@my_class_variable

  def set_class_variable
    puts "set_class_variable\n"\
         "  @@my_class_variable=30"
	@@my_class_variable = 30
  end

  def access_class_variable
    puts "access_class_variable:"\
         " #{@@my_class_variable}"
  end

  def self.set_class_variable
    puts "self.set_class_variable\n"\
         "  @@my_class_variable+=40"
	@@my_class_variable += 40
  end

  def self.access_class_variable
    puts "self.access_class_variable:"\
         " #{@@my_class_variable}"
  end
end

civ = ClassVariableInRuby.new
civ.set_class_variable
civ.access_class_variable
ClassVariableInRuby.access_class_variable

ClassVariableInRuby.set_class_variable
ClassVariableInRuby.access_class_variable
civ.access_class_variable
   </pre>
 </td> 
</tr>
<tr>
 <td>
   <b>Output</b>
   <pre>
set_instance_variable
  @my_instance_variable=10
access_instance_variable:10
set_instance_variable
  @my_instance_variable=10
access_super_instance_variable: 10
   </pre>
 </td>
 <td>
   <b>Output</b>
   <pre>
set_class_instance_variable
  @my_class_instance_variable=20
  @my_class_instance_variable2=40
access_class_instance_variable @my_class_instance_variable: 20
access_class_instance_variable @my_class_instance_variable2: 40
   </pre>
 </td>
 <td>
   <b>Output</b>
   <pre>
set_class_variable
  @@my_class_variable=30
access_class_variable: 30
self.access_class_variable: 30
self.set_class_variable
  @@my_class_variable+=40
self.access_class_variable: 70
access_class_variable: 70

   </pre>
 </td>
</tr>
</table>


<h2>Useful references</h2>
<ol>
 <li>[Ruby official FAQ on Classes and modules](https://www.ruby-lang.org/en/documentation/faq/8/)</li>
</ol>

