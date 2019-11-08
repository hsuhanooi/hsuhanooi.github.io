---
layout: post
title: Simple Scripting Getting ssmtp on UNIX with gmail Working
excerpt: I use cron, ssmtp, bash, and curl for a lot of simple 30 min projects.
---

**MLS Cup Final**

I use cron, ssmtp, bash, and curl for a lot of simple 30 min projects. Most recently I wanted to use the SeatGeek API to find cheap tickets for the MLS Cup final in Seattle coming up on Sunday. Seatgeek has an easy to use API so I decided to record the lowest price, number of tickets and timestamp to see how it trends leading up to the actual day. I also wanted it to send me the lowest price in the email subject line to get my attention if the price is low enough. The current lowest price is $263 which I'm not really interested in paying a massive premium. I wrote the simple script below to query the API and set it to email me every 30 mins with ssmtp. PS Obviously terrible bash but that's all I could do in 30m. 

```bash
source "/root/.bashrc"
OUT_JSON=`curl -s "https://api.seatgeek.com/2/events/5080866?client_id=MTkyODA3MjB8MTU3MzE3MjI3MC41NA" | jq .stats`
LOWEST=`echo $OUT_JSON | jq .lowest_price`
NUM_TICKETS=`echo $OUT_JSON | jq .visible_listing_count`
echo $OUT_JSON | jq .
echo "CURRENT ${LOWEST}"
TTIME=`TZ=America/Los_Angeles date`
echo "$TTIME,$LOWEST,$NUM_TICKETS" >> /root/lowest_sounders.csv
OUTPUT=`cat /root/lowest_sounders.csv`
{
  echo "Subject: Sounders Tickets (Cheapest ${LOWEST})"
  echo
  cat /root/lowest_sounders.csv | while read line; do echo $line ; done
  echo
  echo $OUT_JSON | jq .
  echo
} | /usr/sbin/ssmtp dracarystorrentian@gmail.com
```

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