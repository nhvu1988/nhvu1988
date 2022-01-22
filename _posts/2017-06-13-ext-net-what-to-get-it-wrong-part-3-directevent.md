---
title: Ext.NET - What to get it WRONG? - PART 3 - DirectEvent
author: nhvu1998
date: 2017-06-13 07:00:00 +0700
category: Coding
tags: [ext.net]
---

## DirectEvents

[DirectEvent](http://mvc.ext.net/#/Events/DirectEvents/) is an event will trigger a DirectEvent ajax request to the server. It could be triggered by `grid-column-command` event, `button-click` event or from `MessageBusDirectEvent`

DirectEvent often goes with 3 common properties `Url`, `Method`, and `ExtraParams`

~~~csharp
DirectEvent =
{
    Url = builder.UrlHelper.Action("Action", "Controller"),
    Method = HttpMethod.POST,
    ExtraParams =
    {
        new Parameter("key", "value", ParameterMode.Value)
    }
}
~~~

In Razor style

~~~csharp
Html.X().Button().DirectEvents(de =>
{
    de.Click.Url = Url.Action("Action", "Controller");
    de.Click.Method = HttpMethod.POST;
    de.Click.ExtraParams.Add(
        Html.X().Parameter()
            .Name("key")
            .Value("value")
            .Mode(ParameterMode.Value)
    );
})
~~~

`Ext.NET` will generate to javascript

~~~csharp
Ext.net.directRequest({
    url: "/Controller/Action",
    method: "POST",
    extraParams: {"key":"value"}
}
~~~

## DirectEvent Properties

I will try to list all common properties of DirectEvent that I have worked with

### Url

~~~csharp
Url = builder.UrlHelper.Action("Action", "Controller")
~~~

Url is the url of the ajax request, it's a `string` combined from **`controller`** name and **`action`** method

> It's recommended to use `UrlHelper.Action` to combine the Url

### Method

~~~csharp
Method = HttpMethod.POST
~~~

Method is HTTP method of the ajax request. It could be `HttpMethod.POST`, `HttpMethod.GET`, `HttpMethod.DELETE`…

### ExtraParams

~~~csharp
ExtraParams =
{
    new Parameter("key", "value", ParameterMode.Value)
}

// or
Html.X().Parameter()
    .Name("key")
    .Value("value")
    .Mode(ParameterMode.Value)
~~~

ExtraParams contains all the extra parameters that will be sent in the ajax request. Each extra parameter is a pair of key `name` and `value`, and

> The parameter **mode** will define `value` is a `hard` or `dynamic` value.

If the mode is `ParameterMode.Value`, Ext.NET will generate the `value` as `hard` value

~~~javascript
extraParams: {key: "value"} // value in double quote will be a hard-string value
~~~

and when the mode is `ParameterMode.Raw`, the `value` will be a `dynamic` value

~~~javascript
extraParams: {key: value} // value is a JS variable is available in run-time
                          // otherwise, "value is not defined" exception will throw
~~~

## Use `dynamic` value in DirectEvent

The biggest problem when working with DirectEvent is we often need to use a `dynamic` extra parameter. It means we use `ParameterMode.Raw` mode and **need to know**

> What **`variables`** available in run-time?

In the generation to JS part, DirectEvent always comes in a handler function that call `Ext.net.directRequest('...')` in the body.

For example, DirectEvent of button click

~~~csharp
Html.X().Button().DirectEvents(de => {
    de.Click.ExtraParams.Add(
        Html.X().Parameter()
    );
});
~~~

and will be generated with `item` and `e` variables as below

~~~javascript
{
    buttons: [{
        directEvents: {
            click: {
                fn: function(item, e) {
                    Ext.net.directRequest({ extraParams: {} });
                }
            }
        }
    }]
}
~~~

So base on every single type of the component DirectEvent is binding, it will be bounded with the difference variables.

### DirectEvent in Button

Continue to the previous sample

* `item` is the ExtJS button component
* `e` is an [onclick object event](https://www.w3schools.com/jsref/dom_obj_event.asp).

In this case, we can get `name` of button in `item` by using `item.name` in extra params like this

~~~csharp
Html.X().Parameter()
    .Name("buttonName") // define "buttonName" as key
    .Value("item.name")
    .Mode(ParameterMode.Raw) // use raw mode to pass dynamic value
~~~

and it generates to

~~~javascript
fn: function(item, e){
    Ext.net.directRequest({
        extraParams: {"buttonName": item.name}
    });
}
~~~

### DirectEvent in Grid Command

~~~csharp
X.GridPanel()
    .ColumnModel(
        X.ImageCommandColumn()
            .Commands(X.ImageCommand().CommandName("Submit"))
            .DirectEvents(de => {...})
    );
~~~

It's just the same with Button but the difference **`variables`**

~~~javascript
fn: function (item, command, record, recordIndex, cellIndex) {
    Ext.net.directRequest({...});
}
~~~

* `item` is the command component, here is the `ImageCommand` named "Submit".
* `command` is name of command calling, "Submit" is the command in this case.
* `record` is the [store record which maps to the referenced cell](http://docs.sencha.com/extjs/6.0.2/classic/Ext.grid.CellContext.html#property-record). We can access to data using `record.data`.
* `recordIndex` is the index of store record which maps to the referenced cell.
* `cellIndex` is the index of referenced cell.