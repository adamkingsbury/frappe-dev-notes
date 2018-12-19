# Creating buttons in Frappe

There are a number of different location where you can create buttons or actions within frappe:

## Custom Buttons on a form
This is one of the simplest options available. Adding custom buttons on a form view can be done by adding code into the *[Doctype name].js* file. Usually this will be done in the onload event, but could also be triggered on the refresh or another event if the button should only appear under certain circumstances.

![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Custom%20Button.png?raw=true "Custom button on a form")

To add the button to a form use the following template inside the *[Doctype name].js* file:
```Javascript
frappe.ui.form.on('doctype name', {
	refresh: function(frm) {

		frm.page.add_inner_button(
			"Button Text",
			function(){

				// code to run when button is clicked goes here

			},
			null,		//This is a non-grouped button so don't supply a group
			"primary" 	//This sets the style of the top level button (default, primary, secondary, warning, success, danger, info)
		);
	}
});
```

To add a custom button on a list view, use the follwoing template in a *[Doctype name]_list.js* file:
```Javascript
frappe.listview_settings['Doctype name here'] = {

	onload: function(listview){

		listview.page.add_inner_button(
			"Button Text",
			function(){

				// code to run when button is clicked goes here

			},
			null,		//This is a non-grouped button so don't supply a group
			"primary" 	//This sets the style of the top level button (default, primary, secondary, warning, success, danger, info)
		);
	}
});
```

## Custom Buttons on a form, in a dropdown list
Similar to the simple custom button, it is also possible to add several buttons into a group/dropdown button. This is done by providing a third argument to the *add_custom_button* function specifiying a group name. Multiple calls to the *add_custom_button* function that supply the same group name will all be added into the same group.

![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Custom%20Button%20Dropdown.png?raw=true "Custom Dropdown Button Group")

To add the button to a form use the following template inside the *[Doctype name].js* file:
```Javascript
frappe.ui.form.on('doctype name', {
	refresh: function(frm) {

		frm.page.add_inner_button(
			"Button Text",
			function(){

				// code to run when button is clicked goes here

			},
			"Button Group Label"
		);
		
		frm.page.add_inner_button(
			"Button Text 2",
			function(){

				// code to run when button is clicked goes here

			},
			"Button Group Label"
		);
	}
});
```

To add a custom button on a list view, use the follwoing template in a *[Doctype name]_list.js* file:
```Javascript
frappe.listview_settings['Doctype name here'] = {

	onload: function(listview){

		listview.page.add_inner_button(
			"Button Text",
			function(){

				// code to run when button is clicked goes here

			},
			"Button Group Label"
		);
		
		listview.page.add_inner_button(
			"Button Text 2",
			function(){

				// code to run when button is clicked goes here

			},
			"Button Group Label"
		);
	}
});
```

## Menu Buttons in the regular menu list
The menu button appears on almost all pages and contains a number of common actions. Most of the time the menu actions do not change, so it is not recommended to alter this group very often. To add to this group dynamically you will most likely want to hook into the List View or standard form view events, however it should also be possible in other page views as will using a similar pattern.
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Menu%20Button%20-%20Not%20Standard.png?raw=true "Menu button in standard list")

To add a standard placement menu item into a form view, the follwoing template should be used inside the *[Doctype name].js* file
```Javascript
frappe.ui.form.on('doctype name', {
	refresh: function(frm) {

		frm.page.add_menu_item(
			"Button Text",
			function(){

			// code to run when button is clicked goes here

			},
			true //the button is part of the standard menu list
		);
	}
});
```

To add a standard placement menu item in a list view, the following template should be used in a *[Doctype name]_list.js* file.
```Javascript
frappe.listview_settings['Doctype name here'] = {

	onload: function(listview){

		listview.page.add_menu_item(
			"Button Text",
			function(){

				// code to run when button is clicked goes here

			},
			true //the button is part of the standard menu list
		);
	}
};
```

## Menu Buttons separated from the regular items
When adding items into standard menu items it may be preferable to ensure the new actions are separated out from the standard actions and placed in a visible location. This can be achieved by switching the third argument (a boolean) of the *add_menu_item* function to to indicate the button is a non standard item. This places the item at the top of the menu and places a separator line between it and the standard menu items.
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Menu%20Button%20Standard.png?raw=true "Menu button in non standard list")

To add a stnadard placement menu item into a form view, the follwoing template should be used inside the *[Doctype name].js* file
```Javascript
frappe.ui.form.on('doctype name', {
	refresh: function(frm) {

		frm.page.add_menu_item(
			"Button Text",
			function(){

			// code to run when button is clicked goes here

			},
			false //the button is NOT standard
		);
	}
});
```

To add a non-standard placement menu item in a list view, the following template should be used in a *[Doctype name]_list.js* file.
```Javascript
frappe.listview_settings['Doctype name here'] = {

	onload: function(listview){

		listview.page.add_menu_item(
			"Button Text",
			function(){

				// code to run when button is clicked goes here

			},
			false //the button is NOT standard
		);
	}
};
```

## Action buttons in the actions list
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Actions%20Button%20-%20Standard.png?raw=true "Action Button in standard items")

To add a standard placement action menu item into a form view, the follwoing template should be used inside the *[Doctype name].js* file:
```Javascript
frappe.ui.form.on('doctype name', {
	refresh: function(frm) {

		frm.page.add_actions_menu_item(
			"Button Text",
			function(){

			// code to run when button is clicked goes here

			},
			true //the button is part of the standard menu list
		);
	}
});
```

To add a standard placement action menu item in a list view, the following template should be used in a *[Doctype name]_list.js* file:
```Javascript
frappe.listview_settings['Doctype name here'] = {

	onload: function(listview){

		listview.page.add_actions_menu_item(
			"Button Text",
			function(){

				// code to run when button is clicked goes here

			},
			true //the button is part of the standard menu list
		);
	}
};
```

## Action buttons separated from the regular items
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Actions%20Button%20-%20Not%20Standard.png?raw=true "Action Button separated from standard items")

To add a standard placement action menu item into a form view, the follwoing template should be used inside the *[Doctype name].js* file:
```Javascript
frappe.ui.form.on('doctype name', {
	refresh: function(frm) {

		frm.page.add_actions_menu_item(
			"Button Text",
			function(){

			// code to run when button is clicked goes here

			},
			false //the button is NOT standard
		);
	}
});
```

To add a standard placement action menu item in a list view, the following template should be used in a *[Doctype name]_list.js* file:
```Javascript
frappe.listview_settings['Doctype name here'] = {

	onload: function(listview){

		listview.page.add_actions_menu_item(
			"Button Text",
			function(){

				// code to run when button is clicked goes here

			},
			false //the button is NOT standard
		);
	}
};
```

## Field based buttons
It is also possible to define buttons within the field designer. Simply create a field in the doctype definition and set it's type as button. The button will be created and placed on the form along with all of the other fields that you have defined. The final step is to then add a click handler for the button.

It should be noted that buttons defined in the doctype will not be permitted to be displayed in list views, and whilst it is possible to add them to quick enty forms, it is much more difficult to attach click handlers when the button is displayed in quick entry mode.

To add a button via a doctype definition, edit the Doctype from the DocType List. Then add a field definition and set its type to "Button"
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Doctype%20Button%20setup.png?raw=true "Setup of a doctype button")

This will allow for the display of a button directly within a form:
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Doctype%20Form%20Button.png?raw=true "Doctype field buttons")

To add a click handler to a button created in the doctype designer, the following template should be used inside the *[Doctype name].js* file
```Javascript
frappe.ui.form.on('Doctype name here', {
	button_field_name_from_doctype: function(frm) {
		//Code when button is executed here
	}
});
```

## Field based buttons - in quick entry
As noted previously, if you set up a button in a Doctype definition as available in quick entry mode, it is possible to display the button, but it will not have a click handler attached - even if you have defined a click handler for the main form view.
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Doctyp%20Form%20Button%20-%20Quick%20Entry.png?raw=true "Doctype field button in quick entry")

In order to handle clicks for quick entry the following would need to happen:
1. Add the button in the doctype definition and set the field as available in quick entry.
1. There will need to be:
	1. less than 7 fields defined as available for quick entry, or required
	1. the entire doctype must be marked to allow quick entry
1. You must override the "create new" buttons on all of the following views (the ones you intend to have available):
	1. list view
	1. form view
	1. kanban view
	1. activity view
	1. calendar view
	1. reports (?? Unsure about this one)
1. For each of the overridden actions you will need to call *frappe.new_doc(doctype, options, after_init_callback)
1. Within the after_init_callback you will need to set the click handler for the button.

This is an example of how to do so for a list view *[Doctype name]_list.js*:
```Javascript
frappe.listview_settings['Doctype name here'] = {
	
	//override the primary action
	primary_action: function(listview){

		//Options to pre-fill in the new doc
		const opts = {};

		frappe.new_doc(
			cur_list.doctype,
			opts,
			init_callback
		);

		function init_callback(dialog){
			
			//set up the action for the button 
			//(replace "testbutton" with the name of your defined button fieldname.)
			dialog.fields_dict.testbutton.df.click = function(){
				frappe.msgprint("button hooked up");
			};
		}
	}
};
```
