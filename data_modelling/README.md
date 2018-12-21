# Strategies for structuring doctype relationships.
## Overview

Frappe has a lot of free functionality with respect to defining database tables and inderfaces using the Doctype model, however there are a few quirks when it comes to modelling more complex relationships.

One of the first things you may want to do is have a table of related data displayed inside a form for a particular record. This is achieved by using a **Table** field in the primary Doctype and using a secondary doctype that is marked as a child table.

**A table field definition in a primary doctype**
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/data_modelling/Primary%20Doctype%20Example.png?raw=true "Primary doctype definition")

**Marking a secondary doctype as a child table**
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/data_modelling/Child%20Table%20Doctype%20Example.png?raw=true "Child table doctype definition")

**Viewing the resulting interface on a primary doctype record**
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/data_modelling/Interface%20example.png?raw=true "Resulting interface")

Under the hood, the following data structures have been created:

**Primary Table (just the imprtant fields for the example)**
<table>
  <thead>
    <tr>
      <td>name</td><td>another_field</td><td>table_field_name</td>
     </tr>
  </thead>
  <tbody>
    <tr>
      <td>Parent.001</td><td>This is just some other field</td><td>null</td>
    </tr>
    <tr>
      <td>Parent.002</td><td>the table_field_name column is th link to the child doctype</td><td>null</td>
    </tr>
    <tr>
      <td>Parent.003</td><td>Notice there is not any data in it</td><td>null</td>
    </tr>
   </tbody>
</table>
  
**Child Table (just the important stuff for the example)**
<table>
  <thead>
    <tr>
      <td>name</td><td>creation</td><td>modified</td><td>modified_by</td><td>parent</td><td>parentfield</td><td>parenttype</td><td>idx</td><td>some_other_data</td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0ca751d4ac</td><td>2018-12-14 15:42:01.616689</td><td>2018-12-14 15:42:01.612836</td><td>Administrator</td><td>Parent.001</td><td>table_field_name</td><td>Parent Doctype Name</td><td>1</td><td>some_other_data</td>
    </tr>
    <tr>
      <td>0ed7cdf8c0</td><td>*vaules managed by system*</td><td>*vaules managed by system*</td><td>a@b.com</td><td>Parent.001</td><td>table_field_name</td><td>Parent Doctype Name</td><td>2</td><td>some_other_data</td>
    </tr>
    <tr>
      <td>30726d8acb</td><td>*vaules managed by system*</td><td>*vaules managed by system*</td><td>b@b.com</td><td>Parent.001</td><td>table_field_name</td><td>Parent Doctype Name</td><td>3</td><td>some_other_data</td>
    </tr>
    <tr>
      <td>473b6575c1</td><td>*vaules managed by system*</td><td>*vaules managed by system*</td><td>c@c.com</td><td>Parent.002</td><td>table_field_name</td><td>Parent Doctype Name</td><td>1</td><td>some_other_data</td>
    </tr>
    <tr>
      <td>3477df40ec</td><td>*vaules managed by system*</td><td>*vaules managed by system*</td><td>Administrator</td><td>3ae54f2</td><td>some_other_field</td><td>Different Doctype</td><td>1</td><td>some_other_data</td>
    </tr>
  </tbody>
</table>

Frappe has done a lot of nice things for us. It is managing a lot of information automatically including: 
* the creation and modification timestamps
* noting who last modified the record
* the document owner column
* automatically giving each record a unique name in the name field.
* a bunch more fields too....

Frappe has also created and managed some extremely important information fields so that the two tables can be linked together:
#### parenttype
This tells you which doctype is the parent of this record. That means if multiple doctyles all need to make use of a very similar child table pattern, this can be achieved using just one child table doctype. 

#### parent
noting the record from the parent table that each child record relates to

#### parentfield
This tells you which field in the parent table this child record is related to...In this way it is actually possible to re-use the child table in the same parent doc. This would result in two tables visible in the interface, and the data would be stored in a single child doctype.
This field is also used to manage differening parent field references if more than one doctype uses this child doctype.

#### idx
The idx field corresponds to the row ordering that is displayed in the interface. This will determine which position each item will appear at an this is automatically updated when using the interface.....it is not looked after automatically if you are working with this table programatically.

## Don't Use Child Tables at all


## Child Tables as Master Data
![alt text](Child%20is%20master%20data.png?raw=true "Child tables as master data erd example")



## Child Tables for interface 
![alt text](Child%20Tables%20for%20Interface.png?raw=true "Child tables as interface erd example")



## Child tables for weak traceability
![alt text](Weak%20Traceability.png?raw=true "Child tables for weak traceability interface erd example")



## Complex Modelling
![alt text](Complex%20Model%20Example.png?raw=true "Complex erd model example")



# Old Stuff
## Strategy 1
__Point children to parents__
If you don't need to visually see a list of child items in the parent record, then this is the simplest solution. Simply add a link field in doctype and point at another doctype. In this way many children can point at the one parent document. The restriction here is that without a child table or some other linking doctype. a child can only point to one doctype at a time (in the same field at least).

One Parent <----- many children

You could also add a transactions dashboard area to the parent, which would indicate to the user that there is a related child table. The dashboard area would add the capability to add a child record with the linked parent field already filled in. It would also have a notification count against each related doctyple based on how many are considered active/open at that moment

## Strategy 2
__A basic child table__
A good example of this is the child table in the overview screenshot. It simply stores a list of people, a short biography and a picture. It has no relationship to any tables other than the parent record (the About Us Settings doctype).
This is best used if you have a very simple relationship requirements. The primary document is related to the child table and the child table is used to store information only. 

One Parent <--------> many children

In this case using the most basic data model is ideal:
1. create the primary doctype
1. create a child doctype
1. link them together.

## Strategy 3
__Parent displays information in a table in multiple locations. The tables have very similar fields and rules, but each table deals with a separate topic.__
A good example of this use case may be a "Key Risks" table and an "Key Opportunities" table included in a "Monthly Status" doctype. Both tables have the same fields - description, ranking, date raised, status. The only thing that varies is the topic - is it a risk or is it an opportunity.

This is just like strategy 1, with the key difference we will re-use the same child table in 2 locations.
1. Create the primary doctype
  1. Add a field of type "table". Give it a unique field name.
  1. Create a second field of type "table". Give it a different unique field name.
1. Create a child table that you are going to use for both child table positions in the primary doctype.
1. Go back and edit the primary doctype and point the two table fields to the same child doc type.

## Strategy 4
__Reusing the same child table in multiple parents__
This strategy is very similar to strategy 2, except that the child doctype is used by more that one parent doctype.
This strategy may be harder to identify as the different parent doctype may be developed independently. Additionally, slight changes in needs or behaviour may preclude this from being a valid option.

A good example of where this may be useful is perhaps an billing or sales system. Both an Invoice and a sales order need a child table for line items that make up the invoice or sales order details. It may be efficient to utilise the same line items child table for both if the fields are the same. If the behaviour is different between both or they need different/extra fields then this strategy is less useful.

To implement, Build the first doctype and child doctype as per strategy 1. Then add/build primary doctype 2 and add a table field referenceing the child doctype in this primary doctype too.

## Strategy 5
__Referenceing other doctypes in a child table__
This strategy is no different than in strategy 1 in implementation, however in this case you will utilise fields of type "link" in the child table. This will allow you to gain the benefits of:
1. Being able to use pre-filled dynamic lookups (eg. the child table record references a specific person from the user table)
Using links means you don't have to type their name and/or email address fully and they will be found and accurately filled in.
1. Being able to model many to many relationships
This could be useful when building a table of schedule activities. You want to show that activity 1 is related to Activity 2 and activity 3, etc

## Strategy 6
__Complex interactions__
The doctype child table model breaks down a bit when your application gets a little more complicated due to one key restriction:

**Whilst a doctype can display a child table, a child table cannot display a child table**

So what happens when you have a data model such as:

**A Project**
     |
     | has many
     |
  **WBS**
     |
     | has many
     |
**Activities**

In This case you might want to define the WBS related to the project inside a table on the project form.
You may then want to define the activities linked to the wbs inside a table on the wbs form.

But this is not possible to do using child tables.
