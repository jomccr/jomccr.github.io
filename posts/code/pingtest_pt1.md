# The inspiration for pingtest.py

Wed, Jul 22, 2020  9:56:03 PM

-----

I don't do a lot of programming outside of work like I used to when I was in school. I've been experiencing some internet connectivity issues lately. Every so often my wireless network icon will show an "internet may be unavailable" symbol on my phone. My chromecast will be dead in the water and even my work computer that is plugged directly into the ethernet in the back of the NETGEAR modem/router combo that I'm using at the moment won't be able to reach the web.

So, here's where I'm at. 

I want to accuse the ISP for something, but I really don't have any data to back up my claims at the moment. I decided to write a ping test that will attempt to hit an external site from my computer and log it to a file. 

So the basic pseudocode for the script I want to write goes like this... 

```
  1. setup a logfile
  2. make a request to a url 
  3. if we succeeded: log success as general info
  4. else: log failure as a warning
  5. sleep for one minute
  6. goto 2
```

Since this script will be running on my own machine, I'll just hop on and kill it whenever I get to it in the morning and view the results. Hopefully there's a pattern or some kind of interval as to when my computer has trouble reaching the internet. If not, I'll just keep it running throughout the day. I know for a fact I had trouble at least 5 times throughout the workday today while **working from home**... killing my VPN sessions and my PuTTy windows, database connections, open files I'm looking at with vim etc. 

One thought I had was I need to try not to just ping google every minute because they may not like it? I'm not sure if this is a valid concern when only hitting the site once a minute, but I decided to cycle through a list of 5 urls in order to try and reduce the amount of traffic to any one site. I'll be asleep while this is running, so I don't want to wake up IP banned from google.com or anything crazy like that.  :D

## Code

So the pseudocode above is pretty easy to implement in python. I need to do absolutely nothing fancy at all, just ping, log, sleep, repeat. 

```python
import requests
import logging
import datetime
from time import sleep 
```

I chose to use requests because I had another script that used it handy and I was unable to look up the syntax for anything else due to an intermittent connectivity issue... The logging library is really nice for quick scripts like this and I couldn't be bothered to look up how to find the current time from the `time` module so I just stuck with datetime. 

```python
logging.basicConfig(filename='pingtest.log', level=logging.INFO)

urls = ['https://www.google.com/',
        'https://www.microsoft.com/',
        'https://www.yahoo.com/', 
        'https://www.reddit.com/',
        'https://www.netgear.com/']
```

I chose logging level INFO because I want to separate successful and unsuccessful pings in the log. I'll use `logging.info()` for success and `logging.warning()` for errors.

```python
while True: 
    s = requests.Session()

    for u in urls: 
        try: 
            r = s.get(url=u)
            logging.info('{} : connected to {} successfully'.format(datetime.datetime.now(), u))
        except Exception as e:
            logging.warning('{} : Error - {}'.format(datetime.datetime.now(), e))

        sleep(60 * 1) # log every 1 minutes
```

I wasn't sure if I should instantiate a new connection object once at the top but I decided to make a new one every minute. The overhead is negligible and I don't want to worry about a session "expiring" (if that's even a thing... mind you still no internet at this point). 

The `for` loop spins through the URLs and attempts to connect, if success it `info`s and if it fails then it `warn`s all out to the logfile. Here's a sample of the log: 

```
INFO:root:2020-07-22 21:46:23.609150 : connected to https://www.netgear.com/ successfully
INFO:root:2020-07-22 21:47:23.887136 : connected to https://www.google.com/ successfully
INFO:root:2020-07-22 21:48:24.422790 : connected to https://www.microsoft.com/ successfully
INFO:root:2020-07-22 21:49:25.005658 : connected to https://www.yahoo.com/ successfully
INFO:root:2020-07-22 21:50:25.280306 : connected to https://www.reddit.com/ successfully
INFO:root:2020-07-22 21:51:25.827337 : connected to https://www.netgear.com/ successfully
INFO:root:2020-07-22 21:52:26.111429 : connected to https://www.google.com/ successfully
```

I will report back in the morning with my findings...


