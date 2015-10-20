---
layout: post
title:  "Testing mail delivery (in rails) with RSpec the right way"
date:   2015-10-20 00:00:00
categories:
  - ruby
  - rails
  - testing
---

In my recent dive into the Rails world I found myself writing some tests
checking that certain emails were being delivered.  Conventional wisdom
suggested I write my tests to look something like this.

```ruby
RSpec.describe User, type: [:model, :mailer] do
describe User do
  let(:user) { User.make }

  it "sends an email" do
    expect { user.send_instructions }.to change {
      ActionMailer::Base.deliveries.count
    }.by(1)
  end
end
```

This was all fine and good until I tried to be a bit more specific in my test
and adjusted one of them to look something like the following.

```ruby
describe User do
  let(:user) { User.make }
  let(:expected_subject) { "Welcome to the site!" }

  it "sends an email" do
    subjects = ActionMailer::Base.deliveries.map(&:subject)
    expect(subjects).to_not include expected_subject
    user.send_instructions
    subjects = ActionMailer::Base.deliveries.map(&:subject)
    expect(subjects).to include expected_subject
  end
end
```

Here I sanity check prior to sending the mail that it doesn't somehow exist
within the deliveries, and then see that it has appeared after calling
`send_instructions`.  Nice to be sure we're actually seeing the behavior we
expect rather than just monitoring counts, since some behaviors can trigger
multiple emails.

With this change, my tests started failing at the pre-check.  Turns out that
`ActionMailer::Base.deliveries` was full of tons and tons of emails.  Looking
around for a way to deal with this, conventional wisdom said to do just clear
it in the before (or after) each test with something like the following.

```ruby
# spec_helper.rb

RSpec.configure do |config|
  config.before do
    ActionMailer::Base.deliveries.clear
  end
end
```

This seems like such a common thing it's hard to believe that you have to do
this manually in every project.  Turns out you don't.  You can instead use the
rspec spec type of `:mailer` in your tests, and rspec will do the clearing for
you.


```ruby
RSpec.describe User, type: [:mailer] do
  ... your tests
end
```

`ActionMailer::Base.deliveries` wil be cleared for you before (or maybe after?)
each test.  Happy testing.
