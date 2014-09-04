---
layout: post
title: "Working around Google Maps API changes"
date: 2014-09-04 15:54:50 -0400
comments: true
categories: [Javascript, Google&nbsp;Maps&nbsp;API&nbsp;V3, RTFM]
---

A few days before the Flatiron School Science Fair, where we were to demo what we've built to potential employers, a classmate noticed the Google Maps aspect of their app was hopelessly broken.  And then someone else's app had broken.  And then mine had as well.

### geometry.location.K refers to latitude, naturally!

In my first experiences with the Maps API, I relied on the structure of the JSON response from the API instead of reading the documentation like I should have.  From looking into the response, the variable that contained a coordinate's longitude seemed to be named `K`, and the latitude variable was named `a`.  I thought this was weird, but was in a rush to get Maps working, so I just wrote something like this:

```javascript
var latitude = results[0].geometry.location.a;
```

And that worked, until it didn't.  The bug that crashed every Flatiron app that used Google Maps boiled down to this: the reponse now referred to longitude as `b`, instead of `a`.  What the hell?  I was too busy at the time to think about why that should happen, so just changed my code to use `b` instead.

Fortunately, that fix held through the Science Fair, but broke again a few days later!  After some discussion with classmates, we found a [StackOverflow question](http://stackoverflow.com/questions/13499111/google-maps-geocoder-geocode-geometry-location-lat-lng-property-names-change-f) with a near-identical issue.  The solution was to use the `latitude()` and `longitude()` methods of the Google Maps `LatLng` object, instead of the arbitrarily-named variables `a`, `b`, `K`, etc. which appear to change frequently and without warning.

After looking back over the docs for the [Maps API](https://developers.google.com/maps/documentation/javascript/reference#LatLng), it turns out those methods were both documented.  So my classmates and I could have avoided the whole issue by following that classic four-letter piece of advice: RTFM.