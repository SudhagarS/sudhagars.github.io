---
layout: post
title:  "Twitter/Instagram Like Date Format In Angular"
date:   2016-07-25 16:00:00 +0530
categories: javascript angular
tags: javascript angular
excerpt: Writing a custom directive in angular to show date in time-ago format, e.g. '10m', '3h'.
---

I started learning "proper" angular recently. I love it so far. 

I was building a little job posting app. When displaying jobs and comments, I had to show the time since it was posted. Doing it in a controller or a service doesn't make sense as this is something you need throughout your app. 

"Ah, what a oppurtunity to create a custom directive", I thought.

{% highlight javascript %}
'use strict';

angular.module('moduleName')
  .directive('timeAgo', function () {
    return {
      restrict: 'A',
      timeElapsed: function(time) {
        var elapsed = new Date().getTime() - new Date(time).getTime();
        var msPerMinute = 60 * 1000;
        var msPerHour = msPerMinute * 60;
        var msPerDay = msPerHour * 24;
        var msPerMonth = msPerDay * 30;
        var msPerYear = msPerDay * 365;

        if (elapsed < msPerMinute) {
          return Math.round(elapsed / 1000) + 's';
        } else if (elapsed < msPerHour) {
          return Math.round(elapsed / msPerMinute) + 'm';
        } else if (elapsed < msPerDay) {
          return Math.round(elapsed / msPerHour) + 'h';
        } else if (elapsed < msPerMonth) {
          return Math.round(elapsed / msPerDay) + 'd';
        } else if (elapsed < msPerYear) {
          return Math.round(elapsed / msPerMonth) + 'M';
        } else {
          return Math.round(elapsed / msPerYear) + 'Y';
        }
      },
      link: function postLink(scope, element, attrs) {
        element.text('' + this.timeElapsed(attrs.date));
      }
    };
  });
{% endhighlight %}

Now in your views, you just have to write the below html and it will show the date in time-ago format.

{% highlight html %}
  {% raw %}
    <span date-readable date="{{obj.created_at}}"></span>
  {% endraw %}
{% endhighlight %}

I don't know if this is the best solution for this. But I will keep udpating here as I discover more ways to improve it.
