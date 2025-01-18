## Usages
- Search engine indexing: Googlebot is an example
- Web archiving: saving website information as archives for future uses
- Web Mining: Data mining to get info and generate analytics and reports for business
- Web monitoring: To check copyright and trademark infringements


## Requirements
1. Given a set of URLs, download all the web pages
2. Extract URLs from these website
3. Add new URLs to the list of URLs
#### Clarifications
1. Usage?                                                      - Search engine indexing
2. Web pages per month?                           - 1 billion
3. Content type?                                           - HTML only but extensible in future
4. Data Updated?                                         - newly added or updated website should be handled
5. Data retention?                                         - 5 years
6. Duplicate Pages?                                     - Should be ignored
7. [[Scalability]]?                                                - Very large system
9. Robustness?                                             - Should be able to handle traps, errors, crashes
10. Politeness?                                              - Should not continuously call same domain (DOS)
11. Extensibility?                                           - Extensible to other data with minimal changes


## [[Back of the envelope calculations]]
- 1 billion pages per month
- 1,000,000,000 / 30 / 24 / 3600 = ~400 pages per second
- Peak QPS = 2 x 400 = 800
- Data retention for 5 years
- Assuming each page as 500k
- Storage for 5 years = 1,000,000,000 x 12 x 5 x 500,000 = 30 PB

## High level design
##### Seed URLs
- Initial URLs to start crawling from.
- To crawl the entire web, creative strategies based on locality, topics like shopping, sports, healthcare etc. can be chosen.
##### URL Frontier
- Component to store to-be downloaded URLs
- Follows FIFO queue strategy
##### HTML Downloader
- Downloads web page.
##### DNS resolver
- Resolves the URLs to IP address
##### Content parser
- Checking if a downloaded web page for malformed.
- Separate component than crawl server to save time
##### Content Seen?
- Checks if the web page is a duplicate of already downloaded web page
- 29% of web pages are duplicate according to a study
- Comparing complete strings is slow, hence hash values of web pages are compared
##### Content Storage
- Both disk and memory can be used for efficiency and reliability
##### URL Extractor
- Extract all the links on a webpage and add it to parent prefix domain to form complete links
##### URL Filter
- Excludes certain content types, file extensions, error links, Blacklisted site URLs.
##### URL Seen?
- A bloom filter or hash table implementation to check if a URL has already been visited
##### URL Extractor
- Extract all the links on a webpage and add it to parent prefix domain to form complete links
##### URL Storage
- Store already visited URLs
##### URL Extractor
- Extract all the links on a webpage and add it to parent prefix domain to form complete links

##### <ins>Web crawler workflow</ins>
 1. Add seed URLs to the URL Frontier 
 2. HTML Downloader fetches a list of URLs from URL Frontier.
 3. HTML Downloader gets IP addresses of URLs from DNS resolver and starts  downloading.
 4. Content Parser parses HTML pages and checks if pages are malformed.
 5. After content is parsed and validated, it is passed to the “Content Seen?” component.
 6. “Content Seen” component checks if a HTML page is already in the storage.  
	- If it is in the storage, this means the same content in a different URL has already been  processed. In this case, the HTML page is discarded.
	- If it is not in the storage, the system has not processed the same content before. The  content is passed to Link Extractor.
7. Link extractor extracts links from HTML pages.
8. Extracted links are passed to the URL filter.
9. After links are filtered, they are passed to the “URL Seen?” component. 
10. “URL Seen” component checks if a URL is already in the storage, if yes, it is  processed before, and nothing needs to be done.
11. If a URL has not been processed before, it is added to the URL Frontier. 


## Design Deep Dive

### [[DFS]] vs [[BFS]]
- BFS is a better choice since depth can be huge
- This is a high level strategy, we need to optimize further on priority and politeness.

### URL Frontier

###### Politeness
- To avoid sending too many requests to the same host, called DOS attack.
- Only do one page at a time and add delay in between requests
- Different queues for different website hosts are created and each given a delay/ politeness constraint.
- Queue Router: Ensures each queue contains URLs from same host
- Mapping table: Contains a mapping of host and queue.
- FIFO queues: Contains URLs to be picked up
- Queue selector: Has logic to map each worker with a queue.
- Worker thread: Worker which downloads the webpage.

###### Priority
- Required to prioritize important web pages first
- Random post about Apple product or Apple website
- Prioritizer can take URLs and input and create queues with different priorities
- Queue Selector can then choose higher priority URLs first
- Front queue and Back queue can be added to manage prioritization and politeness respectively
###### Freshness
- To ensure newly added, edited or deleted webpages are updated, periodic crawls should be scheduled
- Strategies:
	- Based on web page update history
	- Prioritized URLs are more frequently crawled

###### Storage for URL frontier
- A hybrid approach of Disk an in memory data storage can be used
- Have a buffer in memory and periodically sync with disk

### HTML downloader

##### Robots Exclusion Protocol
- Robots.txt file is present in every website which tells crawlers which pages are allowed for downloading
- Cache the robots.txt file and refer it

##### Performance Optimization
- Some techniques to optimize performance:
	1. Distributed Crawl
	2. Cache DNS resolver
	3. Localization of workers/servers
	4. Short timeout to avoid long wait

##### Robustness
- Some techniques for robustness
	1. Consistent hashing
	2. Saving crawl states and data
	3. Exception handling
	4. Data Validation

##### Extensibility
- Flexible system to accommodate new requirements
	- PNG downloader can be plugged in after Content Seen?
	- Web monitor can also be added for copyright and trademark infringements

##### Detect and avoid problematic content
1. Redundant content: duplicated content
2. Spider traps: it is a trap which cause crawler to get stuck in infinite loop
	1. like a very long URL with a lot of pages
	2. A URL size limit can be added to prevent
	3. There are a lot of other types of spider traps and not one solution in enough for all
3. Data noise: content like ads, code snippets, spam URLs etc are not useful and should be excluded if possible.


## More studies
- **Server side rendering:** Some websites generate links on the fly, if we download the pages and then parse, the link will not be generated, hence those pages can be rendered server side to get links
- **Filter out unwanted pages**
- **[[DB replication]] and [[DB sharding]]**
- **[[Horizontal scaling]]**
- **[[Availability]], [[Consistency]], [[Reliability]]**
- **Analytics**