# Createing buttons in Frappe

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
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Custom%20Button%20Dropdown.png?raw=true "Custom Dropdown Button Group")

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

## Menu Buttons in the regular menu list
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Menu%20Button%20-%20Not%20Standard.png?raw=true "Menu button in standard list")

## Menu Buttons separated from the regular items
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Menu%20Button%20Standard.png?raw=true "Menu button in non standard list")

## Action buttons in the actions list
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Actions%20Button%20-%20Standard.png?raw=true "Action Button in standard items")

## Action buttons separated from the regular items
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Actions%20Button%20-%20Not%20Standard.png?raw=true "Action Button separated from standard items")

## Field based buttons
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Doctype%20Form%20Button.png?raw=true "Doctype field buttons")

## Field based buttons - in quick entry
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/buttons/Doctyp%20Form%20Button%20-%20Quick%20Entry.png?raw=true "Doctype field button in quick entry")
