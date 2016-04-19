---
layout: post
title: How to View All JavaScript Interval Functions of a Page
permalink: /2016/02/how-to-view-all-javascript-interval-functions-of-a-page/
---
While I was adding some functionality to a web site, I noticed that it reloads the page regularly by itself. Apparently, there was some [timeout set](http://www.w3schools.com/jsref/met_win_settimeout.asp) for a function that caused this behaviour. But how is that possible to find what that function is?

First of all, I googled for how to show all JavaScript timeouts (intervals, timers) set on page. I expected to find something simple, preferably integrated right into the developer console of Safari (or any other browser). But I found little information, just this answer on StackOverflow – [http://stackoverflow.com/a/858638/758990](http://stackoverflow.com/a/858638/758990). But it pointed me in the right direction: there’s no such functionality built into browsers. You have to override the `setTimeout` and `clearTimeout` functions to keep track of the active timers. I altered the code from the answer so that it truly keeps the list of timers, not only their counts. It’s here:

```javascript
window.originalSetTimeout=window.setTimeout;
window.originalClearTimeout=window.clearTimeout;
window.activeTimers=[];
window.setTimeout=function(func,delay)
{
  var id = window.originalSetTimeout(func,delay);
  window.activeTimers[id] = {"func": func, "delay": delay};
  return id;
};
window.clearTimeout=function(timerID)
{
  delete window.activeTimers[timerID];
  window.originalClearTimeout(timerID);
};
```

I would strongly recommend against using it in production because I feel it could slow page execution significantly. I haven’t checked that, though.

So with that snippet of JavaScript code injected into your page, the top of it, you have the `activeTimers` window’s variable which is an array.
In my case, it had 441200 elements. Most of them had the `delay` under 500 which filtered it out for me –
I was looking for something less frequent at first. So I headed to the browser console and typed this command:

```javascript
window.activeTimers.filter(function(val) {return val.delay > 500})
```

It printed all the functions that were running not very often. There were 37 of them, the number you can easily deal with. Among them did I find the culprit of my problem. But that is another topic.

I hope this was a helpful information for you. Good debugging!