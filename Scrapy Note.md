# Scrapy Note

### Install
```txt
# requirements.txt
lxml
parsel
w3lib
Twisted
cryptography
pyOpenSSL
Scrapy
ipython
```

```bash
pip install -r requirements.txt
```

### Useful commands
```bash
(py3) huan@huandeMBP ~ % scrapy
Scrapy 1.4.0 - no active project

Usage:
  scrapy <command> [options] [args]

Available commands:
  bench         Run quick benchmark test
  fetch         Fetch a URL using the Scrapy downloader
  genspider     Generate new spider using pre-defined templates
  runspider     Run a self-contained spider (without creating a project)
  settings      Get settings values
  shell         Interactive scraping console
  startproject  Create new project
  version       Print Scrapy version
  view          Open URL in browser, as seen by Scrapy

  [ more ]      More commands available when run from project directory

Use "scrapy <command> -h" to see more info about a command
```


```bash
scrapy fetch http://www.baidu.com
```


### Shell command

#### 1. Create project
```bash
mkdir project
cd porject

# make a project and see what has beed created
scrapy startproject worldometers # make a project named worldometers
cd worldometers
ls
>>> scrapy.cfg	wordometers
```

Two things created, a config file and a folder having same name as project folder
```scrapy.cfg
# Automatically created by: scrapy startproject
#
# For more information about the [deploy] section see:
# https://scrapyd.readthedocs.org/en/latest/deploy.html

[settings]
default = wordometers.settings

[deploy]
#url = http://localhost:6800/
project = wordometers
```

```wordometers
__init__.py	 items.py	 pipelines.py	 spiders
__pycache__	middlewares.py	settings.py
```

#### Create spider

Inside project folder, generate a spider

**Not use http: in the front**
**Not ends with /**, e.g. www.abs.def/ is not allowed
```bash
scrapy genspider countries www.worldometers.info/world-population/population-by-country
```


Inside `countries.py`

no `http://` in allowed domains
```python
import scrapy


class CountriesSpider(scrapy.Spider):
    name = 'countries'
    allowed_domains = ['www.worldometers.info/'] # no http://here
    start_urls = ['https://www.worldometers.info/world-population/population-by-country/']

    def parse(self, response):
        pass


```
```bash
>>> tree
wordometers # create spider inside this folder
├── scrapy.cfg
└── wordometers
    ├── __init__.py
    ├── items.py
    ├── middlewares.py
    ├── pipelines.py
    ├── settings.py
    └── spiders
        ├── __init__.py
        └── countries.py
```

#### Using scrapy shell

Example website
https://www.worldometers.info/world-population/population-by-country/


```bash
scrapy shell

>>> fetch('https://www.worldometers.info/world-population/population-by-country/')
>>> response.body
>>> response.xpath('//a/text()').getall()

# or
r = scrapy.Request('https://www.worldometers.info/world-population/population-by-country/')
fetch(r)

title = response.xpath('//h1/text()')
title.get()
>>>  'Countries in the world by population (2020)'


title_css = response.css('h1::text')
title_css.get()
>>> 'Countries in the world by population (2020)'


# get all country names
all_countries = response.xpath('//tbody//a/text()').getall()
>>> alist_of_country_names
```

#### Creating spider

parse results
```python
import scrapy

class CountriesSpider(scrapy.Spider):
    name = 'countries'
    allowed_domains = ['www.worldometers.info/world-population/population-by-country']
    start_urls = ['https://www.worldometers.info/world-population/population-by-country/']

    def parse(self, response):
        title = response.xpath('//h1/text()').get()
        countries = response.xpath('//tbody//a/text()').getall()
        yield {'title': title, 'countries': countries}
```


start spider
```bash
# on the same level as scrapy.cfg file
(venv) huan@192 worldometers % ls    
scrapy.cfg      worldometers

# start crawler
scrapy crawl countries
```

### Css selector
[Try jsoup online: Java HTML parser and CSS debugger](https://try.jsoup.org/)

`#` for id `.` for class
```
# Two classes in one tag
<p class="bold italic">

.bold.italic

# select tag with other attributes
<li data-identifier="7">
li[data-identifier=7]

# startswith and endswith
<a href="https://www.google.com">
a[href^='https']

<a href="http://www.google.fr">
a[href$='fr']

```

### xpath

[XPath Playground](https://scrapinghub.github.io/xpath-playground/)

#scrapy
#python