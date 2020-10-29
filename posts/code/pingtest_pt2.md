Thu, Oct 29, 2020  8:09:31 AM
----

## pingtest.py 

[Pingtest](https://github.com/jomccr/pingtest) was a utility I wrote in order to help debug some intermittent internet issues I was having (see my initial post [here](https://jomccr.github.io/posts/code/pingtest_pt1.html)).

```
usage: pingtest.py [-h] [--sleep SLEEP]

Ping a set of hosts and log if one cannot be reached.

optional arguments:
  -h, --help     show this help message and exit
  --sleep SLEEP  number of seconds to sleep between pings
```

It's very simple. I had just moved into a new apartment and I wanted to see if the connectivity issues I was having boiled down to an issue with my DNS settings (I had just set up a new [pi-hole](https://pi-hole.net/) ad blocker) or if it was a legitimate connectivity issue that I could take up with the ISP. 

The tech that came by told me the entire row of apartments I was a part of was probably experiencing intermittent issues, as we were all on the same connector to the underground infrastructure. He moved me over to an empty connector and I was up and running faster than ever!

I used a list of dictionaries that I could loop through and lookup either the hostname or IP address of the destination I'm trying to reach. 

```python
    urls = [ # added IP addresses to see if DNS-only issue
            {'hostname':'http://imgur.com','ip':'http://151.101.56.193' },
            {'hostname':'http://python.org', 'ip':'http://45.55.99.72' },
            {'hostname':'http://yahoo.com', 'ip':'http://72.30.35.9' },
            {'hostname':'http://google.com', 'ip':'http://64.233.177.102'} ,
            {'hostname':'http://reddit.com', 'ip':'http://151.101.65.140' },
            {'hostname':'http://netgear.com', 'ip':'http://13.248.140.194' },
        ]
```

Then, I began the loop through the URLs and a series of try/except blocks. Pseudocode looked something like this: 

```python
try: 
    get(hostname) 
except error: 
    log(error)
    try: 
        get(ip)
    except error: 
        log(error) 
```

This would be easier to follow with C-like curly brackets for scoping/nesting, but python is straightforward enough if you've been around it for a while. At the end of the loop, I use the `sleep()` function for some number of time and repeat the process with the next URL. 

Again, if you want to see the whole code that sets up a log file and handles timestamps and everything, [get it here](https://github.com/jomccr/pingtest).

Thanks
