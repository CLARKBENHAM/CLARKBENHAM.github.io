---
title: "When to quit Books"
date: 2025-04-30
draft: True
mathjax: True
---
I was swayed by Tanner Greer, Scholars Stage, The Last Psychiartrist and a general twitter gestault to read classic literature. I was a bookish child, mostly stopped in high school and college but now have done so for more than 5 years.
Since 01/01/2019 I've finished exactly 200 books, 132 books from 5/24/2021 to 3/11/2025 over 807.7 hours of reading including for books unfinished.
But compare my opportunity costs: In the same period I watched 639 hours worth of movies, 1068.12 hours of blogs and 804.83 hours of twitter. So in spending a similar amount of time I definitly prefer all books and no movies to no book and all movies.
But the question is marginal not infra-marginal: how to allocate the least productive half of each chunk of time?

I use Google Play to track my books because your highlights are saved in an external google doc which makes it easy to re-read and remember the main points of the book.
I used Google Takeout to export my books data and used this [script](https://github.com/CLARKBENHAM/side_projects/blob/master/Self_Tracking/play_books_to_csv.py) to create a csv of completed titles, their authors, and the bookshelf category.

I then labeled each book for enjoyment and overall usefulnes. I found:
1. The books most helpful to my life have been: 3 self-help books addressing concrete problems (eg The MindBody Prescription), The Goal ("Don't optimize based on local conditional if you get paid to improve the output of the system as a whole"), and Algorithms by CLRS for tech interviews.
2. On average each "Literature" book, something I would not normally read but did for self improvement was more enjoyable and useful than regular fiction, but less so than general non-fiction, businees writing, and especially than Computer Science, Math, and ML textbooks.
2b. I'd attribute the larger enjoyment to defering my picks to more knolwedgable readers for Literature than for fiction.
2c. I don't put enough effort into books I assume don't deserve it. The Math book I got the most value out of per hour was probably LADR where I solved every problem, a book I'd just read through wouldn't have been as impactful.
3. I am not able to draw clear morals from books. The books I which have the most influence on my moral outlook are CS Lewis's Abolition of Man, Screwtape letters, and The Great Divorce,  and Unsong by Scott Alexander. The first 3 are all explicit essays and where far more impactful than his Hideous Strength Trilogy which I'd have taken nothing from if I hadnot already read his books. The 4th I read at the same time as his essays talking exactly about the point of the book so I didn't have an extra deduction to do. I would still rank these below The Last Psychiatrist's blogging and at one point in time EA. Only by direct essays can I understand the world.

Here are the distributions of ratings out of 5:
<img src="/images/book-enjoyment-distribution.jpg">
<img src="/images/book-usefulness-distribution.jpg">


But average rating is not what I want to maximize: one the rating does not linearly correlate into value, 1 "5" is more than 25% more valuable than a 4. Second I don't have to finish every book I start and that takes up a ton of time. There's a big difference in a 35th vs 70th percentile book and I can generally tell pretty quick.

I wrote a stimulation to test this.







Business/management: 44, Computer Science: 14; fiction: 51; General Reading: 39; Literature: 50; Machine Learning: 5; Math: 3; Unknown Shelf: 1;