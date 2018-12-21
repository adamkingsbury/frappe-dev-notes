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
If you don't need to visually see a list of child items in the parent record, then this is the simplest solution. Simply add a link field in doctype and point at another doctype. In this way many children can point at the one parent document. The restriction here is that without a child table or some other linking doctype. a child can only point to one doctype at a time (in the same field at least).

![alt text](Don't%20use%20child%20tables%20at%20all.png?raw=true "Don't use child tables at all ERD example")

You could also add a transactions dashboard area to the parent, which would indicate to the user that there is a related child table. The dashboard area would add the capability to add a child record with the linked parent field already filled in. It would also have a notification count against each related doctyple based on how many are considered active/open at that moment

The biggest issue with this option is that if you navigate directly to a doctype lower down the chain, then you will see an unfiltered view of the table. Additionally, there may be issues when adding a new document with respect to ensuring valid combinations of the upper doctypes on the new record. For example, how do you ensure that a new activity has a valid compination of project and wbs?

This will most likely require the following considerations
1. Place the lower-down doctypes on the transactions dashboard. This will give you quick add and goto functionality that will pre-apply filters on link fields
1. On the lower-doctypes:
    1. Add field visibility filters on the lower-down doctypes so that users must select a link on the highest order doctypes first.
    1. Place custom result filters on secondary links so that (when the are revealed) they filter results by the seleted higher-order doctype link values)
    1. Possibly look into removing the lower-order doctypes from menus and searches - this may be difficult as this is not a capability out of the box.

## Child Tables as Master Data
This is the most obvious use case for child tables. The Primary doctype has links to multiple child records. Each child record is tightly linked to its parent.

The child table itself is used to store important information such as counts, quantities or other key information.

In addition to this, it is also optional to use link fields from the child table to other doctypes. You would most likely do this to access master data from the related doctype.

In the example below the Activity table is tightly linked to Activity Resource child table, where all of the information about resources assigned to the activity is stored. 

The child table also has links to the resources doctype. The resources doctype is used to define a master list of available resources in the application and their default prices and work rates. The child table will have a quick reference to this master data via the link field, but will independantly store its values from the master data table onve the primary selection is made.

![alt text](Child%20is%20master%20data.png?raw=true "Child tables as master data erd example")

This type of configuration has few drawbacks as this is primarily the intended usage of child table data. Both the interface itself and the datastructures are well managed by the default functionality of frappe.

One key area of consideration is custom behaviour for child table fields in the interface. Child tables do not have their own **[Doctype Name].js** file. Instad custom behaviour needs to be built into the parent doctype's **[Doctype Name].js** file. To deal with child table fields, the following triggers are made available dynamically in parent doctypes:
1. *[Child Doctype Fieldname]_add*: This will be called when a new child entry is added. This would most likely be used to recalculate totals or call triggers on other related doctypes.
1. *[Child Doctype Fieldname]_move*: This will be called whenever the position of a child doctype record is changed
1. *[Child Doctype Fieldname]_before_remove*: This will be called before a row is removed. Returning *false* will cancel the remove.
1. *[Child Doctype Fieldname]remove*: Called after removing a record from the child table, this trigger could be used for calling other supporting functions to recalculate totals or tidy up/adjust other related tables.

## Child Tables for interface 
Given the fact that Doctypes are intrinsically a representation not just of underlying data structures, but are also strongly tied to the interface within frappe, there are times where child tables could be more readily used for interface/viewing functionality rather than true data storage.

A good example of this is the way in which ERPnext implements its Project and Activity relationship. For interface purposes, the project doctype contains a child table of activities so that a user can create and view activities directly within the related project. The activities themselves however can benefit from gantt and kanban views, which are not available if the activities are only represented within a child table.

To resolve this issue, a detailed activity doctype is defined as the master activity record. This is where all detailed activity information is stored and this is where gantt and kanban views can be accessed. The project activity child table is simply used as an interface convenience and only contains some basic summary information, along with buttons and other interface actions in order to jump to the detailed activity doc type.

![alt text](Child%20Tables%20for%20Interface.png?raw=true "Child tables as interface erd example")

Using child tables is this manner is not the originally intended purpose of the child doctype, so this type of design will bring with it some challenges:
1. Referential integrity will be enforced:
    1. When deleteing from the detailed activity doctype you would need to delete any child table records before you will be able to delete the detailed activity record.
    1. When deleting from the child table you should also delete the detailed activity record.
    1. when deleting the project, then you should also delete the child table records and the detailed activity records.
1. Choosing where information is stored will be a key challenge. in most cases the best strategy will be to keep everything on the detailed master doctypes, hoever some of the information may need to be copied to the child table for viewing purposes. This means you will also need to consider how information updates are performed:
    1. You could choose to make the child table information fully read-only, thus ensuring that you have to maintain the master record only.
    1. You may also choose to make the child table more interactive by making the values fully ediatable. To do this you would need to create custom functions to copy data from the master record onto the non-linked child table fields. Triggers would then need to be created to synchronise changes from child to master and vice-versa depending on where the edits took place.
    

## Child tables for weak traceability
![alt text](Weak%20Traceability.png?raw=true "Child tables for weak traceability interface erd example")



## Complex Modelling
![alt text](Complex%20Model%20Example.png?raw=true "Complex erd model example")



# Old Stuff
## Strategy 1
__Point children to parents__


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
