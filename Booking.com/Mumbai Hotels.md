# Web Scraping Project : Scraping Hotel List from Booking.Com
* I scraped the list of the hotels available for Mumbai city(India) from the website - booking.com.

* This website is being used all around the world.

* If you want to check-out this website.

* The link is here- https://www.booking.com/

# The Objective of this project would be 

1. Scrape hotel list available at Mumbai location.

2. We'll scrape hotel name, reviews, locality, ratings and more.

3. Finally we'll make a csv file for further analysis and use.

# Tools I am Going to Used -

1. VS Code
2. Python
3. Python Libraries such as bs4, pandas, lxml, requests, and jovian.

# Imports

``1. Beautiful Soup``

BS4 module helps in pulling data out of HTML and XML files. For scraping we need to import BeautifulSoup function from the bs4 module.

For more info: https://www.crummy.com/software/BeautifulSoup/bs4/doc/

``2. Requests``

Requests is an elegant and simple HTTP library to request and retrive the data from the server.

For more info: https://docs.python-requests.org/en/master/

``3. lxml``

lxml is the most feature-rich and easy-to-use library for processing and parsing XML and HTML. Although there is an inbuilt parser avaibale, I prefer to use it because of its speed.

For more info: https://lxml.de/

``4. Pandas``

Pandas is one of the most powerful and easy to use data structures and data analysis tool. We'll use it for organising, viewing and saving our scraped data.

For more info: https://pandas.pydata.org/docs/index.html

``5. Matplotlib``

Matplotlib is an easy to use data analysis and data visualisation tool. We'll use it for making graphs and plots out of our scraped data.

For more info: https://matplotlib.org/stable/contents.html


# Let's Start Installing modules

```
from bs4 import BeautifulSoup # Import for Beautiful Soup
import requests # Import for requests
import lxml # Import for lxml parser
import pandas as pd # Import for Pandas
import matplotlib.pyplot as plt # Import for Matplotlib Pyplot
%matplotlib inline
```

# Steps 

* Open  Booking.com - select city - Select Top Reviewed

Select the Top Reviewed option:

![7](https://user-images.githubusercontent.com/120908587/216781471-96f7863e-70fe-44d9-b43e-53c2dc886426.png)

* Page shows Mumbai has around 552 properties to book.

* Maximum Pages booking.com can show is 23 with 25 sections per page.

![image](https://user-images.githubusercontent.com/120908587/216781763-8a2f160c-cc0c-4838-aada-f193a8f0bde0.png)

``
Therefore 25*23=575, we would be able to see and select 1000 (maximum) hotels only, so its better to grab places with better ratings, that's Top Reviewed option.
``

# Let's Check the first and last page:

We can see the internal working of the link, understanding it is important in order to manipulate it as per our needs.


<details>
  <summary>First Page Link!</summary>
<br>

```
https://www.booking.com/searchresults.html?label=gen173rf-1BCAEoggI46AdIM1gDaGyIAQGYAQm4ARfIAQzYAQHoAQGIAgGiAgpqb3ZpYW4uY29tqAIDuALv3_meBsACAdICJDVmODc4YzA5LTE3NjMtNGY1Yi1hNTYzLTkxNTE0MjBmMjk1Y9gCBeACAQ&sid=5645ee060b5d5d2984fbecc43d1c1674&aid=304142&dest_id=-2092174&dest_type=city&group_adults=null&req_adults=null&no_rooms=null&group_children=null&req_children=null&order=bayesian_review_score&offset=0
```

</details>
<br>

<details>
  <summary>Last Page Link !</summary>
<br>

```
https://www.booking.com/searchresults.html?label=gen173rf-1BCAEoggI46AdIM1gDaGyIAQGYAQm4ARfIAQzYAQHoAQGIAgGiAgpqb3ZpYW4uY29tqAIDuALv3_meBsACAdICJDVmODc4YzA5LTE3NjMtNGY1Yi1hNTYzLTkxNTE0MjBmMjk1Y9gCBeACAQ&sid=5645ee060b5d5d2984fbecc43d1c1674&aid=304142&dest_id=-2092174&dest_type=city&group_adults=null&req_adults=null&no_rooms=null&group_children=null&req_children=null&order=bayesian_review_score&offset=550
```

</details>
<br>


`
If we look at both the links it is showing 25 results on every page and goes up from 0 (offset=0) to 550 (offset=550), increment of 25 ( Pages 0 to 23 )

`


* We Will add headers section while requesting for the web page because - 

- When scraping websites, sending a header with the request can provide several benefits:

* Identification: A header can help identify the scraper as a specific user agent, allowing the website to tailor the content it serves to that specific user agent.

* Access control: Some websites block requests from scraper or bots. By sending a header that identifies the scraper as a valid user agent, it may be possible to bypass these blocks.

* Request control: Headers can be used to specify the preferred content type, encoding, and other parameters that the website should use when serving the response.

Overall, sending headers while scraping websites can help ensure that the scraper receives the desired content in the desired format, and can also help avoid issues with access control.

# To Find your Header -

<details>
  <summary>Click to reveal Link !</summary>
<br>

```
https://httpbin.org/get
  
* The link "https://httpbin.org/get" is a public web service for testing HTTP requests. It provides a simple API for sending a GET request to the URL, and returns information about the request, including the headers and query parameters sent in the request. This service can be used for testing and debugging HTTP requests, as well as for learning about HTTP requests and how they work.
```

</details>
<br>


# First Stage -

In this stage we'll try to do :

* Fix the details we need and load the webpage.
* Look for relevant classes, tags and links realted with the information.
* Make functions for later use

``
main_url = 
https://www.booking.com/searchresults.html?aid=304142&label=gen173nr-1FCAEoggI46AdIM1gEaGyIAQGYATG4ARnIAQ_YAQHoAQH4AQKIAgGoAgO4AsrBzIcGwAIB0gIkNDFiZWU4NjMtMDU1My00NjA4LTkxMmYtMGE2NzliNTRiNTNl2AIF4AIB&sid=55fa6423aa5026996217708ea6be131f&tmpl=searchresults&city=-2106102&class_interval=1&dest_id=-2106102&dest_type=city&dr_ps=IDR&from_idr=1&group_adults=2&group_children=0&ilp=1&label_click=undef&no_rooms=1&order=bayesian_review_score&raw_dest_type=city&room1=A%2CA&sb_price_type=total&shw_aparth=1&slp_r_match=0&srpvid=518283f1feb10049&ssb=empty&top_ufis=1&sig=v1Q1dbuDSb&rows=25&offset=0
``

``
headers = {'User-Agent':'Mozilla/5.0 (platform; rv:geckoversion) Gecko/geckotrail Firefox/firefoxversion'}
``


