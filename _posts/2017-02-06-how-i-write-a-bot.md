---
title: How I write a chatbot?
author: nhvu1998
date: 2017-02-06
category: Coding
tags: [nodejs,bot framework,luis]
---

It's just an internal chatbot for my company, it's available on skype/messenger/slack and will answer some questions like

* How many hours did I work `last year`?
* Get Vu's working hours `yesterday`?
* Get Vu's working hours on `1st November 2016`?
* Get my hours from `Jan - Mar 2016`
* Get my hours in `week 23, 2016`

![Bot Example](/assets/img/posts/how-i-write-a-bot-0.png)

Some technologies I learned and used for this project are

* [Bot Framework](https://dev.botframework.com/) to build and deploy chatbot
* [LUIS](https://www.luis.ai) to understand human language
* [Chrono](https://github.com/wanasit/chrono) a natural language date parser in Javascript

## Bot Framework and LUIS

After some researching, I decide to use Bot Framework to build my chatbot since it supports nodejs which's my favorite programming language.

I use LUIS to add natural language understanding to my bot. I compared it with [api.ai](https://api.ai/) and [wit.ai](https://wit.ai) and see it has a good design, friendly UI to define an intent, add new utterance, train and publish the application. They also have 2 [videos](https://www.luis.ai/Help) for me can easily get the overview of LUIS 😍.

### LUIS - builtin.datetime

Seem everything relates to coding with bot framework, training LUIS to understand intent is easy... until I have many types of Date Ranges need to be supported in my chatbot. For example, in 5 questions above, I need to parse date range texts such as `today`, `yesterday`, `last year`, `Jan - Mar 2016`, `Week 23, 2016`...

First of all, thanks to LUIS, it has a set of [pre-built entities](https://www.microsoft.com/cognitive-services/en-us/LUIS-api/documentation/Pre-builtEntities) to recognize date and time in [builtin.datetime](https://www.microsoft.com/cognitive-services/en-us/LUIS-api/documentation/Pre-builtEntities#builtindatetime). It will understand when I say `yesterday`, `1st November` or `last week`, in 3 first questions above, and will convert that to a date.

```javascript
// tomorrow
"resolution": { "date": "2016-11-20" }

// last quarter
"resolution": { "date": "XXXX-Q4" }

// last year
"resolution": { "date": "2015" }

// last two years
"resolution": { "duration": "P2Y" }

// last week
"resolution": { "date": "2016-W45" }

// past three weeks
"resolution": { "duration": "P3W" }

// this month
"resolution": { "date": "2016-11" }

// last ten months
"resolution": { "duration": "P10M" }
```

## Chrono

Since the LUIS with builtin datetime did good jobs on parsing Datetime, the next requirement is how I can parse a date ranges from a specific date (or month) to another specific date (or month)? For example in my fourth question, it's `Jan - Mar 2016`

Chrono supports most date and time formats, such as:

* Today, Tomorrow, Yesterday, Last Friday, etc
* 17 August 2016 - 19 August 2016
* January - March 2016
* 5 days ago

So I used it to try to recognize if LUIS couldn't give me any information about date ranges. Two things I would like to note is I had to use every values in the `knownValues` to parse a date and

> always parse date in UTC

since the date object on server can be in difference timezone

```javascript
var chrono = require('chrono-node');

var chronoParsedResults = chrono.parse(message);
if (chronoParsedResults && chronoParsedResults.length) {
    // we just want to get the first parsed value
    var firstResult = chronoParsedResults[0];
    console.log('firstResult start', firstResult.start);

    // we always can get start date from chrono
    var startValues = firstResult.start.knownValues;
    var startDate = createUTCDateFromKnownValues(startValues.year, startValues.month, startValues.day, true);

    //...
}

function createUTCDateFromKnownValues(year, month, day, isFrom) {
    // (year, null, null, ?)
    if (year && !month && !day)
        return isFrom ? new Date(Date.UTC(year, 0, 1)) : new Date(Date.UTC(year, 11, 31, 23, 59, 59));
    // (year, month, null, ?)
    if (year && month && !day) {
        if (isFrom) return new Date(Date.UTC(year, month - 1, 1));

        var firstNextMonth = new Date(Date.UTC(year, month, 1, 23, 59, 59));
        firstNextMonth.setDate(firstNextMonth.getDate() - 1);
        return firstNextMonth;
    }
    // (year, month, day, ?)
    if (year && month && day) {
        return isFrom ? new Date(Date.UTC(year, month - 1, day)) : new Date(Date.UTC(year, month - 1, day, 23, 59, 59));
    }
}
```

## Parsing Week Of Year

The last challenge is I would like to parse date ranges for a specific week in year, for example is `week 23, 2016`, `week 1` (default year is current year). But LUIS or chrono doesn't support it yet (hopefully it will support in future 😂). So I had to define an entity `WeekOfYear` to recognize

![Parsing Week Of Year](/assets/img/posts/how-i-write-a-bot-1.png)

and try to parse with [moment](https://momentjs.com/docs/)

```javascript
var weekOfYearEntity = builder.EntityRecognizer.findEntity(entities, 'WeekOfYear');
var weekOfYear = weekOfYearEntity ? weekOfYearEntity.entity : '';

// we check if week of year found on message
if (weekOfYear) {
    var definedWeekOfYearFormat = ['[week] W YYYY', '[week] W, YYYY'];
    var m = moment(weekOfYear, definedWeekOfYearFormat);
    //...
}
```
