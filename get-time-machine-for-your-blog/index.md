```.header
Title: Get A Time Machine For You Blog
Subtitle: This is not yet another attempt to build a time machine - we actually made it
Description: A microservice responsible for our blog content and other documentation for public usage, which enables a time machine for all our content.
Image: delorean.jpg
Author: Djordje Zekovic @zekome
Date: 22 February 2017
Tags: microservices
```

![Get A Time Machine For You Blog](delorean.jpg)

We have implemented a microservice responsible for our blog content and other documentation for public usage. That little piece of independent software enables a time machine for all our content. Management of content is tracked with Git versioning system. It’s a public and transparent way of dealing with changes - which might be an additional benefit.

## Time machine

It is an interesting feature of our microservice - a time machine for travelling through different versions of all content, including images. The Delorian machine drives back to the past with a return ticket. The rule is - you can’t touch anything, but you can take a look.

When you share a link with your friends on social networks, you certainly don’t want shared content to change, but to remain just like it was at the moment you have shared it. **Just append @date at the end of link address and time-machine guarantees exactly version from that day.**
 
Authors don’t need anymore to leave update notes at the end of blog entry. Simply, they maintain just the latest version of the information. When someone is surfing the history the system will display notification at the top of web page saying that there is a newer version.

A @date appendix can take different formats:
- @20170220 - version on day 02/20/2017
- @201702 - version on Feb the 1st, 2017
- @2017 - version on the first day of year 2017.

## Significance of implementation for us

Data is stored on GitHub’s infrastructure. There is no more databases in our system. There are just two components - a web server and adjusted structure of information. We expand system with new web servers, while data is loaded into RAM on server boot.

Now, we can with ease bring a new instance of web server within our cluster group for a specific time period - until we handle current load and then shut it down after. It costs us just few instance/hours. The system before was more expensive. We had to reserve capacity in advance and to pay the same price no matter what was the current traffic load. Expenses goes even higher with high-availability DB deployments. It’s good not having this kind of expenses anymore.

With the same ease we can place server instances in different regions across the world, not considering DB replication between regions anymore - simple because we don’t have DB anymore. Along side with that, we get much faster response time between users and our web server - faster web site opening time.

An implementation of new content engine means savings measured in dollars per hour and less complex system administration.

We ask our co-workers to send us a document in standard Markdown format, and programmers to send us Pull-request on GitHub. No more burden of CMS administration. Everything is much simpler now.
 
*REMINDER: An architecture of our web site is based on three layers with microservices in the backend. The first layer is responsible for access security and URL based routing. The middleware renders HTML responses based on predefined templates. And microservices layer is just serving the data, while in the backend takes care of all logic necessary to maintain the right structure of all kind of information. All layers can be expanded on per need.*

## Functional description

The main functionality of software is automatically loading and archiving of all versions of content from specified Git repository and adjusting structure of loaded data for easy usage in web application. The archive’s base entity is a *document*, which consists of text, attached images, and other files.

## Collaborative content management 

Document can be crafted through collaboration of more users using Git tool. Editorial process is separate from the software logic. One use can prepare the first version of the content, someone can insert images with illustration, other users can make the translation, and at the end, responsible editors can make final changes and publish the document. Everything can roll out in public with GitHub repositories, or in private - for example, over BitBucket repositories. When a change of document has been committed, the system receives a notification, and then it loads all changes.

## Markdown for text styling

Text of document can be styled using Markdown language. You can use any Markdown editor for writing. We use GitHub’s built-in Markdown editor, which comes with preview for styled content. Beyond standard GitHub Markdown syntax, we introduced some of our own markdown elements. It’s an element for video injection and document headers containing fundamental meta data. We can always upgrade with new markdown elements if needed. A microservice renders Markdown to HTML on the fly. It enables injection of dynamic content in document - like current number of logged-in users, weather forecast, or maybe even an Ad. Clients using microservice can provide additional data within calling context of API call, which will be included at predefined Markdown placeholders.

## Document basics
A document is uniquely defined with its directory path. A directory path is relative within an archive, but can be mapped to some part of URL address. Inside directory you can upload images and other related files, which you can link in document using Markdown syntax. The text of document must be saved to “index.md” file in the same directory as other related files. The rules are similar to those general ones that applies to directories and index files on standard web servers. Every change to every files represents a new version of a document.

Every document contains a header with meta data. A header section includes: Title, Subtitle, Description, DateTime, Authors, oggraph attributes, classification, and other tags. You can create a document draft, or hide it from public index.

<pre>
```.header
Title: Get A Time Machine For You Blog
Subtitle: This is not yet another attempt to build a time machine - we actually made it
Description: A microservice responsible for our blog content and other documentation for public usage, which enables a time machine for all our content.
Image: delorean.jpg
Author: Djordje Zekovic @zekome
Date: 22 February 2017
Tags: microservices
```
</pre>

## Multi-lingual support

A document has a multi-lingual support. You can use language directives in header together with reference language version of that particular document. Language information is later available on every API call for the purpose of creating Google *hreflang* tags for multi-linugal and multi-regional web pages.

By appending a date at the end of URL address, we potentially  create a lot of links to the same content, but different versions in time. The question is how will Google and other web crawlers index those versions-in-time of the same web page. We solved it by adding the same canonical link to every version-in-time web page. That way Google will index only the latest version of a web page, no matter how many versions-in-time of the same web page has been shared over the Internet.


## Overall consistency on specific date

Time machine doesn’t apply only to how particular document looked like on specific date. It provides a functionality to show a complete blog with all related articles in respect to given date. That means that *Related articles* and *Archive Index* are all viewed from specified time period point. The common ground for this system behavior can be found in that the one article can be related to other only if both articles exist on given date - what was related to what on that day. 

Besides tracking of text changes in document, the microservice keeps record of every change in all related files. It includes images too. If someone changes a text inside an image, such change will be saved and available inside an archive.


## Possibilities of integration

The microservice runs as a separate process on the system. It enables API integration with existing systems no matter what’s the technology behind those systems. It can be part of your system as well.

## Development roadmap

Currently we keep data inside RAM memory and downloading content from Git repository every time on system load. It’s acceptable regarding the size of our blog and other documentation at this particular moment. For some larger repositories it would be necessary to implement local SSD storage and make RAM cache for frequently used data.

We don’t support search. The content is published in public on the Web and thus it’s indexed by Google. The search is performed by integrated Google CSE. It’s possible that we’ll upgrade microservice with some search capabilities within interesting logical parts of a document (excerpts, images, body etc.).


## Archive insights

Our arhive source can be reached on this link:

[https://github.com/zekome/blog](https://github.com/zekome/blog)

Everyone can contribute with new content using Git Pull requests. We have no liability to accept every change request. Everything is so transparent, which represents a value as a process itself. It’s great.

## At the end … of URL address

Would you like to share this article? Maybe you would consider appending today’s date at the end of URL address so you could freeze the current version of this article for your friends and yourself. If something changes, you’ll see the notification on top of the web page, so you could check-out the latest version.






