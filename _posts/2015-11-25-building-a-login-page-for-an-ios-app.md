---
layout: post
title: Building a Login Page for an iOS App
permalink: /2015/11/building-a-login-page-for-an-ios-app/
---
When it came to building an app with a login screen I got a bunch of questions. And I found some answers to them scattered all over the Internet. It might seem an obvious task, but I really wanted to find a guide with some clarifications. I wasn't lucky enough to do that so I decided to describe my way of building a [first] login view myself.

{% capture imagesdir %}{{ site.baseurl }}/assets/posts/2015-11-25-building-a-login-page-for-an-ios-app{% endcapture %}

First of all, you can find the final sources for this post at [https://github.com/ssidelnikov/iOSLoginView](https://github.com/ssidelnikov/iOSLoginView). That is actually a part of the [pull request](https://github.com/rnystrom/HackerNewsReader/pull/4) to a great Hacker News Reader App by Ryan Nystorm. I would highly recommend you to check out the whole repository as there are a lot of great things there.

## Building UI

The first question I came across was how they build those great UI with two fields and a button in the right way. Something like this:

![Facebook login view on iOS]({{ imagesdir }}/facebook-login-ios.png){: .limit-image .image-center}

At first I thought it was a [UITableView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/) as a central part of the view. There were also an image or label for "Facebook" above it. And something in the footer. This [StackOverflow answer](http://stackoverflow.com/a/8962234/758990) pointed to the same solution as well. The UITableView has two static cells with input fields. But [that's impossible](http://stackoverflow.com/a/19110821/758990) if you place it into a  [UIViewController](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIViewController_Class/), not a  [UIViewTableViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITableViewController_Class/). My point was to build it completely in a xib file without any extra code or usage of [a storyboard and a container view](http://stackoverflow.com/a/25193002/758990).

So I looked further for another real-life examples and found a [LastFM iOS app repository](https://github.com/lastfm/lastfm-iphone). It used another approach to the problem. The developer put two images under the input fields as a background so they appeared to be a table view. Using some constraints, you could achieve nice results. This approach seems a bit hackery to me, but if LastFM uses it, that might be not that bad after all. In my case, the final view somehow like this:

![Resulting Login View For a Hackernews Reader App]({{ imagesdir }}/hacernews_login_view.png){: .limit-image .image-center}

## Networking

Another challenge in building a login feature in the iOS app is, well, login feature itself. That is how you send the request, store the session key and use it in future requests to the server. If you are working with an API, everything is fine. You send a request according to the documentation. Then you receive the response with the session key and store it to the database or user preferences. All the future requests contain that key in their headers. This seems easy and straightforward.

The project I was working on dealt with a website that offered no API for login or logout. You send a POST request to the server, and then receive a redirect response with a cookie called "user". All future requests should contain that cookie.

Working with cookies had always pictured to me as a brittle thing. But it turned out to be a quite easy and safe task on iOS. You basically make a request using  [NSURLSession](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/) with the [defaultSessionConfiguration](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSessionConfiguration_class/index.html#//apple_ref/occ/clm/NSURLSessionConfiguration/defaultSessionConfiguration). Some custom [delegate](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/#//apple_ref/occ/instp/NSURLSession/delegate) is also set for the session to prevent redirection. You shouldn't worry about the cookies in the sharedCookiesStorage. If their expiration date is set correctly, they [persist between app relaunches](http://stackoverflow.com/a/8713316/758990).
