---
title:  "Adding gem auditing to CI process"
date:   2015-06-24 19:20:00
---

One part of the development process that is all too often overlooked is security. Although we may follow the Rails Guides advice for securing our applications, what about our external dependencies?

The average Gemfile for most Rails applications is chock full of references to gems created by third-parties. How will you know if any of these are compromised. Luckily, `bundler-audit` is here to help.

First, we install the gem:

{% highlight ruby %}
gem install bundler-audit
{% endhighlight %}

To use it we run `bundle-audit` from the command line. Here is an example output from an outdated Rails application:

{% highlight ruby %}
Name: activesupport
Version: 4.1.6
Advisory: CVE-2015-3227
Criticality: Unknown
URL: https://groups.google.com/forum/#!topic/rubyonrails-security/bahr2JLnxvk
Title: Possible Denial of Service attack in Active Support
Solution: upgrade to >= 4.2.2, ~> 4.1.11, ~> 3.2.22

Name: http
Version: 0.6.3
Advisory: CVE-2015-1828
Criticality: Medium
URL: https://groups.google.com/forum/#!topic/httprb/jkb4oxwZjkU
Title: HTTPS MitM vulnerability in http.rb
Solution: upgrade to >= 0.7.3, ~> 0.6.4

Name: jquery-rails
Version: 3.1.2
Advisory: CVE-2015-1840
Criticality: Unknown
URL: https://groups.google.com/forum/#!topic/ruby-security-ann/XIZPbobuwaY
Title: CSRF Vulnerability in jquery-ujs and jquery-rails
Solution: upgrade to >= 4.0.4, ~> 3.1.3
{% endhighlight %}

Now we can read up on the vulnerabilities which affect of application, and make it our priority to update the Gemfile.

Whilst we can run this manually, it's more helpful to run it as part of your deploy process. The `bundle-audit` command happily returns an non-zero exit code if it finds any unpatched gems, which will cause your build to fail.

It's important to take security seriously, and this helps make it part of your day-to-day development.
