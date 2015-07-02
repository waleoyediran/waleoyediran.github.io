---
layout: post
title: Building Imageing
author: Oyewale Oyediran
feature-image: imageng.jpg
tags:
- social
- google-cloud
- gcdc
- imageing
- image-processing
- javascript
- appengine
---

![placeholder](/images/imageng.jpg "Imageing")

In the social media ero we live in, people are extremely conscious of how they are perceived by others. We are seeing a much higher level of expression especially from young people and pictures have become one of the most ubiquitous means of these expressions. Testament to these is the success of various photo-editing applications, Instagram, PicMix and others. The recently concluded Google Cloud Developer Challenge inspired us to create something that we think will help people express themselves more.

###Why we built Imageing?
While Instagram seem to have grabbed most of the market with their successful Android and iOS apps, they have yet to put the power of their awesome filters into their web app. The instagram web app only allows you to view photos, edit your profile and comment on photos. Considering the large amount of time people spend on their browsers, we thought their we could take advantage of that space and build a useful photo-editing application that would work on a browser and feature custom image filters and incorporate sharing.

###How it works
We built Imageing with the aim of helping browser-based users aggregate their photos from various social media channels (Facebook, Google+) and also photos on their devices, edit them with various Instagram-like filters and also let them share these edited photos on social media. We also had to ride on our users love for memes, by adding capabilities to create memes out of their photos.
We included trending photos on the homepage where we feature photos that are attracting high volume of traffic. (You could try it out at http://image.ng).

###How we did it?
Imageing’s backend is powered by Google’s AppEngine. When you just want to hack and in no mood to bother yourself about infrastructure and scalability challenges, you certainly would want a platform that offers top grade hardware capabilities and offers you features that allows you get a Minimum-Viable-Product within a short timespan. We had no doubt about AppEngine’s reliability. We chose the AppEngine’s Python runtime because, we wanted a simple language that would allow us iterate fast.
A challenge we had to face was the choice of image processing technique. We had to choose between doing the image processing on the server with Python Image-manipulation Library or on the browser with Javascript. We tried both and compared their performance and decided to go with client-side Image processing.

###Okay, about the team
Its a small team and most of us like to stay under the radar. Just for the records, we have +Caleb Mbakwe, +Moyinoluwa Adeyemi, +Akinade Gbenga and your’s truly +Oyewale Oyediran working on Imageing. We started building it in the laboratory at the Department of Computer Science at Obafemi Awolowo University during one of our free weekends. I’m always happy to answer questions about Imageing on my twitter handle @waleoyediran, if you tweet at me.

###Where are we going from here?
We are not calling for an IPO anytime soon (*giggles**). We are building new free and premium features into Imageing and improving the user experience. Yes, we are adding premium features, they are central to our monetization strategy. We would be ditching the appspot domain we are currently riding on for what we think is a more exciting domain name. We also have a native app cooking up, we would try to take on the big guys.


