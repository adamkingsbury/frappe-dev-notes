# Developing Dashboards

Dashboards appear at the top of a Doctype form when you view a record. There are a number of options available:

1. Heatmaps
1. Charts
1. Transactions

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
* __method__: the dot notation function call to a whitelisted server side (python) function. If you do not provide this, the dashboard will default to the functionality provided by *frappe.desk.notifiactions.get_open_count* The default method counts the number of open documents and also looks at the function list provided in *[Doctype Name].py* for a function called "*get_timeline_data*". As long as you have defined a get_timeline_data function for each document type listed in your transactions list, these items will be added to the heatmap.

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


### For Charts

[Frappe.io's Chart Component Site](https://frappe.io/charts/docs)

There is not a lot of examples of using frappe's built in charting components, however I believe that the charting component link above describes the available options. The *frappe.utils.goal.get_monthly_goal_graph_data* function is also provided out of the box and appears to do a lot of heavy lifting. This should be the go-to option before trying to craft a new custom charting method.

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
  
