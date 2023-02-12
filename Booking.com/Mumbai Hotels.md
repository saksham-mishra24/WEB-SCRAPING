# Web Scraping Project : Scraping Hotel List from Booking.Com
* I scraped the list of the hotels available for Mumbai city(India) from the website - booking.com.

* This website is being used all around the world.

* If you want to check-out this website.

* The link is here- https://www.booking.com/

# The Objective of this project would be 

1. Scrape hotel list available at Mumbai location.

2. We'll scrape hotel names, locations

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
* Use the requests library to download web pages

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

![image](https://user-images.githubusercontent.com/120908587/218297630-4cd4233b-2efa-4667-bb55-5e2a42cf876c.png)

* Page shows Mumbai has around 552 properties to book.

* Maximum Pages booking.com can show is 23 with 25 sections per page.

![image](https://user-images.githubusercontent.com/120908587/218297677-030cdac8-b8e9-468a-83d8-7f7bbc2bc192.png)

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
https://www.booking.com/searchresults.html?label=gen173nr-1FCAEoggI46AdIM1gEaGyIAQGYATG4ARfIAQzYAQHoAQH4AQKIAgGoAgO4ApyIn58GwAIB0gIkOTlmNTBmOGMtMjQxNi00MzhjLWI5MzgtZjljNDVlYjE5ZjAz2AIF4AIB&sid=9c98c49eaafdd835f3efe6d3301b8fde&aid=304142&sb_lp=1&error_url=https%3A%2F%2Fwww.booking.com%2Findex.html%3Flabel%3Dgen173nr-1FCAEoggI46AdIM1gEaGyIAQGYATG4ARfIAQzYAQHoAQH4AQKIAgGoAgO4ApyIn58GwAIB0gIkOTlmNTBmOGMtMjQxNi00MzhjLWI5MzgtZjljNDVlYjE5ZjAz2AIF4AIB%26sid%3D9c98c49eaafdd835f3efe6d3301b8fde%26sb_price_type%3Dtotal%26%26&ss=Mumbai&is_ski_area=0&ssne=Mumbai&ssne_untouched=Mumbai&dest_id=-2092174&dest_type=city&checkin_year=&checkin_month=&checkout_year=&checkout_month=&group_adults=2&group_children=0&no_rooms=1&b_h4u_keep_filters=&from_sf=1&order=bayesian_review_score&offset=0
``

``
headers = {'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36'}
``

# Here we added the link we are going to use, similar to the one we used above. Additionaly we set out our headers that we are going to pass.I'm using Mozilla so my header looks like that but its different for different clients and OS.

```python
# Request for the URL
page = requests.get(main_link, headers=headers)

# We can also check the response code
print(page.status_code) #if ouput = 200 - meaning successfully done

# Make it a soup
soup = BeautifulSoup(page.text,"lxml")

# Display Soup ( Main Page)
soup
```


```python
# We need to add query number

def get_page(url,head,num):
    """
    Function takes url as parameter and returns the soup.
    `url` : Link in strings
    'num' : Fordifferent pages (1-40)
    'head' : header for the url
    """
    page = requests.get(url.format(num),headers=head)
    if page.status_code != 200:
        raise Exception('Failed to load page {}'.format(url))
    soup = BeautifulSoup(page.text, "lxml")
    return soup
  ```
  
  # Let's Start working on the page.
  
  * Right Click - Click on Inspect 
  * Find a Common tag like this -
  ![image](https://user-images.githubusercontent.com/120908587/218298156-e55fac11-1af3-433d-bdb9-d519eee038ae.png)
  
  ```python
  blocks = soup.select(".a826ba81c4.fe821aea6c.fa2f36ad22.afd256fc79.d08f526e0d.ed11e24d01.ef9845d4b3.da89aeb942")
```
* If you want to get length of the blocks
  
 ```python
len(blocks)
#output = 25
```
* It means a page contains 25 blocks

 ```python
# For first Block
blocks[0].prettify
```
* The prettify() method will turn a Beautiful Soup parse tree into a nicely formatted Unicode string, with a separate line for each tag and each string.

</details>
<br>

<details>
  <summary>Click Here to see output</summary>
<br>

```
  <div class="a826ba81c4 fe821aea6c fa2f36ad22 afd256fc79 d08f526e0d ed11e24d01 ef9845d4b3 da89aeb942" data-testid="property-card"><div class="d20f4628d0"><div class="f9d4f2568d"><div class="c90a25d457"><a aria-hidden="true" href="https://www.booking.com/hotel/in/the-oberoi-mumbai.en-gb.html?label=gen173nr-1FCAEoggI46AdIM1gEaGyIAQGYATG4ARfIAQzYAQHoAQH4AQKIAgGoAgO4ApyIn58GwAIB0gIkOTlmNTBmOGMtMjQxNi00MzhjLWI5MzgtZjljNDVlYjE5ZjAz2AIF4AIB&amp;sid=9c98c49eaafdd835f3efe6d3301b8fde&amp;aid=304142&amp;ucfs=1&amp;arphpl=1&amp;dest_id=-2092174&amp;dest_type=city&amp;group_adults=2&amp;req_adults=2&amp;no_rooms=1&amp;group_children=0&amp;req_children=0&amp;hpos=1&amp;hapos=1&amp;sr_order=bayesian_review_score&amp;srpvid=f1d32eb0981d001f&amp;srepoch=1676183906&amp;from_beach_sr=1&amp;from_sustainable_property_sr=1&amp;from=searchresults#hotelTmpl" rel="noopener noreferrer" tabindex="-1" target="_blank"><img alt="The Oberoi Mumbai" class="b8b0793b0e" data-testid="image" height="200" src="https://cf.bstatic.com/xdata/images/hotel/square200/25339753.jpg?k=42a6601d184ad9e3fe25ec0d15a8605b7e135142479ae42a6265231dca33191f&amp;o=" width="200"/></a><div class="d226e2f1b9"><span class="cb5ebe3ffb"><button aria-expanded="false" class="f41101cd39" data-testid="wishlist-button"><span aria-hidden="true" class="b6dc9a9e69 c422d77911 ec1b6253a6" data-testid="wishlist-icon"><svg height="1em" viewbox="0 0 128 128" width="1em"><path d="M64 112a3.6 3.6 0 0 1-2-.5 138.8 138.8 0 0 1-44.2-38c-10-14.4-10.6-26-9.4-33.2a29 29 0 0 1 22.9-23.7c11.9-2.4 24 2.5 32.7 13a33.7 33.7 0 0 1 32.7-13 29 29 0 0 1 22.8 23.7c1.3 7.2.6 18.8-9.3 33.3-9.1 13.1-24 25.9-44.2 37.9a3.6 3.6 0 0 1-2 .5z"></path></svg></span></button></span></div></div></div><div class="b978843432"><div class="a1b3f50dcd a1f3ecff04 f7c6687c3d f996d8c258"><div class=""><div class="a1b3f50dcd a7c67ebfe5 b2fe1a41c3 d19ba76520 d14b211b4f"><div class="b1e6dd8416 aacd9d0b0a"><div class="a1b3f50dcd ef8295f3e6 f7c6687c3d"><div><div class="dd023375f5"><h3 class="a4225678b2"><a class="e13098a59f" data-testid="title-link" href="https://www.booking.com/hotel/in/the-oberoi-mumbai.en-gb.html?label=gen173nr-1FCAEoggI46AdIM1gEaGyIAQGYATG4ARfIAQzYAQHoAQH4AQKIAgGoAgO4ApyIn58GwAIB0gIkOTlmNTBmOGMtMjQxNi00MzhjLWI5MzgtZjljNDVlYjE5ZjAz2AIF4AIB&amp;sid=9c98c49eaafdd835f3efe6d3301b8fde&amp;aid=304142&amp;ucfs=1&amp;arphpl=1&amp;dest_id=-2092174&amp;dest_type=city&amp;group_adults=2&amp;req_adults=2&amp;no_rooms=1&amp;group_children=0&amp;req_children=0&amp;hpos=1&amp;hapos=1&amp;sr_order=bayesian_review_score&amp;srpvid=f1d32eb0981d001f&amp;srepoch=1676183906&amp;from_beach_sr=1&amp;from_sustainable_property_sr=1&amp;from=searchresults#hotelTmpl" rel="noopener noreferrer" target="_blank"><div class="fcab3ed991 a23c043802" data-testid="title">The Oberoi Mumbai</div><div class="e6e585da68">Opens in new window</div></a></h3><div class="f919b8b3d5"><span class="cb5ebe3ffb"><div aria-expanded="false" aria-label="5 out of 5" class="e4755bbd60" tabindex="0"><div aria-hidden="true" class="fbb11b26f5" data-testid="rating-stars" role="img"><span aria-hidden="true" class="b6dc9a9e69 adc357e4f1 fe621d6382"><svg viewbox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path d="M23.555 8.729a1.505 1.505 0 0 0-1.406-.98h-6.087a.5.5 0 0 1-.472-.334l-2.185-6.193a1.5 1.5 0 0 0-2.81 0l-.005.016-2.18 6.177a.5.5 0 0 1-.471.334H1.85A1.5 1.5 0 0 0 .887 10.4l5.184 4.3a.5.5 0 0 1 .155.543l-2.178 6.531a1.5 1.5 0 0 0 2.31 1.684l5.346-3.92a.5.5 0 0 1 .591 0l5.344 3.919a1.5 1.5 0 0 0 2.312-1.683l-2.178-6.535a.5.5 0 0 1 .155-.543l5.194-4.306a1.5 1.5 0 0 0 .433-1.661z"></path></svg></span><span aria-hidden="true" class="b6dc9a9e69 adc357e4f1 fe621d6382"><svg viewbox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path d="M23.555 8.729a1.505 1.505 0 0 0-1.406-.98h-6.087a.5.5 0 0 1-.472-.334l-2.185-6.193a1.5 1.5 0 0 0-2.81 0l-.005.016-2.18 6.177a.5.5 0 0 1-.471.334H1.85A1.5 1.5 0 0 0 .887 10.4l5.184 4.3a.5.5 0 0 1 .155.543l-2.178 6.531a1.5 1.5 0 0 0 2.31 1.684l5.346-3.92a.5.5 0 0 1 .591 0l5.344 3.919a1.5 1.5 0 0 0 2.312-1.683l-2.178-6.535a.5.5 0 0 1 .155-.543l5.194-4.306a1.5 1.5 0 0 0 .433-1.661z"></path></svg></span><span aria-hidden="true" class="b6dc9a9e69 adc357e4f1 fe621d6382"><svg viewbox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path d="M23.555 8.729a1.505 1.505 0 0 0-1.406-.98h-6.087a.5.5 0 0 1-.472-.334l-2.185-6.193a1.5 1.5 0 0 0-2.81 0l-.005.016-2.18 6.177a.5.5 0 0 1-.471.334H1.85A1.5 1.5 0 0 0 .887 10.4l5.184 4.3a.5.5 0 0 1 .155.543l-2.178 6.531a1.5 1.5 0 0 0 2.31 1.684l5.346-3.92a.5.5 0 0 1 .591 0l5.344 3.919a1.5 1.5 0 0 0 2.312-1.683l-2.178-6.535a.5.5 0 0 1 .155-.543l5.194-4.306a1.5 1.5 0 0 0 .433-1.661z"></path></svg></span><span aria-hidden="true" class="b6dc9a9e69 adc357e4f1 fe621d6382"><svg viewbox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path d="M23.555 8.729a1.505 1.505 0 0 0-1.406-.98h-6.087a.5.5 0 0 1-.472-.334l-2.185-6.193a1.5 1.5 0 0 0-2.81 0l-.005.016-2.18 6.177a.5.5 0 0 1-.471.334H1.85A1.5 1.5 0 0 0 .887 10.4l5.184 4.3a.5.5 0 0 1 .155.543l-2.178 6.531a1.5 1.5 0 0 0 2.31 1.684l5.346-3.92a.5.5 0 0 1 .591 0l5.344 3.919a1.5 1.5 0 0 0 2.312-1.683l-2.178-6.535a.5.5 0 0 1 .155-.543l5.194-4.306a1.5 1.5 0 0 0 .433-1.661z"></path></svg></span><span aria-hidden="true" class="b6dc9a9e69 adc357e4f1 fe621d6382"><svg viewbox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path d="M23.555 8.729a1.505 1.505 0 0 0-1.406-.98h-6.087a.5.5 0 0 1-.472-.334l-2.185-6.193a1.5 1.5 0 0 0-2.81 0l-.005.016-2.18 6.177a.5.5 0 0 1-.471.334H1.85A1.5 1.5 0 0 0 .887 10.4l5.184 4.3a.5.5 0 0 1 .155.543l-2.178 6.531a1.5 1.5 0 0 0 2.31 1.684l5.346-3.92a.5.5 0 0 1 .591 0l5.344 3.919a1.5 1.5 0 0 0 2.312-1.683l-2.178-6.535a.5.5 0 0 1 .155-.543l5.194-4.306a1.5 1.5 0 0 0 .433-1.661z"></path></svg></span></div></div></span></div><span class="cb5ebe3ffb"><span aria-expanded="false" data-testid="preferred-badge" role="button" tabindex="0"><span aria-hidden="true" class="b6dc9a9e69 c79b3cbed2 e6c50852bd fe3639fe67"><svg height="1em" viewbox="0 0 128 128" width="1em"><path d="M112 8H16a8 8 0 0 0-8 8v96a8 8 0 0 0 8 8h96a8 8 0 0 0 8-8V16a8 8 0 0 0-8-8zM48 96H24V58h24zm56-25a8.7 8.7 0 0 1-2 6 8.9 8.9 0 0 1 1 4 6.9 6.9 0 0 1-5 7c-.5 4-4.8 8-9 8H56V58l10.3-23.3a5.4 5.4 0 0 1 10.1 2.7 10.3 10.3 0 0 1-.6 2.7L72 52h23c4.5 0 9 3.5 9 8a9.2 9.2 0 0 1-2 5.3 7.5 7.5 0 0 1 2 5.7z"></path></svg></span></span></span></div></div><div><div class="a1fbd102d9" data-testid="location"><a class="fc63351294 a168c6f285 e0e11a8307 a25b1d9e47" href="https://www.booking.com/hotel/in/the-oberoi-mumbai.en-gb.html?label=gen173nr-1FCAEoggI46AdIM1gEaGyIAQGYATG4ARfIAQzYAQHoAQH4AQKIAgGoAgO4ApyIn58GwAIB0gIkOTlmNTBmOGMtMjQxNi00MzhjLWI5MzgtZjljNDVlYjE5ZjAz2AIF4AIB&amp;sid=9c98c49eaafdd835f3efe6d3301b8fde&amp;aid=304142&amp;ucfs=1&amp;arphpl=1&amp;dest_id=-2092174&amp;dest_type=city&amp;group_adults=2&amp;req_adults=2&amp;no_rooms=1&amp;group_children=0&amp;req_children=0&amp;hpos=1&amp;hapos=1&amp;sr_order=bayesian_review_score&amp;srpvid=f1d32eb0981d001f&amp;srepoch=1676183906&amp;from_beach_sr=1&amp;from_sustainable_property_sr=1&amp;from=searchresults&amp;map=1" rel="noopener noreferrer" target="_blank"><span><span class="f4bd0794db b4273d69aa" data-testid="address">South Mumbai, Mumbai</span><span class="f4bd0794db b4273d69aa">Show on map</span></span></a><span class="f4bd0794db"><span class="cb5ebe3ffb"><span aria-expanded="false" data-testid="distance">4.7 km from centre</span></span></span><span class="f4bd0794db"><span class="cb5ebe3ffb"><span aria-expanded="false">Beach nearby</span></span></span></div><div><span class="cb5ebe3ffb"><span aria-expanded="false"><span aria-hidden="true" class="b6dc9a9e69 b74301ff50 e25355d3ee"><svg viewbox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path d="M.153 22.237l.85 1.117c.634.76 1.724.856 2.456.244.078-.066.15-.138.216-.217l.944-1.132a.228.228 0 0 1 .349.001l.944 1.13a1.728 1.728 0 0 0 2.651.001l.944-1.132a.228.228 0 0 1 .349.001l.95 1.132a1.728 1.728 0 0 0 2.65 0l.942-1.133a.228.228 0 0 1 .349.001l.942 1.13a1.728 1.728 0 0 0 2.651.001l.944-1.132a.228.228 0 0 1 .349.001l.94 1.13a1.728 1.728 0 0 0 2.652.001l.585-.7a.75.75 0 1 0-1.15-.962l-.585.7a.228.228 0 0 1-.35 0l-.94-1.13a1.728 1.728 0 0 0-2.652-.001l-.944 1.132a.228.228 0 0 1-.349-.001l-.942-1.13a1.728 1.728 0 0 0-2.651-.001l-.943 1.132a.228.228 0 0 1-.348-.001l-.95-1.132a1.726 1.726 0 0 0-2.65 0l-.944 1.133a.228.228 0 0 1-.349-.001l-.944-1.13a1.728 1.728 0 0 0-2.65 0l-.945 1.13a.228.228 0 0 1-.349-.001l-.828-1.09a.75.75 0 1 0-1.194.91zm11.335-2.68A7.161 7.161 0 0 1 15.77 18h7.481a.75.75 0 0 0 0-1.5h-7.5a8.673 8.673 0 0 0-5.196 1.884.75.75 0 1 0 .934 1.174zM22.285 7.969a1.729 1.729 0 0 0 .781-2.711C19.43.713 12.8-.022 8.256 3.614a10.536 10.536 0 0 0-3.952 8.171A1.73 1.73 0 0 0 6.6 13.427l15.684-5.459zm-.494-1.416L6.107 12.01a.229.229 0 0 1-.304-.218 9.036 9.036 0 0 1 16.09-5.599.228.228 0 0 1-.102.359zm-9.459-4.2L11.69.504a.75.75 0 1 0-1.416.492l.643 1.848a.75.75 0 1 0 1.416-.492zm1.156 7.883l2.527 7.262a.75.75 0 1 0 1.416-.494l-2.527-7.26a.75.75 0 1 0-1.416.492z"></path></svg></span><span class="acb0d5ead1">2.9 km from beach</span></span></span></div></div><div><span class="d8eab2cf7f c9fccc9041 a7c6818653 d226e198ab"><span class="e2f34d59b1"><span class="cb5ebe3ffb fba3ab9fd6"><span aria-expanded="false" data-testid="travel-sustainable-badge"><span aria-hidden="true" class="b6dc9a9e69 f8ecfc5adb 6.019-6.088-53.686 9.893-67.35 23.384-10.689 10.585-11.346 29.68-1.487 40.784l42.236-42.064c1.315-1.28 3.079-1.107 4.047-.138 1.004.968 1.453 2.802-.138 4.324L21.409 76.174 9.752 87.797l-.415.415c-.277.276-.45.553-.554.9-.38 1.002-.173 2.213.623 3.009 1.107 1.141 2.94 1.141 4.082 0l9.27-9.202c11.623 6.434 25.91 4.878 36.356-4.946Z"></path></svg></span><span aria-hidden="true" class="b6dc9a9e69 f8ecfc5adb e25355d3ee"><svg viewbox="0 0 94 94" xmlns="http://www.w3.org/2000/svg"><path d="M59.114 77.973c13.595-12.765 29.61-60.64 23.626-66.555-6.019-6.088-53.686 9.893-67.35 23.384-10.689 10.585-11.346 29.68-1.487 40.784l42.236-42.064c1.315-1.28 3.079-1.107 4.047-.138 1.004.968 1.453 2.802-.138 4.324L21.409 76.174 9.752 87.797l-.415.415c-.277.276-.45.553-.554.9-.38 1.002-.173 2.213.623 3.009 1.107 1.141 2.94 1.141 4.082 0l9.27-9.202c11.623 6.434 25.91 4.878 36.356-4.946Z"></path></svg></span><span aria-hidden="true" class="b6dc9a9e69 f8ecfc5adb e25355d3ee"><svg viewbox="0 0 94 94" xmlns="http://www.w3.org/2000/svg"><path d="M59.47 78.707c13.523-12.983 29.235-61.434 23.202-67.39-6.066-6.124-53.665 10.34-67.222 24.058-10.64 10.741-11.17 30.048-1.26 41.19l-8.32 8.433-.132.133c-.298.335-.43.603-.563.97-.365 1.038-.166 2.21.663 3.046a2.862 2.862 0 0 0 4.077 0l8.386-8.5c12.297 10.039 29.335 9.47 41.201-1.94h-.033Zm-37.655-1.673 38.417-39.049c1.59-1.54 1.094-3.38.1-4.35-.995-1.004-2.785-1.171-4.078.167l-38.517 39.15c-6-8.265-8.353-24.025 1.69-33.394 17.734-16.563 55.157-24.192 59.168-24.126.53 1.874-6.795 41.826-23.203 59.193-10.54 11.142-25.258 8.198-33.61 2.409h.032Z"></path></svg></span><span class="d8eab2cf7f be09c104ad">Travel Sustainable Level 2</span></span></span></span></span></div><div class="d8eab2cf7f">Centrally located in Mumbai's business district, close to South Mumbai's shopping and entertainment areas, The Oberoi offers luxury and convenience with its outdoor heated pool, 24-hour spa, fitness...</div></div></div><div class=""><div class="a1b3f50dcd bdf0df2d01 f7c6687c3d f920833fe5"><div class=""><div class="a1b3f50dcd ef8295f3e6 f7c6687c3d"><a class="fc63351294 a168c6f285 aeccf0d865 a25b1d9e47" data-testid="review-score-link" href="https://www.booking.com/hotel/in/the-oberoi-mumbai.en-gb.html?label=gen173nr-1FCAEoggI46AdIM1gEaGyIAQGYATG4ARfIAQzYAQHoAQH4AQKIAgGoAgO4ApyIn58GwAIB0gIkOTlmNTBmOGMtMjQxNi00MzhjLWI5MzgtZjljNDVlYjE5ZjAz2AIF4AIB&amp;sid=9c98c49eaafdd835f3efe6d3301b8fde&amp;aid=304142&amp;ucfs=1&amp;arphpl=1&amp;dest_id=-2092174&amp;dest_type=city&amp;group_adults=2&amp;req_adults=2&amp;no_rooms=1&amp;group_children=0&amp;req_children=0&amp;hpos=1&amp;hapos=1&amp;sr_order=bayesian_review_score&amp;srpvid=f1d32eb0981d001f&amp;srepoch=1676183906&amp;from_beach_sr=1&amp;from_sustainable_property_sr=1&amp;from=searchresults#hotelTmpl" rel="noopener noreferrer" target="_blank"><span><div class="a1b3f50dcd a1f3ecff04 cbb2d85c33 db7f07f643 d19ba76520 d02f1578ba d17b3fe5e2" data-testid="review-score"><div aria-label="Scored 9.3 " class="b5cd09854e d10a6220b4">9.3</div><div class="b1e6dd8416 aacd9d0b0a b48795b3df"><div aria-label="Superb" class="b5cd09854e f0d4d6a2f5 e46e88563a">Superb<!-- --> </div><div class="d8eab2cf7f c90c0a70d3 db63693c62">1,184 reviews</div></div></div></span></a><div><a aria-label="Location: Scored 9.5 " class="fc63351294 a168c6f285 a25b1d9e47" data-testid="secondary-review-score-link" href="https://www.booking.com/hotel/in/the-oberoi-mumbai.en-gb.html?label=gen173nr-1FCAEoggI46AdIM1gEaGyIAQGYATG4ARfIAQzYAQHoAQH4AQKIAgGoAgO4ApyIn58GwAIB0gIkOTlmNTBmOGMtMjQxNi00MzhjLWI5MzgtZjljNDVlYjE5ZjAz2AIF4AIB&amp;sid=9c98c49eaafdd835f3efe6d3301b8fde&amp;aid=304142&amp;ucfs=1&amp;arphpl=1&amp;dest_id=-2092174&amp;dest_type=city&amp;group_adults=2&amp;req_adults=2&amp;no_rooms=1&amp;group_children=0&amp;req_children=0&amp;hpos=1&amp;hapos=1&amp;sr_order=bayesian_review_score&amp;srpvid=f1d32eb0981d001f&amp;srepoch=1676183906&amp;from_beach_sr=1&amp;from_sustainable_property_sr=1&amp;from=searchresults#hotelTmpl" rel="noopener noreferrer" target="_blank"><span><span class="f9afbb0024">Location<!-- --> <!-- -->9.5</span></span></a></div></div></div><div><span class="cb5ebe3ffb"><button aria-expanded="false" class="fc63351294 a822bdf511 d4b6b7a9e7 f7db01295e c334e6f658 f4605622ad" type="button"><span class="e57ffa4eb5">Show prices</span></button></span></div></div></div></div></div></div></div></div><div class="ea6e492f88"></div></div>     
``` 
</details>
<br>

* Giving a variable name to the tag code
```python
 # For First Block
section = soup.select(".a826ba81c4.fe821aea6c.fa2f36ad22.afd256fc79.d08f526e0d.ed11e24d01.ef9845d4b3.da89aeb942")[0]
```
## Select Second Anchor Tag in First Block to find the names of hotels

```python
anchor = section.select("a")[1] 
anchor
```
<details>
  <summary>Output</summary>
<br>

```
  <a class="e13098a59f" data-testid="title-link" href="https://www.booking.com/hotel/in/oyo-76305-a-s-suite.en-gb.html?label=gen173nr-1FCAEoggI46AdIM1gEaGyIAQGYATG4ARfIAQzYAQHoAQH4AQKIAgGoAgO4ApyIn58GwAIB0gIkOTlmNTBmOGMtMjQxNi00MzhjLWI5MzgtZjljNDVlYjE5ZjAz2AIF4AIB&amp;sid=9c98c49eaafdd835f3efe6d3301b8fde&amp;aid=304142&amp;ucfs=1&amp;arphpl=1&amp;dest_id=-2092174&amp;dest_type=city&amp;group_adults=2&amp;req_adults=2&amp;no_rooms=1&amp;group_children=0&amp;req_children=0&amp;hpos=25&amp;hapos=775&amp;sr_order=bayesian_review_score&amp;srpvid=d8562fde24dd000a&amp;srepoch=1676184510&amp;from_beach_sr=1&amp;from=searchresults#hotelTmpl" rel="noopener noreferrer" target="_blank"><div class="fcab3ed991 a23c043802" data-testid="title">Hotel A S Suite</div><div class="e6e585da68">Opens in new window</div></a>
```

</details>
<br>

```python
# Select the first div tag
div = anchor.select("div")[0]
# Get the name of hotel
name = div.getText()
name
```
``
'The Oberoi Mumbai'
``

# Function for Hotel Name

* We made a complete function to get the hotel name from particular block

```python
def get_hotel_name(section):
    """
    Function gives us the hotel name 
    """
    anchor = section.select("a")[1]
    div = anchor.select("div")[0]
    name = div.getText()
    return name
  ```
  
  ## Select the Third Anchor Tag in First Block to find the location of hotels

```python
anchor = section.select("a")[2]
anchor
```

<details>
  <summary>Output</summary>
<br>

```
 <a class="fc63351294 a168c6f285 e0e11a8307 a25b1d9e47" href="https://www.booking.com/hotel/in/the-oberoi-mumbai.en-gb.html?label=gen173nr-1FCAEoggI46AdIM1gEaGyIAQGYATG4ARfIAQzYAQHoAQH4AQKIAgGoAgO4ApyIn58GwAIB0gIkOTlmNTBmOGMtMjQxNi00MzhjLWI5MzgtZjljNDVlYjE5ZjAz2AIF4AIB&amp;sid=9c98c49eaafdd835f3efe6d3301b8fde&amp;aid=304142&amp;ucfs=1&amp;arphpl=1&amp;dest_id=-2092174&amp;dest_type=city&amp;group_adults=2&amp;req_adults=2&amp;no_rooms=1&amp;group_children=0&amp;req_children=0&amp;hpos=1&amp;hapos=1&amp;sr_order=bayesian_review_score&amp;srpvid=f1d32eb0981d001f&amp;srepoch=1676183906&amp;from_beach_sr=1&amp;from_sustainable_property_sr=1&amp;from=searchresults&amp;map=1" rel="noopener noreferrer" target="_blank"><span><span class="f4bd0794db b4273d69aa" data-testid="address">South Mumbai, Mumbai</span><span class="f4bd0794db b4273d69aa">Show on map</span></span></a>

```

</details>
<br>

```python
# Select the first span tag
span = anchor.select("span")
location = span[1].getText()
location
```
``
'South Mumbai, Mumbai'
``

# Function for Hotel location


```Python
def get_location(section):
    """
    Function gives us the location info
    """
    anchor = section.select("a")[2]
    span = anchor.select("span")
    location = span[1].getText()
    return location
```

# We loop through to get data from all pages

```Python
# Setting up the URL and headers
url ="https://www.booking.com/searchresults.html?label=gen173nr-1FCAEoggI46AdIM1gEaGyIAQGYATG4ARfIAQzYAQHoAQH4AQKIAgGoAgO4ApyIn58GwAIB0gIkOTlmNTBmOGMtMjQxNi00MzhjLWI5MzgtZjljNDVlYjE5ZjAz2AIF4AIB&sid=9c98c49eaafdd835f3efe6d3301b8fde&aid=304142&sb_lp=1&error_url=https%3A%2F%2Fwww.booking.com%2Findex.html%3Flabel%3Dgen173nr-1FCAEoggI46AdIM1gEaGyIAQGYATG4ARfIAQzYAQHoAQH4AQKIAgGoAgO4ApyIn58GwAIB0gIkOTlmNTBmOGMtMjQxNi00MzhjLWI5MzgtZjljNDVlYjE5ZjAz2AIF4AIB%26sid%3D9c98c49eaafdd835f3efe6d3301b8fde%26sb_price_type%3Dtotal%26%26&ss=Mumbai&is_ski_area=0&ssne=Mumbai&ssne_untouched=Mumbai&dest_id=-2092174&dest_type=city&checkin_year=&checkin_month=&checkout_year=&checkout_month=&group_adults=2&group_children=0&no_rooms=1&b_h4u_keep_filters=&from_sf=1&order=bayesian_review_score&offset={}"
headers = {'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36'}

# Creating empty dictonary for storing 
scrape_dict = new_data_dict()

# Looping through all the content from 0-1000
for num in range(0,775,25):
    
    # Request for the URL and make soup
    soup = get_page(url,headers,num)
    
    # Get the sections
    sections = soup.select(".a826ba81c4.fe821aea6c.fa2f36ad22.afd256fc79.d08f526e0d.ed11e24d01.ef9845d4b3.da89aeb942")
    
    # For all the blocks
    for section in sections:
        
        # Get and add the details to dictionary
        scrape_dict["Name"].append(get_hotel_name(section))
        scrape_dict["Place"].append(get_location(section))
      
```


# Saving the data to a dataframe

```python
df = pd.DataFrame(scrape_dict)
```

## To save the Data Frame to csv file

```python
df.to_csv("hotel-list.csv")
```

# Here is the Csv file 

[hotel-list.csv](https://github.com/saksham-mishra24/WEB-SCRAPING/files/10715627/hotel-list.csv)

