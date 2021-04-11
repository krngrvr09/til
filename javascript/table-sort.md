```javascript
const getCellValue = (tr, idx) => tr.children[idx].innerText || tr.children[idx].textContent;

const comparer = (idx, asc) => (a, b) => ((v1, v2) =>
    v1 !== '' && v2 !== '' && !isNaN(v1) && !isNaN(v2) ? v1 - v2 : v1.toString().localeCompare(v2)
    )(getCellValue(asc ? a : b, idx), getCellValue(asc ? b : a, idx));

// do the work...
document.querySelectorAll('th').forEach(th => th.addEventListener('click', (() => {
    const table = th.closest('table');
    Array.from(table.querySelectorAll('tr:nth-child(n+2)'))
        .sort(comparer(Array.from(th.parentNode.children).indexOf(th), this.asc = !this.asc))
        .forEach(tr => table.appendChild(tr) );
})));

```



Let's unpack this step by step:

```javascript
const getCellValue = (tr, idx) => tr.children[idx].innerText || tr.children[idx].textContent;
```

This can also be written as:

```javascript
const getCellValue(tr, idx){
  // tr: The row. While sorting the rows, we iterate over all "tr"s while comparing value at a specific 
  // idx(index) of the row, using which we are sorting.
	return tr.children[idx].innerText || tr.children[idx].textContent;
}
```

getCellValue is a function that takes two arguments: `tr`(table row) and `idx`(id of the element?). Intuitively, it should return the value of a cell. So it checks whether a cell - `tr.children[idx]` has `innerText` or `textContent`. Major difference between `innerText` and `textContent` is that `innerText` shows visible content only, and therefore is more performance heavy. `textContent` returns everything as is. There are more differences, but not going into them here. Refer to more information [here](https://kellegous.com/j/2013/02/27/innertext-vs-textcontent/) and [here](https://stackoverflow.com/questions/35213147/difference-between-textcontent-vs-innertext).

```javascript
const comparer = (idx, asc) => (a, b) => ((v1, v2) =>
    v1 !== '' && v2 !== '' && !isNaN(v1) && !isNaN(v2) ? 
                                          v1 - v2 : 
                                          v1.toString().localeCompare(v2))(getCellValue(asc ? a : b, idx), getCellValue(asc ? b : a, idx));
```

This can also be written as:

```javascript
const comparer(idx, asc){
  function temp2(a,b){
    function temp(v1, v2){
    	if(v1 !== '' && v2 !== '' && !isNaN(v1) && !isNaN(v2)){
      	return v1-v2;
    	} 
    	else{
      	return v1.toString().localeCompare(v2)
    	}
  	}
  	return temp(getCellValue(asc ? idx : asc, idx), getCellValue(asc ? asc : idx, idx));
  }
}

```

So this is a comparer function. This says if I get two elements, `idx` and `asc`(whatever they mean?), then who will be taken first. I'll come back to this later.

```javascript
document.querySelectorAll('th').forEach(th => th.addEventListener('click', (() => {
    const table = th.closest('table');
    Array.from(table.querySelectorAll('tr:nth-child(n+2)'))
        .sort(comparer(Array.from(th.parentNode.children).indexOf(th), this.asc = !this.asc))
        .forEach(tr => table.appendChild(tr) );
})));
```

This can also be written as:

```javascript
document.querySelectorAll('th').forEach(
  // For each column, run this function
  (th) => th.addEventListener(
    'click', (
    	() => {
        		// What to do when <th> is clicked
        		// Find the closest ancestor
    				const table = th.closest('table');
    				// Make an array from 
        		Array.from(
              // Select all rows except the first one
        			table.querySelectorAll('tr:nth-child(n+2)')
      			).sort(
              // Sort them using this comparer function.
              // asc is a variable that tells, whether sorting is ascending or descending.
              // The first argument says which column do we want to sort by
              comparer(
        				Array.from(th.parentNode.children).indexOf(th), this.asc = !this.asc
      				)
              // After sorting, whatever array of rows we get, append it to the table.
   					).forEach(
              tr => table.appendChild(tr) 
						);
					}
  	)
	)
);
```

The only thing left here to understand is why am I doing `v1-v2` in the second code block.