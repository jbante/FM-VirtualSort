# Virtual Sort

Virtual Sort lets users sort records in FileMaker portals and found sets by any combination of fields they choose.

## Version History

1.0.0 - 2013-07-17 - Initial Release
1.1.0 - 2014-06-19 - Improved error reporting

## Attributions

This module is based on a technique by [John Ahn][1].

[1]: http://www.filemakerhacks.com/?p=5357 "FM 12 ExecuteSQL 'Unconference' Session"

## Installation

It is possible to install and use this module without custom functions, but I strongly recommend using them. Installing custom functions requires FileMaker Pro Advanced.

1.1. Import the "HyperList" and "Virtual Sort" script folders into your application file, preferably into your file's "Modules" folder.

1.2. (recommended) Import the custom functions to your application file if you have a copy of FileMaker Pro Advanced.

1.3. In each table you want to sort using Virtual Sort, create an unstored calculation field using the VirtualSortPosition ( primaryKey ; sortID ) function. If you do not have a copy of FileMaker Pro Advanced, you can instead use this calculation:

	Position (
		$$VirtualSort.SORT_ORDER[sortID] ;
		¶ & primaryKey & ¶ ;
		1 ;
		1
	)

1.4. Define a button for each field users should be able to sort on calling the script "Virtual Sort by SQL with New Field ( primaryKeyName ; sortFieldName { ; sortID } )". The script parameter should look something like this:

	"$primaryKeyName = " & Quote ( GetFieldName ( Table::id ) ) & " ;¶"	// from table occurrence being sorted
	& "¶$sortFieldName = " & Quote ( GetFieldName ( Table::field ) ) & " ;¶"
	& "¶$sortID = 123 ;¶"	// optional, defaults to 1

1.5. Use the VirtualSortFieldSorted ( field ; sortID ), VirtualSortFieldSortedAscending ( field ; sortID ), and VirtualSortFieldSortedDescending ( field ; sortID ) custom functions with conditional formatting calculations to create sort status indicators. If you do not have a copy of FileMaker Pro Advanced, you can instead use these calculations:

	// VirtualSortFieldSorted ( field ; sortID )
	Position (
		¶ & $$VirtualSort.ORDER_BY[sortID] ;
		¶ & GetFieldName ( field ) ;
		1 ;
		1
	) > 0

	// VirtualSortFieldSortedAscending ( field ; sortID )
	not IsEmpty (
		FilterValues (
			GetFieldName ( field ) ;
			$$VirtualSort.ORDER_BY[sortID]
		)
	)

	// VirtualSortFieldSortedDescending ( field ; sortID )
	not IsEmpty (
		FilterValues (
			GetFieldName ( field ) & "::DESC" ;
			$$VirtualSort.ORDER_BY[sortID]
		)
	)

### To sort portals:

2.1. In the portal definition, check the "Sort portal records" option and sort the portal by the unstored calculation field you created in step 1.3.

2.2. For the layout containing the sorted portal, create an OnRecordLoad script trigger that calls the "Refresh Virtual Sort by SQL ( primaryKeyName { ; orderBy ; sortID } )" script and passes it the same primaryKeyName and sortID values used in step 1.4. This is so the sort re-triggers for new sets of related data.

### To sort found sets:

3.1. For the layout records will be sorted on, create an OnModeEnter script trigger that fires at least when entering Browse Mode and sorts the found set by the unstored calculation field you created in step 1.3. Tripping this trigger is how virtual sort triggers a re-evaluation of the sort order.

## Options

Virtual Sort supports multiple simultaneous sorts, such as for sorting two portals on the same layout, or two found sets in separate windows. Different sorts can be addressed by the sortID parameter to the module's scripts and custom functions. It's up to you to devise a sortID numbering scheme.

For most purposes, the Virtual Sort by SQL scripts will provide ample sort flexibility. Applications needing more exotic sort orders can provide sorted primary key values to the "Set Virtual Sort Variables ( sortedValues ; tableName ; orderBy { ; sortID } )" script and trigger sort execution with the "Trigger Sort { portal }" script.

## License

Anyone may do anything with this software for any purpose. There is no warranty.