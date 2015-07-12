---
title:  "Free (as in beer) SSL Certificates"
date:   2015-05-28 16:30:00
---

We all know about all the man-in-the-middle attacks that can happen if your web sites are not protected by SSL. We also know that Google [ranks SSL sites higher than non-SSL](http://googlewebmastercentral.blogspot.co.uk/2014/08/https-as-ranking-signal.html) ones. But one thing has prevented universal uptake of SSL encryption across the web - cost.

Mozilla, the Electronic Frontier Foundation and others hope to change this. This September, they are launching [Let's Encrypt](https://letsencrypt.org), a SSL certificate authority that will offer certificate free of charge.

The best part is that they have greatly simplified the process of purchasing and installing the certificates. On Linux, they expect the process to be...

{% highlight bash %}
$ sudo apt-get install lets-encrypt
$ lets-encrypt example.com
{% endhighlight %}

Those two commands will install the `lets-encrypt` package, fetch the SSL certificate for your site, install it into your web server. What's more, they claim that the certificate will even [automatically renew](https://github.com/letsencrypt/acme-spec) itself!

They'll still be room for commercial SSL authorities, as many companies will benefit from enhanced verification (showing their company name next to the padlock in the browser) and insurance against the authority's root SSL being compromised.
