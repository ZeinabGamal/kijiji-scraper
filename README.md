# Welcome to kijiji.ca scraper project

## The full source code is private since it's a paid software, contact me for more details

### In this page i will talk about some parts of the code including a sample from the source code.

**Starting from this url** : https://www.kijiji.ca/v-electronics/calgary/motorola-xpr-7580e-is-portable-two-way-radio-csa/1341602389

The following code is written in **python 3.6.5** and **Selenium** which is a module for scraping and automating.

![ad](https://raw.githubusercontent.com/ZeinabGamal/kijiji-scraper/master/ad%20page.PNG)

#### Basically the full script is enters keywords in the search bar and scrape all the ads available and exports the output to a csv file

#### But in this page i will present only the part of scraping one Ad page and not all of the website

The script will open google chrome using the chrome driver then visits an Ad page and then will extract any details e.g. name, email, website, descritpion, ...etc

#### First import the needed modules

```python
from time import sleep
from selenium.webdriver.common.keys import Keys
from selenium import webdriver
from parsel import Selector
import csv
import os.path
from collections import defaultdict
import re

```

#### Get Title

```python
def get_title():
    try:
        title = driver.find_element_by_css_selector('div[class="itemTitleWrapper-2315792072"] div div h1[itemprop="name"]').text
    except:
        title = "None"
        pass
    return title

```

#### parse phone

```python
ddef get_phone():
    
    try:
        click_phone_button = driver.find_element_by_css_selector('span[aria-hidden="true"]').click()
        sleep(2)

        phone = driver.find_element_by_class_name('phoneShowNumberButton-4085241536').text
    except:
        phone = "None"
        pass

    if phone == "None":

        try: 
            sel = Selector(text=driver.page_source)
            description = sel.css('div[itemprop="description"] *::text').extract()
            description = "".join(description)

            for match in re.finditer(r"\(?\b[2-9][0-9]{2}\)?[-. ]?[2-9][0-9]{2}[-. ]?[0-9]{4}\b", description):
                
                phone = match.group()
        except:
            phone = "None"
            pass
    
    return phone

```

#### Get Contact Name

```python
def get_contact_name():
    try:
        contact_name = driver.find_element_by_class_name('profileHeader-81636252').text

        if contact_name == 'About the Poster':
            contact_name = "None"
    except:
        contact_name = "None"
        pass

    return contact_name       

```

#### Get Website URL

```python
def get_website_url():
    try:
        child_of_website_url = driver.find_element_by_xpath("//*[contains(text(), 'Visit website')]")

        parent_of_website = child_of_website_url.find_element_by_xpath("..")

        website_url = parent.get_attribute("href")
    except:
        website_url = "None"
        pass
    return website_url

```

#### Get Location

```python
def get_location():
    try:
        location = driver.find_element_by_css_selector('span[itemprop="address"]').text
    except:
        location = "None"
        pass 
    if location == "None":
        try:
            sel = Selector(text=driver.page_source)
            description = sel.css('div[itemprop="description"] *::text').extract()
            description = "".join(description)

            address = re.compile(r'[T]\d[A-Z] *\d[A-Z]\d')

            location = address.search(description).group()    
        except:
            location = "None"
            pass

    return location

```

#### Finally lets parse the page then print the results and also write them to a csv file

```python
driver = webdriver.Chrome('C:\chromedriver\chromedriver.exe')
driver.get("https://www.kijiji.ca/v-electronics/calgary/motorola-xpr-7580e-is-portable-two-way-radio-csa/1341602389")
sleep(2)
Title = get_title()

Phone_number = get_phone()

Contact_name = get_contact_name()

Website = get_website_url()

Ad_id = get_ad_id()

Date_of_post = get_date_of_post()

Location = get_location()

On_kijiji_since = get_on_kijiji_since()

Description = get_description()

Email = get_email()

Writing_to_csv(Title,Contact_name,Phone_number,Email,Website,Description,Date_of_post,On_kijiji_since,Location,Ad_id)

```

#### The following csv screenshot containing a sample output from scraping 3 Ads Pages

![csv](https://raw.githubusercontent.com/ZeinabGamal/kijiji-scraper/master/csv%20results.PNG)
