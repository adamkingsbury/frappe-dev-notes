# Mapping Between Doctypes
Sometimes you may wish to create a new Doctype record based on the doctype record that you are currently viewing. Possible scenarios may include:

1. Duplicating a transaction to save time on a repeat purchase
1. Moving from one transaction state (represented by one Doctype) to the next transaction state (represented by a different - but with similar field - doctype)
1. Generating a master document record based on some data you have filled in on a child table (a conveniece interface child table) somewhere else
1. Generating a child table record (in a convenience interface child table somewhere else) when you create a master doctype record

Frappe provides client side functionality and server side functionality to deal with this:
## Client Side
Usually this type of functionality will be incorporated by createing a custom button. When the user clickes the button the *frappe.model.open_mapped_doc* function is called and a copy of the current doctype is copied over to a new destination doctype.

The new doctype will then be opened on screen, **usaved** and awaiting the user to fill in any further required details.

Whilst the open mapped doc function *can* try to map data automatically, it is much better practice to explicitly provide a mapping specification telling the function which field in the source doc, goes to which filed in the destination doc.

### creating the button hook in [Doctype Name].js
```Javascript
frappe.ui.form.on("Doctype Name Here", {
	setup: function(frm) {
    this.frm.add_custom_button(
      'Button Title Here',
			function(frm) { me.make_material_request() }
      , __("Group Button Title Here")
    );
    
    make_mapped_doc: function() {
		frappe.model.open_mapped_doc({
			method: "erpnext.selling.doctype.sales_order.sales_order.make_sales_invoice",
			frm: this.frm
		})
	},
  }
});
```
