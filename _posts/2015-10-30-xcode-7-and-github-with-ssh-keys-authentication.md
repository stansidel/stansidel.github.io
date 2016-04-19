---
layout: post
title: Xcode 7 and Github with SSH Keys authentication
permalink: /2015/10/xcode-7-and-github-with-ssh-keys-authentication/
---
I had been working a lot with PHPStorm and RubyMine when I came to Xcode this summer. All the JetBrains tools work with git remote hosts, Github and Bitbucket, quite well. But when I tried to access my repositories with the ssh key from Xcode I received an error: "Authentication failed. Username or password incorrect".

I made sure the password for the key was correct, and the very same key worked for ssh access to Github via console just fine. Github stated on the security tab (Settings - Security) that there were an error authenticating Xcode with the public_key.verification_failure code.

It worked with username and password just fine, but I like everything to be as it should be, not just as it could be. So I proceeded with the investigations. There's a [similar question on Stackoverflow](http://stackoverflow.com/questions/32833100/adding-a-github-repository-in-xcode-7-using-ssh-authentication-gives-an-authenti), where the answer talks about the third-party access restrictions for organisations on Github. It also hints that "any SSH keys generated before February 2014 would no longer work for new apps". So I decided to try to generate a new ssh key following strictly the [official Github guide](https://help.github.com/articles/generating-ssh-keys/). And the new key did work. The only difference between the old key and the new one was that the former used an email not related to the Github account, while the later was generated for the correct email. Exactly the same worked for Bitbucket.