---
layout: post
title:  "Moment Timezone in five minutes"
date:   2020-01-26 11:23:59 -0600
categories: [momentjs, javascript, learning]
description: "The bare minimum to know about the moment-timezone library."
---
## Moment.js and Moment Timezone

[Moment.js](momentjs.com) is a JavaScript library for handling dates and times.
It is far more flexible and sophisticated than the built-in Javascript [Date]
object.

[Moment Timezone](https://momentjs.com/timezone/) is a version of the Moment.js
library that gracefully handles time zone localization and conversions.

Using Moment Timezone, you can create a date localized to one time zone and
convert it to a date localized to a different time zone. For example, you can
find out what the current time is in Berlin, and how it differs from the current
time in Chicago.

## Installation as a Node package

To use as a Node package, go to the directory containing your project and type:
```
npm install moment-timezone
```
Once installed, you can use it in your Node code files by requiring it:
```
const moment = require('moment-timezone');
```

## Basic usage

To create a Moment object for the current time, simply call its constructor with
no arguments:
```
const now = moment();
```
This will give you a Moment object created in your local time zone (determined
from your system settings).

To create the current time for a specific time zone, specify it using a [valid
time zone
identifier](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones):
```
const nowInBerlin = moment().tz('Europe/Berlin');
```
To get a string version of a Moment object, call `format`:
```
// 2020-01-26T15:00:00+0100
const nowInBerlinString = moment().tz('Europe/Berlin').format();

// 2020-01-26T08:00:00+0600 (assuming machine set to Chicago time)
const nowInChicagoString = moment().format();

// 2020-01-26T14:00:00Z (Universal Time Coordinated or UTC)
const nowUtcString = moment().utc().format();
```
(As the comments above indicate, Berlin is 1 hour ahead of UTC,
and Chicago is 6 hours behind UTC.)

Note that `format` by default returns a string in [ISO 8601
format](https://en.wikipedia.org/wiki/ISO_8601). You can specify other formats
by passing [format strings](https://momentjs.com/docs/#/displaying/format/) into
`format`:
```
// Jan 26th 2020 2 PM (Universal Time Coordinated)
const nowUtcString = moment().utc().format('MMM Do YYYY hA');
const nowUtcIso8601String = moment().utc().format(moment.defaultFormat);
```
A side note worth keeping in mind is that Moment also has a `toISOString`
method, which by default returns an ISO 8601 string set to UTC. If you pass
`true` into `toISOString`, whatever time zone the Moment object is set to is
retained when creating the string.

## Parsing dates

To parse a date, pass the date string into the Moment constructor:
```
// January 26, 2020 at 3 PM in Berlin
const parsed = moment('2020-01-26T15:00:00+0100');
```
If you want to convert the time zone for a date string:
```
// Convert a time in Berlin to the corresponding time in Chicago
const parsedInChicago = 
  moment('2020-01-26T15:00:00+0100')
    .tz('America/Chicago');
// 2020-01-26T08:00:00+0600
const chicagoString = parsedInChicago.format();
```
Moment has two modes for parsing dates: _strict_ and _forgiving_. By default,
Moment is forgiving. This means you can do this:
```
const wrongDate = moment('01/12/2016', 'YYYY-MM-DD')
console.log(wrongDate.isValid()) // true
// 2001-12-20T00:00:00-06:00
console.log(wrongDate.format())
```
Do not be forgiving. _Always_ be strict:
```
// true == be strict!
const wrongDate = moment('01/12/2016', 'YYYY-MM-DD', true)
console.log(wrongDate.isValid()) // false
```

## Verifying that a date string has a specific time zone
The simplest way to do this is to use Moment's `parseZone` function:
```
const parsedWithZone = moment.parseZone('2001-12-20T00:00:00-07:00');
// -420 (60 minutes * -7 hours)
const offsetMinutes = parsedWithZone.utcOffset()
```
So if you want to know if a date string is localized to a particular time zone,
don't use a regular expression, just let Moment figure it out for you, by using
its `parseZone` and `utcOffset` functions.
