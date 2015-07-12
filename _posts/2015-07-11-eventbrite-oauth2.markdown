---
title:  "Connecting to the Eventbrite API using OAuth2"
date:   2015-07-11 14:30:00
---

In a [recent project](https://github.com/leeky/gatwick), I needed to pull data from the Eventbrite API. Unfortunately, they didn't appear to have published their own Rubygem for this purpose.

Eventually, I settled on [Envoy's gem](https://github.com/envoy/eventbrite), which wrapped most of the API calls in simple Ruby methods, but lacked the ability to authenticate against the [Eventbrite API](https://www.eventbrite.com/developer/v3/).

## What's this OAuth thing anyway?

The [OAuth] web site, describes it as an open protocol to allow secure authorization in a simple and standard method from web, mobile and desktop applications.  You've seen OAuth in use if you've ever connected your Twitter account to a third-party client; it's the go-to protocol for anyone who wants to offer API access where you're acting on behalf of someone else.

I remember a few years ago attending a hack day and spending the majority of the weekend trying to get a meaningful response back from an API that required you to authenticate using OAuth. The standard was hard to understand, and you had to include lots of OAuth related headers in any requests to any API which used it.

Fortunately, I was pleasantly surprised when I dived into the documentation provided by the [OAuth2 gem](https://github.com/intridea/oauth2). The OAuth2 standard has been greatly simplified, mainly because it only deals with authentication and gets out of the way when it comes to serving API responses.

## Connecting to Eventbrite

Firstly, I had to register on the Eventbrite Developer site. Next, I had to provide details of the application I was creating. A few moments later and my access was granted and I was provided with a `client_id` and `client_secret`.

I wasn't familiar with the OAuth2 gem, so I spent a few minutes playing it in `pry` to get familiar with the way it works.

## Getting Permission

I pass the client_id and client_secret we were given by Eventbrite, along with their OAuth endpoint:

{% highlight ruby %}
require 'oauth2'
client = OAuth2::Client.new(CLIENT_ID, CLIENT_SECRET, site: "https://www.eventbrite.com/oauth/authorize")
{% endhighlight %}

Then I request an OAuth authorisation URL from Eventbrite. When I do so, I provide the callback URL I wish to be called when the end user successfully authorises my request. Because this is done in the browser using an HTTP redirect, I can use my `localhost:3000` development URL and it will still work:

{% highlight ruby %}
client.auth_code.authorize_url(redirect_url: callback_admin_oauth_url)
# => https://www.eventbrite.com/oauth/authorize?client_id=[CLIENT_ID]&redirect_url=http%3A%2F%2Flocalhost%3A3000%2Fadmin%2Foauth2%2Fcallback&response_type=code
{% endhighlight %}

If I visit the returned URL in my web browser, I'm prompted to login to Eventbrite. Next, I see the following screen:

![](/assets/images/oauth-permissions.png)

When I click 'ALLOW', I am redirected back to my local application. An authorisation code parameter is appended to the URL. This code is then exchanged for a permanent access token by making a HTTP POST (on the server side) back to the Eventbrite API. Finally, I have receive an access token that I can use from now on, which allows me to access the API on behalf of the user linked to it.

## Using the API

For example, to receive a list of all the events owned by the user:

{% highlight ruby %}
Eventbrite.token = [ACCESS TOKEN]
Eventbrite::User.owned_events({ user_id: 'me' })
{% endhighlight %}

In the background, the Eventbrite gem is adding an `Authorization: Bearer ...` HTTP header to every request.

NB: It took quite a lot of digging around before I discovered that Eventbrite uses a special id of `me` to refer to the user we are acting on behalf of.

## Conclusion

The original OAuth protocol was really difficult to work with. Additionally, its requirement of complex authentication of ever request to an API created a lot of overhead at the API providers end.

The newer OAuth 2 standard removes the complexity, which makes it much easier to work with.

[OAuth]: http://oauth.net/
