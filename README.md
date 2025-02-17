openlibrary-client
==================

![Travis CI build status](https://travis-ci.org/internetarchive/openlibrary-client.svg?branch=master)

A reference client library for the Open Library API. Tested with Python 3.7, 3.8, and 3.9.

- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Testing](#testing)
- [Other Client Libraries](#other-client-libraries)

## Installation

To install the openlibrary-client package:
```
$ git clone https://github.com/internetarchive/openlibrary-client.git
$ cd openlibrary-client
$ pip install .
```

## Configuration

Many Open Library actions (like creating Works and Editions) require authentication, i.e. certain requests must be provided a valid cookie of a user which has been logged in with their openlibrary account credentials.  The openlibrary-client can be configured to "remember you" so you don't have to provide credentials with each request.

First time users may run the following command to enable the "remember me" feature. This process will ask for an **Archive.org email and password**, will authenticate the credentials, and then store the account's corresponding s3 keys in `~/.config/ol.ini` (or whichever config location the user has specified):

```
$ ol --configure --email mek@archive.org
password: ***********
Successfully configured
```

### Using Keys Directly
The ol.ini has two variables, access and secret. If you have both of them, you can manually initialise them 
```
from olclient import OpenLibrary, config
ol = OpenLibrary(credentials=config.Credentials(access='<access>', secret='<secret>'))
```
This way, access and secret can be pulled from environment variables at runtime!

## Usage

### Python Library

For more examples, you can take a look at our [examples directory](examples/scripts) on Python scripts for specific use cases that are needed.

### Google Colab

You can view interactive documentation of openlibrary-client at this [Google Colab document.](https://colab.research.google.com/drive/10prZ3JwaV1ATJiR_xC6Y-oU7ApRHwaxG?usp=sharing)

#### Adding a new Book

Fun things you can do to add a new book to Open Library
```python
>>> from olclient.openlibrary import OpenLibrary
>>> import olclient.common as common
>>> ol = OpenLibrary()
>>> book = common.Book(title=u"Warlight: A novel", authors=[common.Author(name=u"Michael Ondaatje")], publisher=u"Deckle Edge", publish_date=u"2018")
>>> book.add_id(u'isbn_10', u'0525521194')
>>> book.add_id(u'isbn_13', u'978-0525521198')
>>> new_book = ol.create_book(book)
>>> new_book.add_bookcover('https://images-na.ssl-images-amazon.com/images/I/51kmM%2BvVRJL._SX337_BO1,204,203,200_.jpg')
```

#### Works

Fun things you can do with an Work:

```python
>>> from olclient.openlibrary import OpenLibrary
>>> ol = OpenLibrary()
>>> work = ol.Work.get(u'OL12938932W')
>>> editions = work.editions
```
One thing to consider in the snippet above is that work.editions is a @property which makes several http requests to OpenLibrary in order to populate results. Once a call has been made to work.editions, its editions are saved/cached as work._editions_.


#### Editions

Fun things you can do with an Edition:
```python
>>> from olclient.openlibrary import OpenLibrary
>>> ol = OpenLibrary()
>>> edition = ol.Edition.get(u'OL25952968M')
>>> authors = edition.authors
>>> work = edition.work
>>> work.add_bookcover(u'https://covers.openlibrary.org/b/id/7451891-L.jpg')
>>> edition.add_bookcover(u'https://covers.openlibrary.org/b/id/7451891-L.jpg')
```

#### Authors

Author Information for existing authors can be done in the following manner.
```python
>>> from olclient.openlibrary import OpenLibrary
>>> ol = OpenLibrary()
>>> author_olid = ol.Author.get_olid_by_name('Dan Brown')
>>> author_obj = ol.get(author_olid)
```

### Command Line Tool

Installing the openlibrary-client library will also install the `ol` command line utility. Right now it does exactly nothing.

```
    $ ol

usage: ol [-h] [-v] [--configure] [--get-work] [--get-book] [--get-olid]
          [--olid OLID] [--isbn ISBN] [--create CREATE] [--title TITLE]
          [--baseurl BASEURL] [--email EMAIL]

olclient

optional arguments:
  -h, --help         show this help message and exit
  -v                 Displays the currently installed version of ol
  --configure        Configure ol client with credentials
  --get-work         Get a work by --title, --olid
  --get-book         Get a book by --isbn, --olid
  --get-olid         Get an olid by --title or --isbn
  --olid OLID        Specify an olid as an argument
  --isbn ISBN        Specify an isbn as an argument
  --create CREATE    Create a new work from json
  --title TITLE      Specify a title as an argument
  --baseurl BASEURL  Which OL backend to use
  --email EMAIL      An IA email for requests which require authentication.
                     You will be prompted discretely for a password
```

You can create a new work from the command line using the following syntax. It's almost identical to the olclient.common.Book object construction, except instead of providing an Author object, you instead pass a key for "author" and a corresponding value:

```
> ol --create '{"title": "The Cartoon Guide to Calculus", "publisher": "Teach Yourself", "publish_date": "2013", "identifiers": {"isbn_10": ["144419111X"]}, "cover": "https://images-na.ssl-images-amazon.com/images/I/51aJdEGttLL._SX328_BO1,204,203,200_.jpg", "author": "Hugh Neill"}'
OL26194598M
```

Successful creation of a new Work results in the return of its Open Library edition ID.

## Testing

To run test cases (from the openlibrary-client directory):

```
$ pytest
```

## Other Client Libraries

Other Open Library client libraries include:
- Ruby: https://github.com/jayfajardo/openlibrary
- Javascript: https://github.com/onaclovtech/openlibrary
- Python: https://github.com/felipeborges/python-openlibrary and https://github.com/the-metalgamer/python-openlibrary-client
- PHP: https://github.com/beezus/openlibrary-php
