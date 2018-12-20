# Strategies for structuring doctype relationships.

Frappe has a lot of free functionality with respect to defining database tables and inderfaces using the Doctype model, however there are a few quirks when it comes to modelling more complex relationships.

One of the first things you may want to do is have a table of related data displayed inside a form for a particular record. This is achieved by using a **Table** field in the primary Doctype and using a secondary doctype that is marked as a child table.

**A table field definition in a primary doctype**

**Marking a secondary doctype as a child table**

**Viewing the resulting interface on a primary doctype record**

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
