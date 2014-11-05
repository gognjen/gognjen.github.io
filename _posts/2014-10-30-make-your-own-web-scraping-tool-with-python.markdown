---
layout: post
title: Make your own web scraping tool using Python and lxml
date: 2014-11-02 17:23:01 
disqus: true
---

In this post I'm going to explain how I made a simple web scraping tool with Python 
and lxml toolkit.

I was working on job board website and I wanted to make list of all jobs available in 
my city. There are few popular places on the Web where people from my area announce their 
job offers. None of them has some kind of API or RSS feeds available so I had to use a
Web scraping technique to get that data. 

The first step is to navigate to the pages with the data. This might require entering 
some text into a search box (e.g., searching for a product on Amazon), or it might 
require clicking “next” through all of the pages that results are split over (often 
called pagination). In my case I had to handle pagination across multiple pages of 
results. I parsed the starting page and fou Here is a simple piece of code that do just that. 

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

After I solved this, here comes the interesting part - extracting the data.

This is HTML page structure that I'm passing as `html_page` argument to 
`parse_page_and_return_next_page_url()` function.

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

In `parse_page_and_return_next_page_url()` function I'm going to use lxml 
library and XPath query language. You can also use CSS element selection language for 
this if you want.

And this is how my function looks like:

{% highlight python %}
from lxml import html
import re
from datetime import datetime


def parse_page_and_return_next_page_url(page):

    tree = html.fromstring(page)
              
    # This is one way go query data from my HTML page.

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
    
    # Iterate through multiple lists.
    for title, link, location, employer, category, dates in zip(titles, external_links, locations, employers, categories, dates):
        """
        Using regular expression to get array of dates from string
        My string looks like this: 
            Publish date: 29.10.2014. Expiry date: 13.11.2014.
        """
        parsed_dates = re.findall(r'\d{2}.\d{2}.\d{4}.', dates)
        # Date published is the first one
        publish_date = datetime.strptime(parsed_dates[0], "%d.%m.%Y.").date()
        # Expiry date is the second one
        expiry_date = datetime.strptime(parsed_dates[1], "%d.%m.%Y.").date()            
        
        # Do something with data       
        print(title)
        print(location)
        print(link)
        print(employer)
        print(category)
        print(expiry_date)
        print(publish_date)
                
    return next_page_url 

{% endhighlight %}

If you are facing any problem please share it with me below in comments or send me an email.



