---
title: How to group on array field with angular-ui-grid
author: nhvu1998
date: 2017-05-02
category: Coding
tags: [javascript,angular,ui-grid]
---

![](/assets/img/posts/how-to-group-on-array-field-with-angular-ui-grid-2.png)

## The challenge

Last week, I got a task to add grouping on [angular-ui-grid](http://ui-grid.info) for the column is an array field. The data is

```javascript
var movies =  [
    { title: 'Underworld: Blood Wars',  categories: ['Action', 'Horror'] },
    { title: 'The Bye Bye Man',         categories: ['Horror'] },
    { title: 'Sleepless',               categories: ['Action', 'Thriller'] }
];

$scope.gridOptions = {
    treeRowHeaderAlwaysVisible: false,
    columnDefs: [
        { name: 'title', field: 'title' },
        { name: 'categories', field: 'categories' }
    ],
    data: movies
}
```

The `categories` is the field contains a list of categories movie is paired with. By default, `ui-grid` can't handle array field and generates it as `string`.

![](/assets/img/posts/how-to-group-on-array-field-with-angular-ui-grid-1.png)

The expected result is the `ui-grid` can group the movies by theirs categories one by one. It means category

>    * **Action** contains "Underworld: Blood Wars" and "Sleepless"
>    * **Horror** contains "Underworld: Blood Wars" and "The Bye Bye Man"
>    * **Thriller** only contains "Sleepless"

After googling, also asking question on [stackoverflow](http://stackoverflow.com/questions/43488142/grouping-by-array-field-in-angular-ui-grid) and [ui-grid Github repo](https://github.com/angular-ui/ui-grid/issues/6161) but I can't see any solution available, so I decide to read ui-grid's [document](http://ui-grid.info/docs/#/tutorial/209_grouping) and try to build a solution.


## Solution

### Flatten array field to text field

I try to flatten `movies` to a `cloneMovies` object has field `category` as string, that will have duplicate records in `title`. 

```javascript
var cloneMovies = [];

// flatten movies to cloneMovies
for (var i=0; i<movies.length; i++) {
    var movie = movies[i];
    for (var j=0; j<movie.categories.length; j++) {
        var category = movie.categories[j];
        
        cloneMovies.push({
            title: movie.title,
            category: category
        });
    }
}

/**
 *   cloneMovies =  [
 *       { title: 'Underworld: Blood Wars', category: 'Action' },
 *       { title: 'Underworld: Blood Wars', category: 'Horror' },
 *       { title: 'The Bye Bye Man', category: 'Horror' },
 *       { title: 'Sleepless', category: 'Action' }
 *       { title: 'Sleepless', category: 'Thriller' }
 *   ];
 */
```

### Add hidden field to group flattened text field

In this example, I add a **hidden** `category` column to gridOptions's `columnDefs`. 

```javascript
columnDefs: [
    { name: 'title', field: 'title' },
    { name: 'categories', field: 'categories' },
    // Adding hidden "category" column
    { name: 'category', field: 'category', visible: false }
],
```

This new column is used for displaying the first grouping column when we do grouping on `categories` field. In another word, **this column only shows when the grid is grouped by `categories`**.

### Detect grouping event on array field and forward it to text field

Thanks to groupingChanged event of [grouping PublicAPI](http://ui-grid.info/docs/#/api/ui.grid.grouping.api:PublicApi), we can listen whenever the `categories` is grouped on, by clicking on column header or calling through [groupColumn API](http://ui-grid.info/docs/#/api/ui.grid.grouping.service:uiGridGroupingService). I will update the grouping column to `category` 

```javascript
$scope.gridApi.grouping.on.groupingChanged($scope, function(col) {

    // detect grouping on 'categories' column event called
    // (by clicking on column header or calling through groupColumn API)
    // we will change the grouping column to 'category'
    if (col.field === 'categories' && col.grouping.groupPriority !== undefined) {
        $scope.gridApi.grouping.ungroupColumn('categories');
        $scope.gridApi.grouping.groupColumn('category');
        return;
    }
});
```

### Show/hide nessesary column and update the correct data

Now, the grid can group on the new `category` field, but I have to do a little trick to make the switching between 2 columns transparency. 

```javascript
$scope.gridApi.grouping.on.groupingChanged($scope, function(col) {

    //  nothing to do unless grouping field is 'category'
    if (col.field !== 'category') return;
    
    if (col.grouping.groupPriority !== undefined) {
        // grouping by category is turning on 
        $scope.gridOptions.columnDefs[1].visible = false; // hide column categories
        $scope.gridOptions.columnDefs[2].visible = true; // show column category
        $scope.gridOptions.data = cloneMovies; // update data to use clone object
    } 
    else {
        // grouping by category is turning off
        $scope.gridOptions.columnDefs[1].visible = true; // show column categories
        $scope.gridOptions.columnDefs[2].visible = false; // hide column category
        $scope.gridOptions.data = movies; // update data to use original object
    }
});
```

Finally, it works like a charm :D. Please check out my [demo on jsfiddle](https://jsfiddle.net/nhvu1988/jhr76n24/) to see how it works.

<iframe width="100%" height="250" src="https://jsfiddle.net/nhvu1988/jhr76n24/embedded/result,html/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>
