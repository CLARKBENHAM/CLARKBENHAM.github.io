---
title: "When to quit Books"
date: 2025-04-30
draft: True
mathjax: True
---
I was swayed by Tanner Greer, Tyler Cowen, The Last Psychiartrist and a general twitter gestault to read classic literature. I was a bookish child, mostly stopped in high school and college but now have done so for more than 5 years.
From 01/01/2019 to March 2025 I've finished 207 books. From May 2021 to March 2025 over 807.7 hours I finished 132 books of reading including for books unfinished.
But compare my opportunity costs: In the same period I watched 639 hours worth of movies, 1068.12 hours of blogs and 804.83 hours of twitter. So in spending a similar amount of time I definitly prefer all books and no movies to no books and all movies.
But the question is marginal not infra-marginal: how to re-allocate the least productive chunk of time between categories?

I use Google Play to track my books because my highlights are saved in an external google doc which makes it easy to re-read and remember the main points of the book.
I used Google Takeout to export my books data and used this [script](https://github.com/CLARKBENHAM/side_projects/blob/master/Self_Tracking/play_books_to_csv.py) to create a csv of completed titles, their authors, and the bookshelf category.

I then labeled each book for enjoyment and overall usefulnes. I found:
1. The books most helpful to my life have been: 3 self-help books addressing concrete problems [1], The Goal [2], and Algorithms by CLRS for tech interviews.
1b. Other reviewers are biased against concrete self-help books since they achnoledge specific embarassing problems, but if if general the [95% percentile](https://danluu.com/p95-skill) isn't that good then I should assume there's many large basic flaws I'm making.
2. On average each "Literature" book, something I would not normally read but did for self improvement was more enjoyable and useful than regular fiction, but less so than general non-fiction, businees writing, and especially than Computer Science, Math, and ML textbooks.
2b. I'd attribute the larger enjoyment to defering my picks to more knolwedgable readers for Literature than for fiction.
2c. I don't put enough effort into books I assume don't deserve it. The Math book I got the most value out of per hour was probably LADR where I solved every problem, a book I'd just read through wouldn't have been as impactful.
2d. Which would make me act kinder over the course of a year: "Life and Fate" or writing a 1 senetence affirmation every day and trying to follow it for 15 minutes? I'd say the later. So either transfer learning is fake in general or for me personally at this level of abstraction.
2e. Reading Caro's books on Johnson seems to give me a much clearer understanding of people than any Literature did. He has realistic dialouge, which is why the Life of Johnson is engaging: repeating direct quotes is more realistic than the inventions of any author.
3. I am not able to draw clear morals from books. The books I which have the most influence on my moral outlook are CS Lewis's Abolition of Man, Screwtape letters, and The Great Divorce,  and Unsong by Scott Alexander. The first 3 are all explicit essays and where far more impactful than his Hideous Strength Trilogy which I'd have taken nothing from if I had not already read his books. The 4th I read at the same time as his essays talking exactly about the point of the book so I didn't have an extra deduction to do. I would still rank these below The Last Psychiatrist's blogging and at one point in time EA. Only by direct essays can I understand the world.
3b. Question of impact of litaerature, Kipling took 2k pages vs EY; DFW as asking more relevant questions; Can't tell minor characters of Harding's Jude The Obscure and The Well-Beloved apart, can't really keep the plots of Great Expectations vs Bleak house straight - just short stories.
4. This doesn't count the cummulative effect of reading literature. There was a distinct moment when Twain went from practically impenetrable to understandable, and a specific week where Kipling started making sense out of a few months of on/off reading. Both happened a couple hundred pages in. How do I value this Groking? If nothing raises IQ it seems like it at least changes my ability to process long sentences with novel syntax. I don't know how to value this.

Here are the distributions of ratings from 1-5:
<img src="/images/book-enjoyment-distribution.jpg">
<img src="/images/book-usefulness-distribution.jpg">
Enjoyment mean 3.4 (sd 1.0) and Usefulness 1.8 (sd 1.1).

But average rating is not what I want to maximize: one the rating does not linearly correlate into value, 1 "5" is more than 25% more valuable than a 4. Second I don't have to finish every book I start and that takes up a ton of time. There's a big difference in a 35th vs 70th percentile book and I can generally tell pretty quick.

I wrote a stimulation[script](https://github.com/CLARKBENHAM/side_projects/blob/master/Self_Tracking/play_books_static_stopping.py) to test this. I sampled books from the same category, given costs to stopping books early, a fixed cost to finding a new book, and with noise in my estimates of value. I found the size of noise in my estimates by rating all the books again after 2 weeks later. For Enjoyment and Usefulness, R=0.77 and 0.85; l1 = 0.46 and 0.34; sqrt mse = 0.66 and 0.59 [3]. Errors were assumed to be serially correlated at r=0.9. When I dropped books I substituded the average level of enjoyment of following the current stragegy, while also accounting for how many books I already stop [5].

With a fine grid I calculated the Utilty maximizing number of books to cut at each step. The average and median optimal curve of books dropped is below. Note that the best cases are the top left paths: when my estimates are accurate it's optimal to drop more books earlier, when they're inaccurate to drop less at each stage.
Usefulness
# TODO don't want average drop schedule but median drop schedule
```
============================== FINAL SUMMARY ==============================

Summary for: fiction
Fraction Read    Cumulative Drop %
--------------------------------------------------
        0.11                79.55
        0.31                93.46
        0.50                94.11
Final cumulative drop: 94.2%
Median Final Utility (simulated): 1.50 (Rating: 2.32)
Mean Final Utility (simulated): 1.49 (Rating: 2.32)
Current Avg Utility (empirical): 0.48 (Rating: 1.56)

Summary for: Literature
Fraction Read    Cumulative Drop %
--------------------------------------------------
        0.11                77.25
        0.31                90.61
        0.50                92.15
Final cumulative drop: 92.6%
Median Final Utility (simulated): 2.17 (Rating: 2.66)
Mean Final Utility (simulated): 2.16 (Rating: 2.66)
Current Avg Utility (empirical): 0.87 (Rating: 1.90)

Summary for: Business, management
Fraction Read    Cumulative Drop %
--------------------------------------------------
        0.11                75.47
        0.31                87.14
        0.50                88.41
Final cumulative drop: 89.0%
Median Final Utility (simulated): 3.31 (Rating: 3.11)
Mean Final Utility (simulated): 3.30 (Rating: 3.11)
Current Avg Utility (empirical): 1.59 (Rating: 2.37)

Summary for: General Reading
Fraction Read    Cumulative Drop %
--------------------------------------------------
        0.11                75.58
        0.31                88.35
        0.50                89.48
Final cumulative drop: 89.8%
Median Final Utility (simulated): 5.25 (Rating: 3.64)
Mean Final Utility (simulated): 5.24 (Rating: 3.64)
Current Avg Utility (empirical): 2.44 (Rating: 2.78)

Summary for: Computer Science
Fraction Read    Cumulative Drop %
--------------------------------------------------
        0.11                65.59
        0.31                74.87
        0.50                76.71
Final cumulative drop: 77.4%
Median Final Utility (simulated): 5.53 (Rating: 3.71)
Mean Final Utility (simulated): 5.48 (Rating: 3.70)
Current Avg Utility (empirical): 3.23 (Rating: 3.08)
```


# With a less refined grid I directly computed the optimal path through all possible drop categories. The median and mean of that are below. There IS/ISNT a significant difference in the global optimal path vs the greedy path at each step.
Actually it would never make sense to do this? The rate at which I get information about a books quality goes down every minute, so I should be most willing to quit a book early.
There's no case where it would make sense to get to 1/3 way mark.
So the rate at which I drop books should be declining, therefore the greedy approach is correct.
But this interacts with the overhead of picking a book in the first place. Still then seems like there should be an increase and then decrease afterwards.
	Would it be possible to solve for this directly?

The Plot of Median optimal drop percentage, by category, as I vary: The noiseness of my value estimates.

The Plot of Median optimal drop percentage, by category, as I vary: The auto correlation of my errors.

The Plot of Median optimal drop percentage, by category, as I vary: Utility with Book rating. utility = VALUE_BASE ** rating

Heat plot of utilities If I spent X times longer picking a book for a Y pt gain in average value.

Heat plot of utilities If I spent X times longer reading a book for a Y% decrease in estimate error.


Conclusion:
These graphs are of how many books I should be dropping of the books that I'm currently finishing. I should go from finishing 75-80%[4] of books I start to X%.
I should have dropped X%,Y,Z% of the books I've started at 5%, 10%, 25%, and 50% of the way through.
I should spend X more/less time deciding to start a given book.
I should spend X more/less time deciding if I should continue a book once started.

I expected these changes to increase my utility from reading by Xpts or Y%, holding categories constant.

Table of new estimate of utility per category:


[1] The MindBody Prescription, Models (a long time ago, though The Last Psychiatrist's blog as a whole would be even more useful), and Alan Carr's writings.
[2] "Improving local performance according any metric will not find the optimal total performance; in a complicated system you can't predict intereactions but you can find the single current bottleneck. Fix that bottlneck and then find the next bottleneck. Run the Simplex Algo not Greedy Search."
[3] So even after finishing a book I only know how how much I liked it to within ~0.6 pts.
[3b.] I didn't use a rubric just a geshalt impression. A rubric would make my answers consistent and reduce variance, but I was afraid would introduce a bias in my stated rubric vs. real internal rubric.
[3c.] There's 4 books that my esimation of their usefulness shifted by more than 1.5 pts after reading.
[4] From books currently started but uncompleted I will finish 10, might finish 17, won’t finish 35. I'd say I'm dropping (17/2+35)/(10+207)=20%.
Though it's 26% of books dropped after May 2021 (15/2+29)/(132+10). Throughout the rest of this analysis I'll calculate how many more books should I be dropping  the books I'm currently am finishing.
[5] I substituted an enjoyment of 1.5 and usefulness of 1 for the books I stopped. With my current pace and queue it'll take between and 13 years so I'm not worried about running out. 2_261 days/(17/2+35 + 207) * 459/365 = 11.4 years. My best pace was 459 books / 48 finished/year * 1 started/0.76 finished = 7.3 years.


207=Business/management: 44, Computer Science: 14; fiction: 51; General Reading: 39; Literature: 50; Machine Learning: 5; Math: 3; Uncategorized Shelf: 1;