---
title:  "Testing ActiveJob with RSpec"
date:   2015-07-13 09:00:00
---

I recently found myself experimenting with [ActiveJob], which provides a standard interface for various queueing backends. As is the Rails Way&trade;, the documentation doesn't mention how to test jobs through RSpec, so I had to discover that for myself!

## ActiveJob Basics

Jobs live in `app/jobs` and look like this:

{% highlight ruby %}
# app/jobs/example_job.rb
class ExampleJob < ActiveJob::Base
  queue_as :default

  def perform(parameters)
    # do processing
  end
end
{% endhighlight %}

For these examples, I'm using [DelayedJob], which stores everything in the application's database. But the job code is exactly the same, no matter what background processor you use.

We can add jobs to the queue, by calling

{% highlight ruby %}
ExampleJob.perform_later(parameters)
{% endhighlight %}

Note, this only adds the job to the queue. In order to process the jobs, you need to keep a rake task running:

{% highlight ruby %}
  rake jobs:work
{% endhighlight %}

I'd usually add this to my [Procfile], so that I can export it as an Ubuntu `upstart` process.

## Testing

After a little digging, I discovered that testing ActiveJob is really straightforward:

{% highlight ruby %}
# spec/jobs/example_job_spec.rb
require "rails_helper"

describe ExampleJob do
  describe "#perform" do
    it "does some example work" do
      expect{ ExampleJob.perform_now(parameters) }.to ...
    end
  end
end
{% endhighlight %}

The important call there is `perform_now` which runs the job directly, instead of queueing it.

**You can see a [fuller example](https://github.com/leeky/gatwick/blob/rlc-fetch-events/spec/jobs/event_fetcher_job_spec.rb#L3-L20) in my [Gatwick] project.**

[DelayedJob]: https://github.com/collectiveidea/delayed_job
[ActiveJob]: https://github.com/rails/rails/tree/master/activejob
[RSpec]: http://rspec.info/
[Procfile]: http://ddollar.github.io/foreman/
[Gatwick]: https://github.com/leeky/gatwick/

