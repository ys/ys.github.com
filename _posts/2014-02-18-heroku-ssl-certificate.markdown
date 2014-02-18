---
layout: large
title: Update a Heroku SSL Certificate
date: '2014-02-18'
categories: ''
background: "#6567a5"
font_color: "#fff"
code_color: "#494b7d"
---

A quick reminder as I always forget how to deal with [DNSimple](http://dnsimple.com) certificates.

- Build the Public Certificate Chain PEM file
  By concatenating the certificates in the *right order* from yours to the CARoot.

```
$ cat STAR_your_app_com.crt EssentialSSLCA_2.crt ComodoUTNSGCCA.crt UTNAddTrustSGCCA.crt AddTrustExternalCARoot.crt > bundle.pem
```

- Download also the private key to `private.key`

- Update certs on heroku

```
$ heroku certs:update bundle.pem private.key -a your_app
```

Warning: If you `heroku certs:add` a new one, you end with a new herokussl endpoint.
