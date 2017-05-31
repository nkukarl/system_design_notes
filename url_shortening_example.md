## URL Shortening Example

Design a url shortening service, like bit.ly.

### Use Cases

#### In Scope

1. Take a long url and return a short url
2. Take a short url and retrieve the full url
3. Custom url
4. High availability??

#### Out of Scope

1. Analytics
2. Automatic expiration
3. UI vs API



### Data and Traffic

Start from tweets: 0.5 billion tweets are generated per day. Over a month time, we will have 15 billion new urls.

Let us assume 10% of tweets contain a shortened url, this would give us 1.5 billion new urls per month.

Given the assumption that the system we are designing is not the top 3 but the top 10 url shortening service, and the 80-20 rule, we could say No. 4 - 10 deals with 300 million urls per month in total.

And we could assume we need to generate 100 million new urls per month if we take potential growth into consideration.

Let us assume shortening service only accounts for 10% of the total requests, we will have 1 billion total requests per month.

Furthermore, we could reduce it to request per second, we have roughly

1. 40 new url per second
2. 360 redirection request per second

#### Data

In 5 years time, we create 60 month * 100 million/month = 6 billion urls.

If we use A-Za-z0-9 as the characters (62 chars) for the shortened url (hash), it would require 6 bytes per shortened url since 62^6 > 6 * 10^9.

If each long url contains 500 bytes on average, we will need 500 * 6 * 10^9 = 3TB to store the long urls and 6 * 6 * 10^9 = 36GB to store the hashes.

#### Traffic

40 new urls per second: 40 * 500B = 20KB per second

360 redirection per second: 360 * 500B = 180KB per second



### Abstract Design

Application service layer: shortening and redirecting

Generate a hash, check if it already exists. If Yes, generate another one. If No, create a hash to url entry.

Data storage layer: storing hash to url mapping

Store mappings and retrieve a value based on a key given.

N.B., when creating hashes, can do something like:

```python
hashed_url = convert_to_base62(md5(original_url + random_salt))[:6]
```



### Questions worth exploring:

1. What does it mean by high availability?
2. What is random salt?
3. What is md5?