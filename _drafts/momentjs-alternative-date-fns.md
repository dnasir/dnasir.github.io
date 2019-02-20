---
title: "Experimenting with MomentJS alternative date-fns"
description: "My thoughts about date-fns, an alternative JavaScript library to MomentJS for handling time."
tags:
  - js
  - javascript
  - momentjs
  - moment
  - date-fns
  - intl api
---

MomentJS is awesome. It's simple to use, and it does what I need it to do.

But every now and then, I come across alternative libraries that try to solve the problems plaguing current mainstream libraries - or so they claim.

## Why use date-fns?

### Modularity

It's no secret for those of us who work with international projects, that dealing with timezones is a huge pain in the backside, and the `moment-timezone` package helps alleviate most of the issues I have when dealing with this issue.

DST

### Intl API support

The Intl API is the next big thing in browser tech.

### Immutability

One of the problem I've encountered when dealing with Moment objects is the fact that it is mutable. Take the following example:

```javascript
import moment from 'moment';

const now = moment();
console.log(`Between ${now.format('HH:mm')} and ${now.addMinutes(30).format('HH:mm')}`);
```

As the more experienced of you would probably have noticed, the value of `now` has now been modified. To avoid this issue, you would have to use the `.clone()` method to create a clone of the original Moment instance.

This is how you would do it with date-fns:

```javascript
import format from 'date-fns/format';
import addMinutes from 'date-fns/addMinutes';

const now = new Date();
console.log(`Between ${format(now, 'HH:mm')} and ${format(addMinutes(now, 30), 'HH:mm')}`);
```

Since date-fns functions are pure functions, they do not mutate the input object.

### Build size

explain experiment

### Native Date object

I've rarely come across a plugin that accepts a Moment object as an input parameter.

have to convert to Date, and reconvert plugin output to Moment 

## Bottom line

date-fns is definitely something to keep an eye on.

Currently lacking official timezone support, but you can use `date-fns-timezone` for v1.x, and `date-fns-tz` for v2.x (currently alpha).

Localization is also another field where date-fns still needs work on.
