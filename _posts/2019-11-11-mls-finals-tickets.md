---
layout: post
title: Scalpers are smarter than I am
excerpt: I proclaimed ticket prices would go down within 48 hours of kick-off. I was wrong.
tags: sounders,seatgeek,cron,ssmtp,curl,bash,scripting,tickets,prices
---

**MLS Cup Final Ticket Prices**

Yesterday I went to the MLS Cup final and watched the Seattle Sounders beat Toronto FC 3-1 to win the MLS Cup at home, here in Seattle. It was an exciting game and Seattle played great but my prediction around tickets for the game were completely wrong. In my last post I set up a quick script to email and record ticket prices and quantity from seatgeek every 15 mins. 3 days before the final I predicted there'd be an excess of inventory which would drop prices significantly before the game. The very next day I was right, ticket prices had dropped to their lowest 

<img src="/public/images/NumTickets.png" alt="Number of tickets per day" width="700px">

<img src="/public/images/LowestPrice.png" alt="Lowest Price per day" width="700px">



**ssmtp working with gmail**

I always jump through multiple authentication login issues when sending through gmail with UNIX tools so hopefully writing this down will make it so I don't have to remember the same issues each time.

First to setup ssmtp.
```
sudo apt-get update
sudo apt-get install ssmtp
sudo vi /etc/ssmtp/ssmtp.conf
```

Then to set up the config file.
```/etc/ssmtp/ssmtp.conf
root=MYEMAIL@gmail.com
mailhub=smtp.gmail.com:465
FromLineOverride=YES
AuthUser=MYEMAIL@gmail.com
AuthPass=MYPASSWORD
UseTLS=YES
```

Now to test.
```
echo "Testing...1...2...3" | ssmtp MYEMAIL@gmail.com
```

Then you inevitably get the below.

```
ssmtp: Authorization failed (535 5.7.8  https://support.google.com/mail/?p=BadCredentials q1sm2306501qti.46 - gsmtp)
```

Then you google it and you find this Google Support article. https://support.google.com/mail/thread/5621336?hl=en. Then you enable “Access for less secure apps” per the first recommended answer in the Google Support article. And you once again get the above error. 

This is where it starts to get pretty frustrating but I finally found the actual solution. You have to enable "App Passwords" in gmail and assign a unique password per email client. I couldn't use my "main" password for the email account, it had to use an App Specific Password to work. Before you can have App Passwords though you have to enable 2FA. So in gmail, click "Manage your Google Account" -> "Security" -> "Enable 2-step verification". Then go through the flow and the first time I did this it forced me to use Google Prompt (don't worry you can switch to regular 2FA after enabling Google Prompt and then disable Google Prompt and delete your phone). Then go back to the "Security" page and now you'll see the "App Passwords" section. Then go through the flow and add a unique password per app. Hit the test again.

```
echo "Testing...1...2...3" | ssmtp MYEMAIL@gmail.com
```

Success.


**UPDATE**

I realized the API I was using wasn't accurate! Looks like the API is using Elasticsearch under the hood. The stats column is cached! Argh. There's no listings route so now I'm forced to go down the scraping route. Luckily they don't mask the request and response very well. They do use abbreviations for the response fields though but I've mapped it below for the ones you're probably interested in.

```bash
curl "https://seatgeek.com/rescraper/v2/listings?_include_seats=1&aid=225&client_id=MTY2MnwxMzgzMzIwMTU4&id=5080866&sixpack_client_id=dec7312f-10a0-4101-a5fc-d49afe679a2f" | jq .listings[0]
```
```
{
  "dm": "electronic",
  "d": "Enjoy the match from behind the South goal, where fans tend to watch while standing.",
  "dq": {
    "b": 0,
    "dq": 98.83,
    "ddq": "9.9",
    "ev": 666.66,
    "estimatedValue": 666.66
  },
  "dqm": {
    "1": {
      "b": 0,
      "dq": 97.46000000000001,
      "ddq": "9.7",
      "ev": 582.13,
      "estimatedValue": 582.13
    },
    "3": {
      "b": 0,
      "dq": 97.81,
      "ddq": "9.8",
      "ev": 610.58,
      "estimatedValue": 610.58
    }
  },
  "ep": true,
  "et": 1,
  "f": 27.5,
  "fi": "f8400ae8-bb9f-40bd-9262-40bf887ba3a7",
  "gk": "116_ll_11:13",
  "gr": 303008,
  "id": "l_3KEmAwlCdlBde5hyp1ymqf1Lygk3tb4NBwGsp2qdmDHkgEWmghgkxwl4iJFg2FRl",
  "ihd": "",
  "dl": 1,
  "h": 0,
  "lv": 0,
  "vp": 0,
  "mk": "s:116 r:ll",
  "m": "open",
  "pu": 0,
  "p": 275, # Price
  "pf": 302.5, # Price with fees
  "q": 3, # Quantity of tickets
  "r": "ll", # Row
  "rf": "Row LL",
  "rr": "LL",
  "ss": [
    "11",
    "12",
    "13"
  ],
  "sgp": 275,
  "sgf": 27.5,
  "sif": false,
  "s": "116",
  "sf": "Section 116",
  "sr": "116",
  "sh": 0,
  "sco": false,
  "sp": [
    1,
    3
  ],
  "spt": "",
  "st": "pdf",
  "wc": 0,
  "sro": 0,
  "w": 275
}
```

Had to do some slightly more complicated shuffling so decided to replace curl piece with this below python script. Still returns json so I can use jq but handles some of the filtering and sorting more elegantly. I know you could do that in bash but this seemed easier for me.

```python
import datetime
import requests
import json

res = requests.get('https://seatgeek.com/rescraper/v2/listings?_include_seats=1&aid=225&client_id=MTY2MnwxMzgzMzIwMTU4&id=5080866&sixpack_client_id=dec7312f-10a0-4101-a5fc-d49afe679a2f')
js = res.json()
listings = js['listings']
num_tickets = sum(map(lambda x: x['q'], listings))
sorted_price = sorted(listings, key=lambda x: x['pf'])
cheapest_price = sorted_price[0]['pf']
cheapest = []
for listing in listings:
    if listing['pf'] == cheapest_price:
        price = listing['p']
        price_with_fee = listing['pf']
        quantity = listing['q']
        row = listing['r']
        section = listing['s']
        seats = listing['gk'].split('_')[2]

        cheap = { 'price': price, 'price_with_fee': price_with_fee, 'quantity': quantity, 'row': row, 'section': section, 'seats': seats, 'time': datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')}
        cheapest.append(cheap)

num_cheapest_tickets = sum(map(lambda x: x['quantity'], cheapest))
print(json.dumps({'cheapest_price': cheapest_price, 'num_cheapest_tickets': num_cheapest_tickets, 'num_tickets': num_tickets, 'listings': cheapest}))
```


I let this run during work and got some interesting interesting data. Columns are time, lowest price, total number of tickets, number of tickets at lowest price. First thing I noticed is there seems to be some price floor at $203.50 which seems like it's set by scalpers. That are about 100 people all seem to be all pricing their tickets exactly the same. More likely is scalpers are buying anything lower than $203.50 and reselling at $203.50 to make a synthetic floor. 2nd is it seems like most individual sellers are posting a single ticket. I wonder if those single tickets get bought by scalpers because they own other tickets near the single tickets. Also it seems like most people post tickets to sell in the morning and purchase tickets after work near the afternoon, at least thats what the below data seems to show. Lastly, my hypothesis is that scalpers overbought and overpriced the tickets and they will get a lot cheaper come Saturday night when they start getting close to game time. 

The Sounders average roughly 42,000 fans. I think they opened all of CenturyLink for the final which can hold 72,000 but generally they only hit that for Seahawks playoff games. Roughly it seems like you get 1.5x more attendance for a final (just looking at Atlanta last year) which should put us at a fairly large surplus ahead of Sunday's game even though they supposedly sold 69K+ tickets. I have tickets already but hoping to use the above script to find some affordable tickets for some friends. Should be a great game, go Sounders!

```csv
Time,LowestPrice,NumTickets,NumLowestTickets
Fri Nov 8 09:30:02 PST 2019,203.5,1712,70
Fri Nov 8 09:45:02 PST 2019,203.5,1702,63
Fri Nov 8 10:00:02 PST 2019,203.5,1694,85
Fri Nov 8 10:15:01 PST 2019,203.5,1777,127
Fri Nov 8 10:30:02 PST 2019,203.5,1747,115
Fri Nov 8 10:45:01 PST 2019,203.5,1748,122
Fri Nov 8 11:00:02 PST 2019,198,1740,1
Fri Nov 8 11:15:04 PST 2019,171.6,1726,1
Fri Nov 8 11:30:02 PST 2019,203.5,1702,109
Fri Nov 8 11:45:01 PST 2019,203.5,1705,120
Fri Nov 8 12:00:02 PST 2019,198,1687,1
Fri Nov 8 12:15:02 PST 2019,198,1666,1
Fri Nov 8 12:30:01 PST 2019,198,1632,1
Fri Nov 8 12:45:02 PST 2019,203.5,1622,85
Fri Nov 8 13:00:10 PST 2019,203.5,1598,75
Fri Nov 8 13:15:02 PST 2019,203.5,1578,65
Fri Nov 8 13:30:02 PST 2019,203.5,1577,68
Fri Nov 8 13:45:02 PST 2019,203.5,1569,52
Fri Nov 8 14:00:04 PST 2019,203.5,1563,64
Fri Nov 8 14:15:02 PST 2019,203.5,1536,83
Fri Nov 8 14:30:02 PST 2019,203.5,1531,82
Fri Nov 8 14:45:03 PST 2019,184.8,1482,1
Fri Nov 8 15:00:05 PST 2019,203.5,1483,59
Fri Nov 8 15:15:02 PST 2019,203.5,1450,52
Fri Nov 8 15:30:01 PST 2019,203.5,1442,49
Fri Nov 8 15:45:02 PST 2019,203.5,1424,43
Fri Nov 8 16:00:02 PST 2019,203.5,1406,31
Fri Nov 8 16:15:02 PST 2019,203.5,1397,31
Fri Nov 8 16:30:02 PST 2019,203.5,1397,29
Fri Nov 8 16:45:01 PST 2019,203.5,1386,20
Fri Nov 8 17:00:02 PST 2019,203.5,1429,27
```