title: Relational Algebra
categories: 
    - Studying Notes
    - Database
tags: [Relational Algebra]
date: 2016-06-23
---
<img src="https://c8.staticflickr.com/8/7371/27775233071_894e39fbed.jpg" width="500" height="301">
<!--more-->
----

Relational Algebra: a formal language, underpinnings of implemented languages like SQL

Introducing Relational Algebra operator based on simple college admissions database:
* College(cName, state, enrollment)
* Student(sID, sName, GPA, sizeHS)
* Apply(sID, cName, major, decision)

<img src="https://c3.staticflickr.com/8/7319/27573344650_962654069e.jpg" width="500" height="99">

select operator: pick certain rows
* students with GPA > 3.7

<img src="https://c1.staticflickr.com/8/7598/27573344720_82bcfc972a.jpg" width="398" height="61">

* students with GPA > 3.7 and HS < 1000

<img src="https://c7.staticflickr.com/8/7372/27573344790_c865852165.jpg" width="500" height="53">

* applications to standord CS major

<img src="https://c7.staticflickr.com/8/7432/27573344910_09f5ff0c54.jpg" width="500" height="51">

project operator: pick certain columns
* ID and decision of all applications

<img src="https://c1.staticflickr.com/8/7297/27573345000_5bc9c7f357.jpg" width="349" height="112">

* list of application majors and decisions

<img src="https://c3.staticflickr.com/8/7064/27573345050_42dc615313.jpg" width="394" height="100">

cross-product: combine two relations
* names and GPAs of students with HS > 1000 who applied to CS and were rejected

<img src="https://c3.staticflickr.com/8/7564/27573345130_9746e076b2.jpg" width="500" height="121">

natural join: enforce equality on all attributes with same name; eliminate one copy of duplicate attributes
* names and GPAs of students with HS > 1000 who applied to CS at college with enr > 20000 and were rejected

<img src="https://c1.staticflickr.com/8/7791/27573345240_2f9b4640a4.jpg" width="500" height="92">

theta join: natural join with condition

<img src="https://c3.staticflickr.com/8/7098/27573345330_befbf4619d.jpg" width="500" height="48">

union operator:
* list of college and student names

<img src="https://c3.staticflickr.com/8/7347/27573344490_40c78b9b7b.jpg" width="378" height="164">

difference operator:
* IDs and names of students who didn't apply anywhere

<img src="https://c3.staticflickr.com/8/7502/27573344410_26d586fbd4.jpg" width="500" height="103">

intersection operator:
* names that are both a college name and a student name

<img src="https://c5.staticflickr.com/8/7341/27573344340_7b61fe1064.jpg" width="500" height="89">

rename operator: reassign the schema in the result of E; to unify schemas for set operators; for disambiguation in "self-joins"

<img src="https://c3.staticflickr.com/8/7319/27573344130_08b46a039a.jpg" width="322" height="105">

* list of college and student names

<img src="https://c5.staticflickr.com/8/7632/27573344020_cffcf285b2.jpg" width="500" height="144">

* pairs of colleges in same state

<img src="https://c1.staticflickr.com/8/7441/27573343840_aa724cd602.jpg" width="500" height="124">

assignment statements: a way to break down relational algebra expressions into parts
* pairs of colleges in same state

<img src="https://c5.staticflickr.com/8/7367/27573343740_495d3d7f39.jpg" width="493" height="268">

expression tree: allow to visualize structure of the expression
* GPAs of students applying to CS in CA

<img src="https://c1.staticflickr.com/8/7408/27573343680_48499dae78.jpg" width="500" height="199">