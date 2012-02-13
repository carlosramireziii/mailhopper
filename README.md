# Mailhopper  [![Build Status](https://secure.travis-ci.org/cerebris/mailhopper.png)](http://travis-ci.org/cerebris/mailhopper)

Mailhopper provides a simple ActiveRecord-based queue for asynchronous delivery of email in Rails apps.

Why use Mailhopper to queue your email?

 * Mailhopper captures the full content and headers of emails at their time of creation. It can handle multiple MIME types and attachments.
 * If email can't be delivered from your queue (e.g. your smtp server is down), you can retry delivery until successful.
 * Emails can be accessed at any time, even after they've been sent.

The complete rationale is explained in this blog post: http://www.cerebris.com/blog/2011/09/07/tame-rails-email-dragons-with-mailhopper/

Mailhopper is intended to be used along with a delivery agent such as DelayedMailhopper, which uses DelayedJob to deliver email from the Mailhopper queue. Without a delivery agent, emails will accumulate in the Mailhopper queue but won't be delivered.

DelayedMailhopper can be found here: https://github.com/cerebris/delayed_mailhopper

## Requirements

Rails 3.1+

## Installation

Add to your project's Gemfile:

```
gem 'mailhopper'
```

Install with bundler:

```
bundle install
```

Generate default initializer and migration files:

```
rails generate mailhopper
```

*Before migrating your database, please take a moment to review the `CreateEmails` migration that has been generated.* In particular, please review the limit to the `content` field, which has been set to a safe but very large size (100MB characters). You may wish to change this based upon your needs and particular database.

When you're ready, migrate your database:

```
rake db:migrate
```

Don't forget to also install a delivery agent, such as DelayedMailhopper, so that emails will be delivered from your queue !!!
  
## Configuration

If you want all of your application's email to be queued with Mailhopper, configure mailers either in application.rb or your application's environment-specific configuration files:

```
MyApp::Application.configure do
  config.action_mailer.delivery_method = :mailhopper
end
```

Alternatively, or additionally, configure individual mailers to use Mailhopper:

```
class MyMailer < ActionMailer::Base
  ActionMailer::Base.delivery_method = :mailhopper
end
```

## Options

The following options can be configured in your initializer (config/initializers/mailhopper):

```
Mailhopper::Base.setup do |config|
  # The base email class used by Mailhopper
  config.email_class = Mailhopper::Email

  # The base mailer class used by Mailhopper
  config.mailer_class = Mailhopper::Mailer

  # The method used by the delivery agent to deliver emails from your queue
  config.default_delivery_method = :smtp
end
```

It's preferable to leave these options out of your initializer if the defaults, shown above, are acceptable. Delivery agents may override some defaults (e.g. DelayedMailhopper sets email_class = DelayedMailhopper::Email).

## Copyright

Copyright (c) 2011 Cerebris Corporation. This is free software released under the MIT License (see MIT-LICENSE for details).
