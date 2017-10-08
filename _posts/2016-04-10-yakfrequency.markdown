---
layout:     post
title:      "YakFrequency"
subtitle:   "Web app that analyses word frequencies in Yik Yak Posts"
date:       2016-04-10 12:00:00
categories: projects
author:     "Dominic Peters"
---

![]({{ site.url }}/img/yakfrequency/exams.PNG){:class="center" width="90%"}

Word clouds are something that have been done a million times on different platforms, but I thought it would be interesting to see what kind of results I'd get analysing Yik Yak posts. For those that have never heard of Yik Yak, it's an app that allows people to anonymously post messages to a location based board. The locations are usually centered around universities so that students only see messages written by other students at their university.

Yik Yak doesn't have a public API, but that hasn't stopped plenty of people from reverse engineering their own libraries with varying degrees of reliability. YakFrequency has three components- data gathering, processing and visualization. The first is a python script that uses an [existing library](https://github.com/akashlevy/Yaklient) to supply the post data. It's then stripped of everything but the actual content, sanitized, and added to a database. This process repeats hourly.

To process the posts I am using another python script with start/end date and location parameters. It compiles every post with said parameters from the database and runs it against a list of stopwords, removing words that don't have any significance. The remaining word list is run through a counter which tallies the amount of times each each word occurs. The 30 most common words are returned with their respective number of occurences.

Finally, I am using a simple flask site to display the most common words. There are a couple sliders to set the parameters, and whenever a slider is moved, the new post range is processed server side and the word cloud updated with the resulting words. The whole thing is hosted on a free Amazon EC2 instance so I'm quite happy to see that it can process hundreds of thousands of words nearly instantaneously.

<video class="center" width="100%" controls>
  <source src="{{ site.url }}/img/yakfrequency/demo.mp4" type="video/mp4">
</video>
<p></p>
###### Demo Video
<p></p>


![]({{ site.url }}/img/yakfrequency/brussels.PNG){:class="center" width="90%"}
###### March 22, 2016. Date of the Brussels terrorist attack and death of Rob Ford

[Source Code](https://github.com/dpeters1/YakFrequency)

