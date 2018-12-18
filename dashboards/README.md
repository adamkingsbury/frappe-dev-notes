#Developing Dashboards

Dashboards appear at the top of a Doctype form when you view a record. There are a number of options available:

1. Heatmaps
1. Charts

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
* __fieldname__: a fieldname from the current doctype that will be used as a key into other related doctypes
* __non_standard_fieldnames__: An object containing key value pairs. This is a map of where to find the matching fieldname(above)
in the related docs. Use this when the related doc stores the reference key in a differently named field. This often appears to 
be the case if the value is actually in a child table
  * __Key__: string. The related doctype name. This is the table where we will need to find the key in a different field 
  * __Value__: string. The fieldname in the related doctype that will match the fieldname in the current doctype
* __internal_links__: An object containing key value pairs
  * __Key__: String. DocType name
  * __Value__: Array of strings. Each string represents
* __transactions__: an array of objects. Each object includes:
  * __label__: A grouping label that can be displayed on the 
