title: Introduction, Relational Database and XML
categories: 
    - Studying Notes
    - Database
tags: [Relational Database, XML, DTD, XSD]
date: 2016-05-31
---
<img src="https://c5.staticflickr.com/8/7549/27134031940_34d06d4bd0.jpg" width="500" height="224">
<!--more-->
----

## Introduction and Relational Databases

### Introduction

database management system (DBMS): massive, persistent, safe, multi-user, convenient, efficient, reliable

database applications may be programmed via "frameworks"; DBMS may run in conjunction with "midleware"; data-intensive applications may not use DBMS at all

data model: a description of how data is structured. eg: relational data model(a set of records), XML(hierarchical structure, labeled values), graph data model(nodes and edges)xml, JSON

schema versus data: like types and variables in programming language. schema sets up structure of database

data definition language(DDL): used to set up a schema

data manipulation or query language(DML): used to querying and modifying the data

people about database: DBMS implementer(build system), database designer(establish schema), database application developer(program operate on database), database administrator(loads data, keeps running smoothly)

### The Relational Model

database is set of named relations or tables; each table has a set of named attributes or columns; each tuple or row has a value for each attribute; each attribute has a type or domain(integer, domain)

schema is structural description of relations in database; instance is actual contents at given point in time

NULL: special value for "unknown" or "undefined"

key: attribute whose value is unique in each tuple or set of attributes whose combined values are unique

<img src="https://c5.staticflickr.com/8/7291/27134032260_774b971927.jpg" width="500" height="167">

creating tables in SQL:
create table student(ID, name, GPA, photo)
create table college(name string, state char(2), enrollment integer)

### Querying Relational Databases

steps in creating and using a relational database:
* design schema; create using DDL
* "bulk load" initial data
* repeat: execute queries and modifications

<img src="https://c7.staticflickr.com/8/7420/27134032150_de78c8a758.jpg" width="500" height="194">

queries return tables(compositional, closed)

<img src="https://c6.staticflickr.com/8/7533/27338321581_ccb8a3f4d5.jpg" width="500" height="215">

query language:
* relational algebra(formal)
* SQL(actual/implemented)

## XML Data

### Well-formed XML

Extensible Markup Language(XML): standard for data representation and exchange; document format similar to HTML(tags describe content instead of formatting); also have streaming format

basic constructs: 
* tagged elements(nested): opening tag, text or other sub-elements, closing tag
* attributes: within element's opening tag; attribute name, the equal sign and attribute value
* text: like leaf element in tree(XML)

relational model versus XML:

|<font size=2>  (none) </font> |<font size=2>  Relational  </font>|<font size=2>  XML  </font>|
| :| |
|<font size=2>  structure </font> |<font size=2>  tables  </font>|<font size=2>  hierarchical tree, graph  </font>|
|<font size=2>  schema </font> |<font size=2>  fixed in advance  </font>|<font size=2>  flexible, self-describing  </font>|
|<font size=2>  queries </font> |<font size=2>  simple, nice  </font>|<font size=2>  less so  </font>|
|<font size=2>  ordering </font> |<font size=2>  none  </font>|<font size=2>  implied  </font>|
|<font size=2>  implementation </font> |<font size=2>  native  </font>|<font size=2>  add-on  </font>|

### DTDs, IDs & IDREFs

XML adhere to basic structural requirement: single root element; matched tags, proper nesting; unique attributes within elements

XML also adhere to content-specific specification:
* Document Type Descriptor(DTD)
* XML Schema(XSD)

<img src="https://c6.staticflickr.com/8/7286/27338321461_1df61faefc.jpg" width="500" height="212">

DTD: grammar-like language for specifying elements, attributes, nesting, ordering, #occurrences; also special attribute types ID and IDREFS(specify pointers in document)

DTD/XSD versus none (well-formed):
* \+: programs can assume structure; CSS/XSL can assume structure; as a specification language(data exchange); specification for documentation
* \-: flexibility, easy to change; DTDs/XSDs can be messy and irregular

Demo 1 of XML:
```
<Bookstore>
   <Book ISBN="ISBN-0-13-713526-2" Price="85" Edition="3rd">
      <Title>A First Course in Database Systems</Title>
      <Authors>
         <Author>
            <First_Name>Jeffrey</First_Name>
            <Last_Name>Ullman</Last_Name>
         </Author>
         <Author>
            <First_Name>Jennifer</First_Name>
            <Last_Name>Widom</Last_Name>
         </Author>
      </Authors>
   </Book>
   <Book ISBN="ISBN-0-13-815504-6" Price="100">
      <Title>Database Systems: The Complete Book</Title>
      <Authors>
         <Author>
            <First_Name>Hector</First_Name>
            <Last_Name>Garcia-Molina</Last_Name>
         </Author>
         <Author>
            <First_Name>Jeffrey</First_Name>
            <Last_Name>Ullman</Last_Name>
         </Author>
         <Author>
            <First_Name>Jennifer</First_Name>
            <Last_Name>Widom</Last_Name>
         </Author>
      </Authors>
      <Remark>
         Buy this book bundled with "A First Course" - a great deal!
      </Remark>
   </Book>
</Bookstore>
```
root element is Bookstore, has two Book sub-elements. 
the first book has an ISBN number, price and editions as attributes.

Demo 1 of DTD:
```
<!DOCTYPE Bookstore [
	<!ELEMENT Bookstore (Book | Magazine)*>
	<!ELEMENT Book (Title, Authors, Remark?)>
	<!ATTLIST Book  ISBN CDATA #REQUIRED
	                Price CDATA #REQUIRED
	                Edition CDATA #IMPLIED>
	<!ELEMENT Magazine (Title)>
	<!ATTLIST Magazine  Month CDATA #REQUIRED
	                    Year CDATA #REQUIRED>
	<!ELEMENT Title (#PCDATA)>
	<!ELEMENT Authors (Author+)>
	<!ELEMENT Remark (#PCDATA)>
	<!ELEMENT Author (First_Name, Last_Name)>
	<!ELEMENT First_Name (#PCDATA)>
	<!ELEMENT Last_Name (#PCDATA)>
]>
```
Like regular expression. This DTD named Bookstore and root element called bookstore, having any number of elements that called book or magazine. 
Book element has a title followed by authors, followed by an optional remark in this order. 
Book has 3 attributes: ISBN, price, edition. CDATA is type of attribute, same as string. Required means attributes must be present, implied means it doesn't have to. 
Title is a leaf consist of string data, so add hash PC data in parentheses in DTD.

Demo 2 of XML:
```
<Bookstore>
   <Book ISBN="ISBN-0-13-713526-2" Price="100" Authors="JU JW">
      <Title>A First Course in Database Systems</Title>
   </Book>
   <Book ISBN="ISBN-0-13-815504-6" Price="85" Authors="HG JU JW">
      <Title>Database Systems: The Complete Book</Title>
      <Remark>
         Amazon.com says: Buy this book bundled with
         <BookRef book="ISBN-0-13-713526-2"/>
         - a great deal!
      </Remark>
   </Book>
   <Author Ident="HG">
      <First_Name>Hector</First_Name>
      <Last_Name>Garcia-Molina</Last_Name>
   </Author>
   <Author Ident="JU">
      <First_Name>Jeffrey</First_Name>
      <Last_Name>Ullman</Last_Name>
   </Author>
   <Author Ident="JW">
      <First_Name>Jennifer</First_Name>
      <Last_Name>Widom</Last_Name>
   </Author>
</Bookstore>
```
Book has ID refs attribute authors and its value can refer to one or more strings that are ID attributes. 
Author element has ident attribute as ID type. 
ISBN is an ID attribute for book element and BookRef element has ID refs attribute book.

Demo 2 of DTD:
```
<!DOCTYPE Bookstore [
	<!ELEMENT Bookstore (Book*, Author*)>
	<!ELEMENT Book (Title, Remark?)>
	<!ATTLIST Book  ISBN ID #REQUIRED
	                Price CDATA #REQUIRED
	                Authors IDREFS #REQUIRED>
	<!ELEMENT Title (#PCDATA)>
	<!ELEMENT Remark (#PCDATA | BookRef)*>
	<!ELEMENT BookRef EMPTY>
	<!ATTLIST BookRef book IDREF #REQUIRED>
	<!ELEMENT Author (First_Name, Last_Name)>
	<!ATTLIST Author Ident ID #REQUIRED>
	<!ELEMENT First_Name (#PCDATA)>
	<!ELEMENT Last_Name (#PCDATA)>
]>
```
Book has a ID attribute ISBN and a IDREFS attribute Authors. 
Remark consist of the PC data(string) or a book reference and zero more instances of those. 
BookRef is an empty element and has a IDREF attribute book.

### XML Schema

XSD: 
* extensive language
* like DTDs, can specify elements, attributes, nesting, ordering, #occurrences
* also data types, keys, (typed)pointers, and more
* written in XML

demo of XSD:
```
<?xml version="1.0" ?>
<!-- XSD for Bookstore-XSD.xml -->

<xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema">
   <xsd:element name="Bookstore">
      <xsd:complexType>
         <xsd:sequence>
            <xsd:element name="Book" type="BookType"
                         minOccurs="0" maxOccurs="unbounded" />
            <xsd:element name="Author" type="AuthorType"
                         minOccurs="0" maxOccurs="unbounded" />
         </xsd:sequence>
      </xsd:complexType>
      <xsd:key name="BookKey">
         <xsd:selector xpath="Book" />
         <xsd:field xpath="@ISBN" />
      </xsd:key>
      <xsd:key name="AuthorKey">
         <xsd:selector xpath="Author" />
         <xsd:field xpath="@Ident" />
      </xsd:key>
      <xsd:keyref name="AuthorKeyRef" refer="AuthorKey">
         <xsd:selector xpath="Book/Authors/Auth" />
         <xsd:field xpath="@authIdent" />
      </xsd:keyref>
      <xsd:keyref name="BookKeyRef" refer="BookKey">
         <xsd:selector xpath="Book/Remark/BookRef" />
         <xsd:field xpath="@book" />
      </xsd:keyref>
   </xsd:element>
   <xsd:complexType name="BookType">
      <xsd:sequence>
         <xsd:element name="Title" type="xsd:string" />
         <xsd:element name="Authors">
            <xsd:complexType>
               <xsd:sequence>
                  <xsd:element name="Auth" maxOccurs="unbounded">
                     <xsd:complexType>
                        <xsd:attribute name="authIdent" type="xsd:string"
                                       use="required" />
                     </xsd:complexType>
                  </xsd:element>
               </xsd:sequence>
            </xsd:complexType>
         </xsd:element>
         <xsd:element name="Remark" minOccurs="0">
            <xsd:complexType mixed="true">
               <xsd:sequence>
                  <xsd:element name="BookRef" minOccurs="0"
                               maxOccurs="unbounded">
                     <xsd:complexType>
                        <xsd:attribute name="book" type="xsd:string"
                                       use="required" />
                     </xsd:complexType>
                  </xsd:element>
               </xsd:sequence>
            </xsd:complexType>
         </xsd:element>
      </xsd:sequence>
      <xsd:attribute name="ISBN" type="xsd:string" use="required" />
      <xsd:attribute name="Price" type="xsd:integer" use="required" />
   </xsd:complexType>
   <xsd:complexType name="AuthorType">
      <xsd:sequence>
         <xsd:element name="First_Name" type="xsd:string" />
         <xsd:element name="Last_Name" type="xsd:string" />
      </xsd:sequence>
      <xsd:attribute name="Ident" type="xsd:string" use="required" />
   </xsd:complexType>
</xsd:schema>
```
difference from DTD:
* typed values: attribute/element have types other than string
* key declarations: similar to ID, have selector and field, not to be globally unique
* references: specific key have specific reference, also have selector and field
* currents constraints: can specify how many times an element type is allowed to occur
