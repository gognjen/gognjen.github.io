---
layout: post
title: Make your own web scraping tool using Python and lxml
date: 2014-09-01 17:23:01 
disqus: true
---

In this post I'm going to explain how I made a simple web scraping tool with Python and lxml package library.

I was working on job board website and I wanted to make list of all jobs available in my city. There are few popular
places on web where people from my area anounce their job offers. None of them have some kind of API or RSS feeds 
available so I had to use web scraping tehnique to get that data on my site. Here I'm going to explaine how 
I got jobs from posao.banjaluka.com. 

![My helpful screenshot]({{ site.url }}/assets/images/xpath-help.png)
<figcaption></figcaption>

First question is where to start with scraping. The easiest way for me to get all jobs was to find a link where I 
have all jobs listed in paged list. After that I could loop through all pages and get jobs from each one. Here is a simple that do just that. 

{% highlight python %}
import urllib2

# This URL is my starting point.
page_url = ['http://posao.banjaluka.com/?page_id=4']

# If there is a page to scrap...
while page_url:
            
    # Create HTTP request object with urllib2.
    req = urllib2.Request(page_url[0])
    
    # Some sites require that you set the "User-Agent" header.
    req.add_header('User-agent', 'Mozilla 5.10')
    
    # Opening an network connection.
    res = urllib2.urlopen(req)
    
    # Read and decode response object into a string.
    html_page = res.read().decode('utf-8')
    
    # Close response object.
    res.close()              

    # Parse page and return next page URL if exists.
    page_url = parse_page_and_return_next_page_url(html_page)
            
{% endhighlight %}

Here goes an interesting part of this story. In the next function I'm using lxml library 
and XPath query language. You can also use CSS element selection language for this if you want.

XPath is a language for finding information in an XML document. XPath uses path 
expressions to select nodes or node-sets in an XML document. These path expressions 
look very much like the expressions you see when you work with a traditional computer 
file system.

This is how my HTML page structure looks like. I'm passing it as string argument to my
function.

{% highlight html %}

<!DOCTYPE html>
<html lang="en-US">
   <head> 
   </head>
   <body>
      <div id="single-post">
         <form method='post'>
            <div class="job-list">
               <div class="job1 job5638 odd">
                  <div class="job-box">
                     <!-- Job title --> 
                     <h3><a href="http://posao.banjaluka.com/?page_id=5638">Tržišni inspektor </a></h3>
                     <!-- Location --> 
                     <h4>Banja Luka</h4>
                     <!-- Employer --> 
                     <p>Poslodavac: Gradska uprava Grada Banja Luka</p>
                     <!-- Category -->                      
                     <p>Kategorija: <a href="http://posao.banjaluka.com/?jcat=drzavna-sluzba-zaposlenje-banjaluka" title="Jobs for Državna služba i uprava">Državna služba i uprava</a></p>
                     <!-- Date published and expire date -->                      
                     <p>Objavljeno: 29.10.2014. Ističe: 13.11.2014.</p>
                  </div>
               </div>
               <div class="job2 job5648 even">
                  <!-- Same structure as job1 --> 
               </div>
               <div class="job3 job5637 odd">
                  <!-- Same structure as job1 --> 
               </div>
               <div class="job4 job5649 even">
                  <!-- Same structure as job1 --> 
               </div>
               <div class="job5 job5650 odd">
                  <!-- Same structure as job1 --> 
               </div>
               <div class="clear"></div>
               <div class="job-nav">
                  <div class="previous"></div>
                  <div class="this">Poslovi od 1 do 5 od ukupno 91</div>
                  <!-- Link to the next page (if exists) -->                   
                  <div class="next"><a href='http://posao.banjaluka.com/?page_id=4&amp;page=2'>Strana 2</a></div>
               </div>
            </div>
            </p>
         </form>
      </div>
   </body>
</html>
{% endhighlight %}

And this is how my function looks like. 

{% highlight python %}
from lxml import html
import re
from datetime import datetime


def parse_page_and_return_next_page_url(page):

    tree = html.fromstring(page)
              
    """
    This is one way go query data from my HTML page. Maybe it's
    not the best one, but it works.
    """

    titles = tree.xpath(
        '//*[@id="single-post"]/form/div/div/div/h3/a/text()'
    )
    external_links = tree.xpath(
        '//*[@id="single-post"]/form/div/div/div/h3/a/@href'
    )
    locations = tree.xpath(
        '//*[@id="single-post"]/form/div/div/div/h4/text()'
    )
    employers = tree.xpath(
        '//*[@id="single-post"]/form/div/div/div/p[1]/text()'
    )
    categories = tree.xpath(
        '//*[@id="single-post"]/form/div/div/div/p[2]/a/text()'
    )
    dates = tree.xpath(
        '//*[@id="single-post"]/form/div/div/div/p[3]/text()'
    )    
    # Get the URL of the next page 
    next_page_url = tree.xpath(
        '//*[contains(@class, 'job-nav')]/div[3]/a/@href'
    )
    
    # Iterate through multiple lists and save Job object to database.
    for title, link, location, employer, category, dates in zip(titles, external_links, locations, employers, categories, dates):
        # Usign regular expression to get array of dates from string
        # (ex., "Published: 29.10.2014. Expires: 13.11.2014.")
        parsed_dates = re.findall(r'\d{2}.\d{2}.\d{4}.', dates)
        # Date published is the first one
        publish_date = datetime.strptime(parsed_dates[0], "%d.%m.%Y.").date()
        # Expire date is the second one
        expire_date = datetime.strptime(parsed_dates[1], "%d.%m.%Y.").date()            
        
        # Do something with data       
        print(title)
        print(location)
        print(link)
        print(employer)
        print(category)
        print(expire_date)
        print(publish_date)
                
    return next_page_url 

{% endhighlight %}

If you are facing any problem please share it with me below in comments or send me an email.



