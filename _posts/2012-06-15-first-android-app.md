---
layout: post
title: First Android App
---
I've finished a beta of my first Android app for internal use, a front office system for the Cafe owned by our company. It has 6 thousands lines of code, I've spent 170 hours for immidiate development of it.

{% capture imagesdir %}{{ site.baseurl }}/assets/posts/2012-06-15-first-android-app{% endcapture %}

## App Features

So, what the app can do?

It can load a list of goods through FTP or 1C web service. It consumes the following data: name of the item, its group, price and a weight goods tag.

The app displays the list of goods divided by their groups for easier look up for the needed item. The lists shows the name and price for each item.

![Goods (blue border) and groups (white border)]({{ imagesdir }}/first-android-app-1.jpg)

The main feature of the app is to compose an order with both piece-goods and weight goods. For piece-goods the operator simply enters its number by means of the + and - buttons. For weight items, they need to enter the weight with the virtual keyboard. As the items are added and deleted fromt the order, the total price without discounts is displayed.

![Order with three items]({{ imagesdir }}/first-android-app-2-order.jpg)

![Request for the item's weight]({{ imagesdir }}/first-android-app-3-weight.jpg)

 The app can also deal with the clients' cards, applying discounts accrodingly. To apply a card to the order, operator enters its number in a special popup window.

![Card number request]({{ imagesdir }}/first-android-app-4-card-number.jpg)

![Payment window when the card balance is zero]({{ imagesdir }}/first-android-app-5-payment.jpg)

 When a card number is entered the client has an option to pay for the order partly with their bonuses. The maximum bonus payment is calculated as a constant percentage of the order value. There's also an option to use a bonus rate other than 1 to 1.

![Payment window with some positive card balance]({{ imagesdir }}/first-android-app-6-payment-plus.jpg)

 If the client decided to pay for the order with bonuses, the app immediately withdraws the required amount from the card balance in the central database through the web service provided by 1C system. The detailed information about the sale is written to another central 1C based database.

![Links of the app with other software of the company]({{ imagesdir }}/first-android-app-7-links.png)

 The app has some settings:

 * Goods information transport: FTP or web service.
 * FTP server params.
 * Web servers URLs: for goods import and bonuses info exchange.
 * Clients cards default discount rate.
 * Current department code.
 * Maximum percantage of the order value for calculation of bonus payment limits.
 * Bonus payments rate.

## Technical details

There were actually no obstacles we couldn't solve in little time. I think, there're only two points worth mentioning:

1. When working with an SQLite databes in Android you should you [only one OpenHelper](http://stackoverflow.com/questions/2244965/having-several-sqliteopenhelper-in-one-appli-android). I realised this when already implemented two helpers used in different parts of the app. Fixed it up in 4 hours. Not too much, but would better not make such pitty mistakes.
2. I've spent a lot of time making the app work with the 1C web service together. In fact the [ksoap2-android](http://code.google.com/p/ksoap2-android/) library getting with all this stuff very well until we deal with some complex data structures such as an array of order goods. I described a solution for dealing with complex data structures over SOAP on [habr](http://habrahabr.ru/post/145389/) (RU).

I used [Robolectric](http://pivotal.github.com/robolectric/) as a testing framework which turned out to be a really interesting and handy thing. Another great stuff that helps considerably lower number of lines of code is [Mockito](https://code.google.com/p/mockito/). This is a dependency mocking framework. In an ideal world you'd probably also use [Robotium](http://code.google.com/p/robotium/) for the integration testing, but I didn't get enough time for using this tool in the project.

I used [FogBugz](http://www.fogcreek.com/fogbugz/) for task (and bugs) management. That's the best tool I've ever found that offers a great time tracking feature, esp if you're working in Eclipse. This IDE has a great plugin for the FogBugz service, and it gets all the headaches about time tracking from you. Just tell it what you're working on now, and it does the rest of the job.

## Conclusion

The first snap is ready. It's time for the first "field tests" in the Cafe. That's by no means a less interesting and exciting stage than the development itself. Hope everything went smoothly :)

Update 01.04.2013. The app is working. It's not perfect, but working :) I decided to open the sources since the app is now not developing, and the very sources doesn't have any world shaking code: [app sources](https://bitbucket.org/projectsarchive/cafe-android) and [tests sources](https://bitbucket.org/projectsarchive/cafe-android-tests).