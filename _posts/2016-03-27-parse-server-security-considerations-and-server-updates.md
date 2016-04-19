---
layout: post
title: Parse Server — Security Considerations and Server Updates
permalink: /2016/03/parse-server-security-considerations-and-server-updates/
---
When you take some lessons for beginners that show how to use Parse in your iOS projects,
there’s a growing feelings of the power and ease of BaaS and its week security.
Indeed, you control the database structure right from your client apps which have all the powers. Don’t you?

{% capture imagesdir %}{{ site.baseurl }}/assets/posts/2016-03-27-parse-server-security-considerations-and-server-updates{% endcapture %}

For example, [The Complete iOS 9 Developer Course on Udemy](https://www.udemy.com/the-complete-ios-9-developer-course/)
that I’m taking suggests to use Parse as a backend for iOS apps.
I used to build a custom Ruby on Rails backend for each app,
and the idea of BaaS looked very promising to me. Indeed, this very flexible solution
may get you quite far in much shorter time. Just like Ruby on Rails compared to any other
web frameworks. You just put all the logic into the client and the database is set up for you.
But that is the client who is marshalling the database structure,
and from what I read first it didn’t look very safe. Anyone can find the credentials
for accessing the server from the sources or, possibly, by intercepting the requests.
With the credentials comes absolute power over the server, at least if you followed the tutorials to the letter.

I doubted that the service was really that bad. So I did some research to understand what was going on.

At first, you are to initialise the Parse object with this code
([source](http://www.robpercival.co.uk/parse-server-on-heroku/), without the comment for masterKey):

So you specify the master key which involves that unrestricted power.
Very strange, like giving away your master SSH key to everyone.
As it turned out the master key shouldn’t be used in you app.
From the [Keys section of the official documentation](https://github.com/ParsePlatform/parse-server/wiki/Parse-Server-Guide#keys):

> Parse Server does not require the use of client-side keys.
This includes the client key, JavaScript key, .NET key, and REST API key.
The **Application ID is sufficient to secure your app**.

Okay, that does make sense than. Your server knows who is connecting to it, a master or a client.
But wait a minute, this knowledge gives us nothing as any client, even without the master key,
can create, edit and delete any entries and even create and alter classes. Let’s dive deeper.

We start by reading the [Parse Security section](https://parse.com/docs/ios/guide#security)
of its official documentation. Passing the subsection about using Master Key
which we have already covered, we come to the
[Class-Level Permissions](https://parse.com/docs/ios/guide#security-class-level-permissions) (CLP).
I looked up the described options at my [Parse Dashboard](https://github.com/ParsePlatform/parse-dashboard)
but didn’t find them. So the latter consideration, about altering classes,
is kind of answered, but just doesn’t work on my installation. Okay, let’s move on with the documentation for now.

The very next section,
[Object-Level Access Control](https://parse.com/docs/ios/guide#security-object-level-access-control),
gives an answer to the very basic concern of accessing objects. By default,
all the objects are only modifiable by their owner, that is the user who’s created them.
They are publicly readable, but that can be changed. In fact, Parse provides developers
with a very thorough granulated access control model for objects. You set the permissions
in code and there should be no problem with the security of your app’s data.

So I stuck with the CLP, which was described in the official documentation but wasn’t present in the dashboard.
At the time of my first acquaintance with the Parse Server the latest version was 2.2.0.
CLP editing feature was only added in
[version 2.2.1](https://github.com/ParsePlatform/parse-server/blob/master/CHANGELOG.md#221-3222016).
That’s why didn’t I see the option of restricting creation of classes by clients in my dashboard.
It did in fact present on a fresh install of Parse Server v2.2.2, the latest at the time of writing.

It wasn’t a big deal just to reinstall the server to get a new version on the development stage,
but that is usually a definite point to know how to update whatever you’re using in your work.
I tried to find information about updating a Parse Server deployed to Heroku but failed.

So how do you update your Parse Server deployed to Heroku? I deployed Parse with the
[deploy button](https://github.com/ParsePlatform/parse-server-example#with-the-heroku-button).
It sets up a web dyno and a MongoDB instance and clones the
[Parse Server Example](https://github.com/ParsePlatform/parse-server-example) repo to the dyno.
There’s a great answer on Stackoverflow about
[using Parse Server with Heroku](http://stackoverflow.com/a/35389397/758990).
I used items 15 through 18 to get the deployed code on my local machine.
When the code is in place, all you need to do is to update it with the latest version
from the Parse Server Example (replace all the files) and deploy back to Heroku.

I think there’s also an option to clone the Parse Server Example repository and enable
automatic deploys from GitHub (see [the tutorial](https://devcenter.heroku.com/articles/github-integration)).
So whenever the server updates all you have to do is to get those changes from the original repo
into your own and your server updates automatically. I haven’t done it myself yet though.

So, with a Parse Server version 2.2.1 or above you can see the option to manage Class-Level Permissions,
which is almost what makes the world perfect. You have control over access to individual objects
through code and over access to the classes from the dashboard. You can restrict reading/writing
of all objects of a class to a user or group of users, as well as restrict adding new fields to the class
by public users. Magnificent!

![]({{ imagesdir }}/parse-class-permissions-window.png)

But any client can still create new classes in your database. It’s convenient during development
but may provide you with a number of headaches at the production stage. You won’t loose the data,
but the database may well get messy should someone decide to play a joke on you.

The option of restricting creation of classes by clients is described in the documentation
but it’s nowhere in the Dashboard of the Parse Server. I found a submitted and closed
[issue](https://github.com/ParsePlatform/parse-server/issues/39) and the corresponding
merged [pull request](https://github.com/ParsePlatform/parse-server/pull/684).
So this option is there… somewhere. In fact, you have to add this option to
your server’s configuration in the `index.js` file of your server, the one you cloned from Heroku.

With this line, the server looks if the CLIENT_CLASS_CREATION config variable is defined
and disallows class creation by clients if it’s not. Having that option added, you need
to commit the changes back to Heroku:

    $ git add index.js
    $ git commit -m "Added ALLOW_CLIENT_CLASS_CREATION option"
    $ git push heroku master

Your clients won’t be able to create classes any more unless you specify the CLIENT_CLASS_CREATION variable:

![CLIENT_CLASS_CREATION variable in the Heroku console]({{ imagesdir }}/allowClientClassCreation_Parse_Heroku.png)

This [issue](https://github.com/ParsePlatform/parse-server/issues/749) discusses some problems related to this approach, but it worked for me very well.

As a conclusion, you can fully rely on the security system of Parse Server and just give it a try in your new, or existing, projects. Just remember the following points:

1.  Don’t write the master key in the source codes of the clients. They don’t need it.
2.  Use ACL in your code.
3.  Use Parse Server 2.2.1 or above to enjoy CLP.
4.  Modify the index.js file to use the Allow Client Class Creation option. Disable at the production stage.

I would also recommend you to have a look at a good blog articles series covering the security aspects of Parse (as a hosted solution) in depth – [Parse Security — Are You The Key Master](http://blog.parse.com/learn/engineering/parse-security-i-are-you-the-key-master/).