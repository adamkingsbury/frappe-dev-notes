# Creating buttons in Frappe

There are a number of different location where you can create buttons or actions within frappe:

## Custom Buttons on a form
This is one of the simplest options available. Adding custom buttons on a form view can be done by adding code into the *[Doctype name].js* file. Usually this will be done in the onload event, but could also be triggered on the refresh or another event if the button should only appear under certain circumstances.

![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Custom%20Button.png?raw=true "Custom button on a form")

To add the button use the following template:
```Javascript
frappe.ui.form.on('doctype name', {
	refresh: function(frm) {

		frm.add_custom_button(
			"Button label text",
			function(){

			//Code when button is clicked goes here

			},
			__("Link Activivities from")
		);
	}
});
```

## Custom Buttons on a form, in a dropdown list
Similar to the simple custom button, it is also possible to add several buttons into a group/dropdown button. This is done by providing a third argument to the *add_custom_button* function specifiying a group name. Multiple calls to the *add_custom_button* function that supply the same group name will all be added into the same group.

![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Custom%20Button%20Dropdown.png?raw=true "Custom Dropdown Button Group")

To add the button use the following template:
```Javascript
frappe.ui.form.on('doctype name', {
	refresh: function(frm) {

		frm.add_custom_button(
			"Group button 1 text",
			function(){

				//Code when button is clicked goes here

			},
			__("The group lablel")
		);
		
		frm.add_custom_button(
			"Group Button 2 text",
			function(){

				//Code when button is clicked goes here

			},
			__("The group lablel")
		);
	}
});
```

## Menu Buttons in the regular menu list
The menu button appears on almost all pages and contains a number of common actions. Most of the time the menu actions do not change, so it is not recommended to alter this group very often. To add to this group dynamically you will most likely want to hook into the List View or standard form view events, however it should also be possible in other page views as will using a similar pattern.
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Menu%20Button%20-%20Not%20Standard.png?raw=true "Menu button in standard list")

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

To add a stnadard placement menu item into a form view, the follwoing template should be used inside the *[Doctype name].js* file
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


## Menu Buttons separated from the regular items
When adding items into standard menu items it may be preferable to ensure the new actions are separated out from the standard actions and placed in a visible location. This can be achieved by switching the third argument (a boolean) of the *add_menu_item* function to to indicate the button is a non standard item. This places the item at the top of the menu and places a separator line between it and the standard menu items.
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Menu%20Button%20Standard.png?raw=true "Menu button in non standard list")

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

## Action buttons in the actions list
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Actions%20Button%20-%20Standard.png?raw=true "Action Button in standard items")

## Action buttons separated from the regular items
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Actions%20Button%20-%20Not%20Standard.png?raw=true "Action Button separated from standard items")

## Field based buttons
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Doctype%20Form%20Button.png?raw=true "Doctype field buttons")

## Field based buttons - in quick entry
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Doctyp%20Form%20Button%20-%20Quick%20Entry.png?raw=true "Doctype field button in quick entry")
