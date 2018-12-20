# Developing Dashboards

Dashboards appear at the top of a Doctype form when you view a record. There are a number of options available:

1. **Heatmaps**
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/dashboards/Screenshot%202018-12-18%20at%2017.12.32.png?raw=true "Heatmap Screenshot")

1. **Charts**
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/dashboards/Screenshot%202018-12-18%20at%2017.13.09.png?raw=true "Charting Screenshot")

1. **Transactions**
![alt text](https://github.com/adamkingsbury/frappe-dev-notes/blob/master/dashboards/Screenshot%202018-12-18%20at%2017.14.31.png?raw=true "Transactions Screenshot")

##Setup

To implement a dashboard, create a new file in the appropriate doctype folder called [doctype name]_dashboard.py

The file contents should look similar to the code below.

```Python
from frappe import _

def get_data():
	return {

#		Your return object for the dashboard and/or charts will go here.

  }
```

## get_data() return values

The get_data() function must return an object defining what deashboard elements you wish to display and the options
you wish to use for the content. The following are the available fields:

### For Heatmaps

* __heatmap__: boolean indicating if a heatmap should be displayed
* __heatmap_message__: A text string shown underneath the heatmap visualisation
* __method__: the dot notation function call to a whitelisted server side (python) function. If you do not provide this, the dashboard will default to the functionality provided by *frappe.desk.notifiactions.get_open_count* If you are not also using transactions views in the dashboard, then the function will simply look in *[Doctype Name].py* for a function called "*get_timeline_data*".

**Warning**: If you are using both transactions and heatmaps on the dashboard, then be aware that the method you use must handle both the counting of transactions that will be applied as badges AND also provide the heatmap dataset - particularly important if you are overriding the default method.

***Example get_data function***
```Python
from frappe import _

def get_data():
	return {
		'heatmap': True,
		'heatmap_message': _('This is based on the Time Sheets created against this project'),
		'fieldname': 'project',
	}
```

Within *[Doctype name].py* the *get_timeline_data* function must be applied. This function recieves a reference to the DocType and the name field for the current record. using these, the function must return a dictionary with the following structure:
 * __key__: unix timestamp date,
 * __value__: integer. the count of things that occurred on that date for the heatmap.
 
 Results should be limited to a range of one year back from the current date.
 
 ***Example get_timeline_data function inside [Doctype name].py
 ```Python
 from __future__ import unicode_literals
import frappe
from frappe.model.document import Document

class Doctype_Name_As_Class_Name(Document):
	def validate(self):
		pass

#note that the get timeline data function is not included in the doctype class itself
def get_timeline_data(doctype, name):
	'''Return timeline for attendance'''
	return dict(frappe.db.sql('''select unix_timestamp(`date`), count(*)
		from `tabStudent Attendance` where
			student=%s
			and `date` > date_sub(curdate(), interval 1 year)
			and status = 'Present'
			group by date''', name))
```


### For Charts

[Frappe.io's Chart Component Site](https://frappe.io/charts/docs)

There is not a lot of examples of using frappe's built in charting components, however the documentaion at the link above provides all of the information on how to structure the data for different types of charts and features. Essentially, the **graph_method** that is called must return a data object structured similar to the example below:

```Python
data = {
    labels: ["12am-3am", "3am-6pm", "6am-9am", "9am-12am",
        "12pm-3pm", "3pm-6pm", "6pm-9pm", "9am-12am"
    ],
    datasets: [
        {
            name: "Some Data", type: "bar",
            values: [25, 40, 30, 35, 8, 52, 17, -4]
        },
        {
            name: "Another Set", type: "line",
            values: [25, 50, -10, 15, 18, 32, 27, 14]
        }
    ]
}
```

The required fields in the *[Doctype Name]_dahshboard.py* file are:

* __graph__: Boolean. Set to true to display a chart
* __graph_method__: a dot notation string to the whitelisted python function that will return the required data for the chart
* __graph_method_args__: an object. A set of key value pairs relating to the arguments you need to pass when calling __graph_method__. 


***Example of charting setup***
```Python
from frappe import _

def get_data():
	return {
		'graph': True,
		'graph_method': "frappe.utils.goal.get_monthly_goal_graph_data",
		'graph_method_args': {
			'title': _('Sales'),
			'goal_value_field': 'monthly_sales_target',
			'goal_total_field': 'total_monthly_sales',
			'goal_history_field': 'sales_monthly_history',
			'goal_doctype': 'Sales Invoice',
			'goal_doctype_link': 'company',
			'goal_field': 'base_grand_total',
			'date_field': 'posting_date',
			'filter_str': 'docstatus = 1',
			'aggregation': 'sum'
		},
	}
```


### For Transaction Links and Notifications

* __method__: the dot notation function call to a whitelisted server side (python) function. If you do not provide this, the dashboard will default to the functionality provided by *frappe.desk.notifiactions.get_open_count* The default method counts the number of open documents and also looks at the function list provided in *[Doctype Name].py* for a function called "*get_timeline_data*". As long as you have defined a get_timeline_data function for each document type listed in your transactions list, these items will be added to the heatmap.
* __fieldname__: a fieldname from the current doctype that will be used as a key into other related doctypes
* __non_standard_fieldnames__: An object containing key value pairs. This is a map of where to find the matching fieldname(above)
in the related docs. Use this when the related doc stores the reference key in a differently named field. This often appears to 
be the case if the value is actually in a child table
  * __Key__: string. The related doctype name. This is the table where we will need to find the key in a different field 
  * __Value__: string. The fieldname in the related doctype that will match the fieldname in the current doctype
* __internal_links__: Slightly unclear on usage. Looks like it prevents values from appearing in heatmaps and potentially from appearing in summary data as well. An object containing key value pairs.
* __transactions__: an array of objects. Each object includes:
  * __label__: A grouping label that can be displayed on the summary area
  * __items__: The Doctypes included in the grouping

***Example configuration of [Doctype name]_dashboard.py***
```Python
from frappe import _

def get_data():
	return {
		'fieldname': 'project',
		'transactions': [
			{
				'label': _('Project'),
				'items': ['Task', 'Timesheet', 'Expense Claim', 'Issue' , 'Project Update']
			},
			{
				'label': _('Material'),
				'items': ['Material Request', 'BOM', 'Stock Entry']
			},
		]
	}
```

***A detailed example configuration of [Doctype name]_dashboard.py***
```Python
from frappe import _

def get_data():
	return {
		'fieldname': 'purchase_order',
		'non_standard_fieldnames': {
			'Journal Entry': 'reference_name',
			'Payment Entry': 'reference_name',
			'Auto Repeat': 'reference_document'
		},
		'internal_links': {
			'Material Request': ['items', 'material_request'],
			'Supplier Quotation': ['items', 'supplier_quotation'],
			'Project': ['items', 'project'],
		},
		'transactions': [
			{
				'label': _('Related'),
				'items': ['Purchase Receipt', 'Purchase Invoice']
			},
			{
				'label': _('Payment'),
				'items': ['Payment Entry', 'Journal Entry']
			},
			{
				'label': _('Reference'),
				'items': ['Material Request', 'Supplier Quotation', 'Project', 'Auto Repeat']
			},
			{
				'label': _('Sub-contracting'),
				'items': ['Stock Entry']
			},
		]
	}

```

#### Using the default method for transactions
The default method called when using the Transactions dashboard is *frappe.desk.notifiactions.get_open_count*

The signature for this method is: **get_open_count(doctype, name, items=[])**
* __doctype__: String. the Doctype name for the item being displayed on the dashboard
* __name__: String. The key for the item being displayed on the dashboard.
* __items__: Array of strings. A list of the related doctypes that are to be shown on the transactions view of the dashboard.

The default method iterates over the list of items and searches for records that are linked to the primary doctype and name. It then counts the number of those transactions that are considered to still be open and returns the summary of open items.

Note that the method does not pass in the following items defined in the *[Doctype name]_dashboard.py* file:
* the fieldname that links the primary document to the transactions document
* the value in the primary document to compare to the fieldname value in the related docs
* the list of internal link fields
* the map of non standard fieldnames

All of the above are needed to fully resolve the transactions counts. as such the method must also re-load *[Doctype name]_dashboard.py* in order to obtain the correct values. Additionally, the method must also check permissions to ensure the current user has the rights to view this information, and finally, the method is also overloaded to deal with obtaining the required heatmap data and merging this into its return dataset.

***frappe.desk.notifiactions.get_open_count implementation (with additional notes)***
```Python
@frappe.whitelist()
@frappe.read_only()
def get_open_count(doctype, name, items=[]):
	
	#Check user has permission to view this master document.
	frappe.has_permission(doc=frappe.get_doc(doctype, name), throw=True)
	
	#Load the metadata for the master doctype and use the provided function to
	#load any config found in the [Doctype name]_dashboard.py file
	meta = frappe.get_meta(doctype)
	links = meta.get_dashboard_data()

	# compile all items in a list - if the function was not called with a list of related items
	# fall back to the values in the dashboard def.
	if not items:
		for group in links.transactions:
			items.extend(group.get('items'))

	if not isinstance(items, list):
		items = json.loads(items)

	#iterate over each item (a related doctype)
	out = []
	for d in items:
	
		# don't count anything if the item(doctype) is included in the
		# [Doctype name]_dashboard.py internal links list
		if d in links.get('internal_links', {}):
			# internal link
			continue

		#get any filters that need to be applied based on settings found in frappe hooks
		filters = get_filters_for(d)
		
		# if the current item(doctype) is in the nonstandard fields list, use the non-standard field name
		# otherwise use the default fieldname
		fieldname = links.get('non_standard_fieldnames', {}).get(d, links.fieldname)
		
		data = {'name': d}
		if filters:
			# get the fieldname for the current document
			# we only need open documents related to the current document
			filters[fieldname] = name
			total = len(frappe.get_all(d, fields='name',
				filters=filters, limit=100, distinct=True, ignore_ifnull=True))
			data['open_count'] = total

		total = len(frappe.get_all(d, fields='name',
			filters={fieldname: name}, limit=100, distinct=True, ignore_ifnull=True))
		data['count'] = total
		out.append(data)

	out = {
		'count': out,
	}

	# This function is overlaoded. also go and get any heatmap timeline data if required and 
	# append it to the return.
	module = frappe.get_meta_module(doctype)
	if hasattr(module, 'get_timeline_data'):
		out['timeline_data'] = module.get_timeline_data(doctype, name)

	return out
```


So the final structure of the returned data may look like:
```Python
{
	'count': [
		{
			'name': 'Project',
			'count': 2,
		},
		{
			'name': 'Activity',
			'count': 40,
		},
		{
			'name': 'Resource',
			'count': 2,
		},
	],
	'timeline_data': "" #this dataset is outside the scope of the current analysis
}
```
