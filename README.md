# SeekerDebugger

Seeker Prototype Queryable Time-Traveling Debugger.

*(Beware that the SeekerInstallHelpers class>>install call will overwrite parts of the StDebugger's code of your image)*

Do this:
```Smalltalk
Metacello new
    baseline: 'Seeker';
    repository: 'github://maxwills/SeekerDebugger:main';
    load.
    
#SeekerInstallHelpers asClass install.
```

The `SeekerInstallHelpers>>#install` will:
- Enable the debugger extension in the StDebugger UI.
- Change the `StDebugger` `debuggerActionModel` default class to `SeekerStDebuggerActionModel`. This will make the StDebugger to need Seeker to be enabled to work. (You can still debug normaly without Seeker, but it will be shown at the right, even if it is not used). Don't use this if you rely on your own modifications of `StDebuggerActionModel`. If you unload Seeker, you will need to manually restore `StDebugger>>#debuggerActionModel`.


## Limitations
- Non-local changes NOT supported (i.e. only supports assignments for temporary (and parameters?) variables.  
- Currently, supports only test cases.
  - It doesn't support tests tear down phase at the moment.
- Performance: Executing code with Seeker is slow. Consider closing by force if necessary.

## Quick reference:
The Quick Reference pdf document is included in the repository, and can be accessed [here](./Resources/TTQs-QuickReference.pdf).

## UI Integrated Query Example

To show query results in the UI (the Query Tab of Seeker), follow this example.

1. Go to the Scripting Tab, as shown here. 
<img src="./Resources/scripting.png" width="700px">  


2. Paste and execute the following code.
```Smalltalk
|query|
"Lists the variable name for all the assignments of an execution."
query := Query from: seeker programStates
 select: [:state| state node isAssignment] 
 collect: [:state| New with: {
		(#bytecodeIndex -> state bytecodeIndex).
		(#varName -> state node variable variable name).
		}]
.
seeker ui showResult: query asSeekerResultsCollection  
```
3. See the results in the Query Tab. Click on any bytecodeIndex to time-travel to the result.

### Time-Traveling Queries Notes:

- The Query object instantiation doesn't trigger the production of results.
- The method asSeekerResultsCollection triggers the query evaluation, and additionally produces a Seeker UI-friendly object containing the resulting collection.
- The field bytecodeIndex is mandatory.
- `New with:` message instantiates a dictionary-like object. Collect your results attributes like that for displaying them in the UI, as in the example.
