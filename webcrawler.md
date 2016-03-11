title: Web Crawler
tags: web crawler
categories: 
    - project
    - Intro to Computer Science
date: 2016-03-01
---
![](/images/web crawler.png)
### Introduction
> <font size=2><p>A <strong>Web crawler</strong> starts with a list of URLs to visit, called the seeds. As the crawler visits these URLs, it identifies all the hyperlinks in the page and adds them to the list of URLs to visit, called the crawl frontier. URLs from the frontier are recursively visited according to a set of policies. If the crawler is performing archiving of websites it copies and saves the information as it goes. The archives are usually stored in such a way they can be viewed, read and navigated as they were on the live web, but are preserved as ‘snapshots'.- [wikipedia](https://en.wikipedia.org/wiki/Web_crawler)</p></font>

<!--more-->

<p>We can start from one seed page to search all the URLs with finite depth that we can get, then rank them in order, and give us the best choose or a rank list. And this is what the project does.
</p>


### Project
<p>For this project, Our goal is base on the keyword from input we return a list of URLs, that all of them contain the keyword and the most popular URL rank at the top.
<p>We can divide the problem to several parts:
<font size=2><ol>
<li>Get all the content from one page, we can use these content to find URLs and matched keyword</li>
<li>Get all URLs from one page's content</li>
<li>Store all URLs that contain the keyword to a hash table from pages' content</li>
<li>Get a map of one page's URL to all URLs that this page content, we can use this to rank our order</li>
<li>Give as the rank list by using sorting algorithm</li>
</ol></font>


- [github code link](https://github.com/zhaot777/PythonWebCrawler)

### Summary
<p> This project is a just implement a simple function to get the URLs from a seed page.
<p> It is base on the given input cache, which is a dictionary that stores all the URLs and maps content.
<p>In fact, we can use Scrapy to help us to build a web crawler easier rather than write by ourselves.


### More about Web Crawler


<p>Scrapy is a free and open source web crawling framework written in Python. Originally designed for web scraping, it can also be used to extract data using APIs or as a general purpose web crawler.  
<p> If there are large scare of output, we can use this tool crawling content and store in MongoDB.
<p> Next time I will use Scrapy and MongoDB to implement the web crawler.
