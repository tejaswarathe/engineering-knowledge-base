## Requirements
- 100 million URLs generated per day
- as short as possible URL
- Combination of number and alphabets
- Cannot be deleted or updated
- Highly available, scalable and fault-tolerant

## Back of the envelope calculations
- Write operations 100 million per day
- Write per sec: *100 million/24/3600 = 1160*
- Read to write ratio: 10:1
- Read per sec: 11600
- Assuming service run for 10 years, storage requirements: *100 million x 365 x 10 = 365 billion records*
- Average URL length = *100 bytes*
- Storage in 10 years = *365 billion x 100 bytes x 10 years = 365 TB*


### High level design
- Create a REST API based approach with one GET and one POST API.
- POST is simple: send long URL, get back shortened URL
- GET will accept the short URL from browser and return a 301 response with the long URL
- 301 is a redirect response code

	###### <ins>301 redirect vs 302 redirect</ins>
	
	301 redirect is a permanent redirect where the server receives request once, browser caches the request and future requests are directly made to long URL
	
	302 redirect is a temporary redirect where all the request comes to the server.
	301 is better for speed ↔ 302 is better for when analytics like click rate are important.


- Most intuitive way to implement is using hash tables with <short URL, long URL> pairs.

#### URL Shortening
- We will need a hash function to convert long to short URL
- All collisions must be handled.

### Design Deep dive
#### Data Model
Hash Tables are not scalable, hence a Relational DB is a better approach.

#### Hash function
A hash function will be used to generate *hashValue*
	###### <ins>Calculating hash value length</ins>
	- *hashValue* can consist of numbers and capital and small alphabets
	- total of 62 characters are possible
	- We need to decide a value *n*, such that 62^n can contain all 365 billion records
	- In this case for n=7, we can store 3.5 trillion records


1. Hash + collision resolution
	- URL is shortened using well known hash functions CRC32, MD5 or SHA-1 etc.
	- the first 7 characters are taken, rest are ignored
	- if collision happens, we append a predefined string to long URL and recursively keep generating short URL with them until collision ends
	- Con: It will be expensive to query DB in read operation,
	- [[Bloom filter]] can be used to improve performance.

2. base 62 conversion
	- a new unique ID by incrementing is generated for every new write operation
	- this ID is converted to its base 62 number, which will form the short URL
	- unique ID, short URL (base 62 conversion), long URL are store in DB
	- Distributed ID generator as discussed in [[Chapter 7 - Design A Unique ID Generator in Distributed System]] can be used.


### Further points
- Rate limiter can be added - [[Chapter 4 - Design a rate limiter]]
- Web Server scaling
- Database scaling: [[DB replication]] and [[DB sharding]] techniques
- Analytics can be added for business improvements
- [[Availability]], [[Consistency]], [[Reliability]]





