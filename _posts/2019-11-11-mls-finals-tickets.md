---
layout: post
title: 2019 MLS Cup Final Ticket Prices Data
excerpt: I proclaimed ticket prices would go down within 48 hours of kick-off. I was wrong.
tags: sounders,seatgeek,cron,ssmtp,curl,bash,scripting,tickets,prices
---

**MLS Cup Final Ticket Prices**

Yesterday (Sunday Nov 10th) I went to the MLS Cup final and watched the Seattle Sounders beat Toronto FC 3-1 to win the MLS Cup at home, here in Seattle. It was an exciting game and Seattle played great but my prediction around ticket prices before the game were completely wrong. In my last post I set up a quick script to email and record ticket prices and quantity from seatgeek every 15 mins. 3 days before the final (Thursday) I predicted there'd be an excess of inventory which would drop prices significantly before the game. The very next day (Friday), I was right. Ticket prices dropped to $163 per ticket on Friday from 6:45pm till 8:30pm with about 100 or so tickets being on sale. Unfortunately this was the last time tickets would drop that low. I graphed the lowest ticket prices by time below along with the number of tickets available at that price.

<img src="/public/images/LowestPrice.png" alt="Lowest Price per day" width="600px">

Then I graphed the total number of tickets available to look at supply. 

<img src="/public/images/NumTickets.png" alt="Number of tickets per day" width="600px">

On the day of the game, starting around 12:01am you can see ticket prices spiked up to $250 and hit almost $400 right before game time. This is where I thought scalpers would get nervous and start to firesale tickets but they must have been confident that people were waiting until the last minute to buy and that demand would outstrip supply. They were right, and as the number of tickets dwindled they continued to sell at almost 4x the original price. Now I didn't control here for what the seats were actually like and maybe those $400 cheapest tickets were club seats or better than nosebleeds but for people who just wanted to get in to see the game and feel the atmosphere I don't think that matters that much.

I ended up getting tickets through my friend who's a season ticket holder but was unable to help my friends get their own tickets to the game :(. I did alert them of the price movement to $163 but thought it would keep dropping so I ended up giving some bad advice. Usually for normal non-playoff Sounders home games I can get less than season-ticket holder prices on last minute tickets but looks like the MLS Cup Final doesn't have the same type of demand. It was interesting to see how supply and demand bounced leading up to game day though and maybe someone can use this data somehow. It's not a ton of data points but I included them below if anyone wants to use it.

Also if you want to parse the datetime in python I included the datetime strptime string I used to parse it since that's never fun.

```python3
parsed = datetime.datetime.strptime(dtime, '%a %b %d %H:%M:%S PST %Y')
```

```csv
Time,LowestPrice,NumTickets,NumTicketsAtLowest
Thu Nov  7 20:30:02 PST 2019,203.5,1758,61
Thu Nov  7 20:45:02 PST 2019,191.4,1725,2
Thu Nov  7 21:00:02 PST 2019,203.5,1718,46
Thu Nov  7 21:15:02 PST 2019,203.5,1697,37
Thu Nov  7 21:30:05 PST 2019,203.5,1811,71
Thu Nov  7 21:45:02 PST 2019,203.5,1820,79
Thu Nov  7 22:00:02 PST 2019,203.5,1813,75
Thu Nov  7 22:15:05 PST 2019,203.5,1809,71
Thu Nov  7 22:30:02 PST 2019,203.5,1796,78
Thu Nov  7 22:45:02 PST 2019,203.5,1786,72
Thu Nov  7 23:00:02 PST 2019,203.5,1773,67
Thu Nov  7 23:15:05 PST 2019,203.5,1756,54
Thu Nov  7 23:30:04 PST 2019,203.5,1774,66
Thu Nov  7 23:45:05 PST 2019,203.5,1766,61
Fri Nov  8 00:00:05 PST 2019,203.5,1764,61
Fri Nov  8 00:15:06 PST 2019,203.5,1755,54
Fri Nov  8 00:30:02 PST 2019,203.5,1756,59
Fri Nov  8 00:45:05 PST 2019,203.5,1758,56
Fri Nov  8 01:00:02 PST 2019,203.5,1764,61
Fri Nov  8 01:15:04 PST 2019,203.5,1765,62
Fri Nov  8 01:30:02 PST 2019,203.5,1761,58
Fri Nov  8 01:45:05 PST 2019,203.5,1760,57
Fri Nov  8 02:00:05 PST 2019,203.5,1761,60
Fri Nov  8 02:15:05 PST 2019,203.5,1763,60
Fri Nov  8 02:30:05 PST 2019,203.5,1765,61
Fri Nov  8 02:45:05 PST 2019,203.5,1765,61
Fri Nov  8 03:00:05 PST 2019,203.5,1764,60
Fri Nov  8 03:15:02 PST 2019,203.5,1765,61
Fri Nov  8 03:30:04 PST 2019,203.5,1766,62
Fri Nov  8 03:45:04 PST 2019,203.5,1760,60
Fri Nov  8 04:00:02 PST 2019,203.5,1762,60
Fri Nov  8 04:15:01 PST 2019,203.5,1761,60
Fri Nov  8 04:30:02 PST 2019,203.5,1756,54
Fri Nov  8 04:45:06 PST 2019,203.5,1756,56
Fri Nov  8 05:00:02 PST 2019,203.5,1753,53
Fri Nov  8 05:15:01 PST 2019,203.5,1747,53
Fri Nov  8 05:30:02 PST 2019,203.5,1755,57
Fri Nov  8 05:45:04 PST 2019,203.5,1738,47
Fri Nov  8 06:00:02 PST 2019,203.5,1748,57
Fri Nov  8 06:15:02 PST 2019,203.5,1739,50
Fri Nov  8 06:30:02 PST 2019,203.5,1717,47
Fri Nov  8 06:45:01 PST 2019,203.5,1714,44
Fri Nov  8 07:00:02 PST 2019,203.5,1697,33
Fri Nov  8 07:15:02 PST 2019,203.5,1695,34
Fri Nov  8 07:30:02 PST 2019,203.5,1694,38
Fri Nov  8 07:45:05 PST 2019,203.5,1685,40
Fri Nov  8 08:00:02 PST 2019,203.5,1676,29
Fri Nov  8 08:15:01 PST 2019,203.5,1681,35
Fri Nov  8 08:30:02 PST 2019,203.5,1655,25
Fri Nov  8 08:45:01 PST 2019,203.5,1942,147
Fri Nov  8 09:00:02 PST 2019,198,1769,1
Fri Nov  8 09:15:01 PST 2019,203.5,1745,61
Fri Nov  8 09:30:02 PST 2019,203.5,1712,70
Fri Nov  8 09:45:02 PST 2019,203.5,1702,63
Fri Nov  8 10:00:02 PST 2019,203.5,1694,85
Fri Nov  8 10:15:01 PST 2019,203.5,1777,127
Fri Nov  8 10:30:02 PST 2019,203.5,1747,115
Fri Nov  8 10:45:01 PST 2019,203.5,1748,122
Fri Nov  8 11:00:02 PST 2019,198,1740,1
Fri Nov  8 11:15:04 PST 2019,171.6,1726,1
Fri Nov  8 11:30:02 PST 2019,203.5,1702,109
Fri Nov  8 11:45:01 PST 2019,203.5,1705,120
Fri Nov  8 12:00:02 PST 2019,198,1687,1
Fri Nov  8 12:15:02 PST 2019,198,1666,1
Fri Nov  8 12:30:01 PST 2019,198,1632,1
Fri Nov  8 12:45:02 PST 2019,203.5,1622,85
Fri Nov  8 13:00:10 PST 2019,203.5,1598,75
Fri Nov  8 13:15:02 PST 2019,203.5,1578,65
Fri Nov  8 13:30:02 PST 2019,203.5,1577,68
Fri Nov  8 13:45:02 PST 2019,203.5,1569,52
Fri Nov  8 14:00:04 PST 2019,203.5,1563,64
Fri Nov  8 14:15:02 PST 2019,203.5,1536,83
Fri Nov  8 14:30:02 PST 2019,203.5,1531,82
Fri Nov  8 14:45:03 PST 2019,184.8,1482,1
Fri Nov  8 15:00:05 PST 2019,203.5,1483,59
Fri Nov  8 15:15:02 PST 2019,203.5,1450,52
Fri Nov  8 15:30:01 PST 2019,203.5,1442,49
Fri Nov  8 15:45:02 PST 2019,203.5,1424,43
Fri Nov  8 16:00:02 PST 2019,203.5,1406,31
Fri Nov  8 16:15:02 PST 2019,203.5,1397,31
Fri Nov  8 16:30:02 PST 2019,203.5,1397,29
Fri Nov  8 16:45:01 PST 2019,203.5,1386,20
Fri Nov  8 17:00:02 PST 2019,203.5,1429,27
Fri Nov  8 17:15:04 PST 2019,184.8,1606,1
Fri Nov  8 17:30:02 PST 2019,202.4,1581,1
Fri Nov  8 17:45:01 PST 2019,189.22,1554,1
Fri Nov  8 18:00:02 PST 2019,193.6,1550,2
Fri Nov  8 18:15:02 PST 2019,189.22,1536,1
Fri Nov  8 18:30:01 PST 2019,189.22,1508,1
Fri Nov  8 18:45:02 PST 2019,162.8,1505,82
Fri Nov  8 19:00:02 PST 2019,162.8,1441,30
Fri Nov  8 19:15:05 PST 2019,162.8,1430,29
Fri Nov  8 19:30:02 PST 2019,158.4,1403,2
Fri Nov  8 19:45:02 PST 2019,158.4,1395,2
Fri Nov  8 20:00:02 PST 2019,158.4,1372,2
Fri Nov  8 20:15:01 PST 2019,162.8,1363,10
Fri Nov  8 20:30:02 PST 2019,162.8,1341,5
Fri Nov  8 20:45:02 PST 2019,203.5,1335,3
Fri Nov  8 21:00:04 PST 2019,203.5,1348,14
Fri Nov  8 21:15:02 PST 2019,203.5,1342,11
Fri Nov  8 21:30:04 PST 2019,203.5,1334,7
Fri Nov  8 21:45:02 PST 2019,203.5,1331,11
Fri Nov  8 22:00:04 PST 2019,203.5,1331,7
Fri Nov  8 22:15:02 PST 2019,203.5,1331,9
Fri Nov  8 22:30:03 PST 2019,203.5,1327,7
Fri Nov  8 22:45:02 PST 2019,203.5,1322,4
Fri Nov  8 23:00:06 PST 2019,203.5,1316,4
Fri Nov  8 23:15:02 PST 2019,203.5,1318,3
Fri Nov  8 23:30:02 PST 2019,203.5,1316,5
Fri Nov  8 23:45:04 PST 2019,203.5,1315,3
Sat Nov  9 00:00:02 PST 2019,203.5,1312,5
Sat Nov  9 00:15:04 PST 2019,203.5,1303,3
Sat Nov  9 00:30:05 PST 2019,203.5,1300,2
Sat Nov  9 00:45:05 PST 2019,203.5,1302,5
Sat Nov  9 01:00:05 PST 2019,203.5,1297,4
Sat Nov  9 01:15:02 PST 2019,203.5,1297,4
Sat Nov  9 01:30:04 PST 2019,203.5,1295,2
Sat Nov  9 01:45:04 PST 2019,203.5,1293,5
Sat Nov  9 02:00:02 PST 2019,203.5,1293,5
Sat Nov  9 02:15:05 PST 2019,203.5,1295,5
Sat Nov  9 02:30:05 PST 2019,203.5,1294,5
Sat Nov  9 02:45:04 PST 2019,203.5,1291,4
Sat Nov  9 03:00:05 PST 2019,203.5,1292,5
Sat Nov  9 03:15:05 PST 2019,203.5,1292,5
Sat Nov  9 03:30:04 PST 2019,203.5,1290,5
Sat Nov  9 03:45:04 PST 2019,203.5,1290,5
Sat Nov  9 04:00:01 PST 2019,203.5,1290,5
Sat Nov  9 04:15:02 PST 2019,203.5,1286,3
Sat Nov  9 04:30:02 PST 2019,203.5,1288,3
Sat Nov  9 04:45:02 PST 2019,203.5,1280,1
Sat Nov  9 05:00:02 PST 2019,203.5,1280,3
Sat Nov  9 05:15:02 PST 2019,203.5,1280,3
Sat Nov  9 05:30:02 PST 2019,203.5,1281,3
Sat Nov  9 05:45:02 PST 2019,203.5,1281,3
Sat Nov  9 06:00:01 PST 2019,203.5,1280,3
Sat Nov  9 06:15:02 PST 2019,203.5,1281,3
Sat Nov  9 06:30:02 PST 2019,203.5,1282,3
Sat Nov  9 06:45:03 PST 2019,203.5,1278,3
Sat Nov  9 07:00:02 PST 2019,203.5,1275,1
Sat Nov  9 07:15:02 PST 2019,203.5,1273,3
Sat Nov  9 07:30:02 PST 2019,203.5,1262,1
Sat Nov  9 07:45:01 PST 2019,229.68,1251,1
Sat Nov  9 08:00:02 PST 2019,203.5,1246,1
Sat Nov  9 08:15:04 PST 2019,203.5,1242,3
Sat Nov  9 08:30:02 PST 2019,203.5,1228,1
Sat Nov  9 08:45:02 PST 2019,203.5,1206,1
Sat Nov  9 09:00:01 PST 2019,229.68,1212,1
Sat Nov  9 09:15:02 PST 2019,198,1205,1
Sat Nov  9 09:30:02 PST 2019,198,1186,1
Sat Nov  9 09:45:02 PST 2019,198,1179,1
Sat Nov  9 10:00:02 PST 2019,203.5,1165,1
Sat Nov  9 10:15:01 PST 2019,231,1146,2
Sat Nov  9 10:30:02 PST 2019,231,1139,2
Sat Nov  9 10:45:02 PST 2019,203.5,1130,1
Sat Nov  9 11:00:05 PST 2019,224.4,1119,1
Sat Nov  9 11:15:02 PST 2019,224.4,1105,1
Sat Nov  9 11:30:04 PST 2019,231,1090,2
Sat Nov  9 11:45:02 PST 2019,203.5,1079,1
Sat Nov  9 12:00:01 PST 2019,229.68,1067,2
Sat Nov  9 12:15:01 PST 2019,229.68,1060,2
Sat Nov  9 12:30:02 PST 2019,203.5,1038,1
Sat Nov  9 12:45:02 PST 2019,203.5,1033,1
Sat Nov  9 13:00:02 PST 2019,203.5,1022,1
Sat Nov  9 13:15:02 PST 2019,215.16,1006,1
Sat Nov  9 13:30:04 PST 2019,215.16,1010,1
Sat Nov  9 13:45:02 PST 2019,203.5,1004,1
Sat Nov  9 14:00:02 PST 2019,203.5,995,1
Sat Nov  9 14:15:03 PST 2019,215.16,985,1
Sat Nov  9 14:30:01 PST 2019,229.68,981,2
Sat Nov  9 14:45:02 PST 2019,229.68,966,2
Sat Nov  9 15:00:02 PST 2019,229.68,957,2
Sat Nov  9 15:15:02 PST 2019,229.68,950,2
Sat Nov  9 15:30:04 PST 2019,229.68,940,2
Sat Nov  9 15:45:01 PST 2019,229.68,935,2
Sat Nov  9 16:00:02 PST 2019,250.8,927,3
Sat Nov  9 16:15:04 PST 2019,250.8,912,3
Sat Nov  9 16:30:02 PST 2019,250.8,901,3
Sat Nov  9 16:45:01 PST 2019,250.8,890,1
Sat Nov  9 17:00:02 PST 2019,250.8,877,1
Sat Nov  9 17:15:02 PST 2019,250.8,874,1
Sat Nov  9 17:30:02 PST 2019,250.8,857,1
Sat Nov  9 17:45:02 PST 2019,231,845,1
Sat Nov  9 18:00:02 PST 2019,231,837,1
Sat Nov  9 18:15:02 PST 2019,212.52,832,1
Sat Nov  9 18:30:02 PST 2019,212.52,825,1
Sat Nov  9 18:45:02 PST 2019,212.52,808,1
Sat Nov  9 19:00:02 PST 2019,211.22,791,1
Sat Nov  9 19:15:02 PST 2019,212.52,779,1
Sat Nov  9 19:30:02 PST 2019,212.52,763,1
Sat Nov  9 19:45:01 PST 2019,224.4,751,1
Sat Nov  9 20:00:01 PST 2019,224.4,741,1
Sat Nov  9 20:15:02 PST 2019,233.61,735,1
Sat Nov  9 20:30:01 PST 2019,237.6,713,2
Sat Nov  9 20:45:02 PST 2019,237.6,701,1
Sat Nov  9 21:00:05 PST 2019,237.6,702,2
Sat Nov  9 21:15:02 PST 2019,196.68,698,1
Sat Nov  9 21:30:02 PST 2019,242.88,693,1
Sat Nov  9 21:45:01 PST 2019,237.6,681,3
Sat Nov  9 22:00:02 PST 2019,262.68,665,1
Sat Nov  9 22:15:01 PST 2019,262.68,659,1
Sat Nov  9 22:30:04 PST 2019,262.68,649,1
Sat Nov  9 22:45:01 PST 2019,262.68,642,1
Sat Nov  9 23:00:02 PST 2019,262.68,633,1
Sat Nov  9 23:15:02 PST 2019,246.84,623,1
Sat Nov  9 23:30:01 PST 2019,257.4,608,1
Sat Nov  9 23:45:02 PST 2019,250.8,601,1
Sun Nov 10 00:00:02 PST 2019,250.8,601,1
Sun Nov 10 00:15:02 PST 2019,250.8,591,1
Sun Nov 10 00:30:02 PST 2019,250.8,590,1
Sun Nov 10 00:45:02 PST 2019,250.8,584,1
Sun Nov 10 01:00:04 PST 2019,250.8,581,1
Sun Nov 10 01:15:01 PST 2019,250.8,585,1
Sun Nov 10 01:30:04 PST 2019,257.4,577,1
Sun Nov 10 01:45:04 PST 2019,257.4,578,1
Sun Nov 10 02:00:03 PST 2019,257.4,582,1
Sun Nov 10 02:15:01 PST 2019,257.4,582,1
Sun Nov 10 02:30:04 PST 2019,257.4,582,1
Sun Nov 10 02:45:02 PST 2019,257.4,584,1
Sun Nov 10 03:00:02 PST 2019,257.4,583,1
Sun Nov 10 03:15:02 PST 2019,257.4,581,1
Sun Nov 10 03:30:04 PST 2019,257.4,579,1
Sun Nov 10 03:45:03 PST 2019,257.4,579,1
Sun Nov 10 04:00:02 PST 2019,257.4,581,1
Sun Nov 10 04:15:01 PST 2019,257.4,579,1
Sun Nov 10 04:30:02 PST 2019,257.4,579,1
Sun Nov 10 04:45:04 PST 2019,257.4,579,1
Sun Nov 10 05:00:02 PST 2019,257.4,579,1
Sun Nov 10 05:15:02 PST 2019,257.4,579,1
Sun Nov 10 05:30:02 PST 2019,257.4,576,1
Sun Nov 10 05:45:02 PST 2019,257.4,574,1
Sun Nov 10 06:00:02 PST 2019,257.4,572,1
Sun Nov 10 06:15:02 PST 2019,257.4,576,1
Sun Nov 10 06:30:01 PST 2019,257.4,563,1
Sun Nov 10 06:45:02 PST 2019,257.4,557,1
Sun Nov 10 07:00:02 PST 2019,257.4,555,1
Sun Nov 10 07:15:02 PST 2019,257.4,538,1
Sun Nov 10 07:30:02 PST 2019,231,531,2
Sun Nov 10 07:45:02 PST 2019,252.12,505,1
Sun Nov 10 08:00:02 PST 2019,252.12,482,1
Sun Nov 10 08:15:02 PST 2019,262.68,449,2
Sun Nov 10 08:30:02 PST 2019,229.68,422,1
Sun Nov 10 08:45:02 PST 2019,264,386,1
Sun Nov 10 09:00:02 PST 2019,264,358,1
Sun Nov 10 09:15:02 PST 2019,231,341,1
Sun Nov 10 09:30:02 PST 2019,274.56,313,1
Sun Nov 10 09:45:02 PST 2019,297,294,3
Sun Nov 10 10:00:02 PST 2019,323.43,269,1
Sun Nov 10 10:15:01 PST 2019,323.43,220,1
Sun Nov 10 10:30:03 PST 2019,264,211,4
Sun Nov 10 10:45:02 PST 2019,203.5,167,2
Sun Nov 10 11:00:01 PST 2019,237.6,142,2
Sun Nov 10 11:15:01 PST 2019,302.5,115,2
Sun Nov 10 11:30:02 PST 2019,396,68,3
Sun Nov 10 11:45:01 PST 2019,330,54,1
Sun Nov 10 12:00:02 PST 2019,328.68,37,1
Sun Nov 10 12:15:02 PST 2019,505.56,24,1
Sun Nov 10 12:30:03 PST 2019,505.56,20,1
Sun Nov 10 12:45:02 PST 2019,278.52,23,1
Sun Nov 10 13:00:02 PST 2019,231,44,10
```
