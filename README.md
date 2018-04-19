# Paubox Gem
This gem and Paubox Transactional Email HTTP API are currently in pre-alpha development.

This is the official Ruby wrapper for the Paubox Transactional Email HTTP API. The Paubox Transactional Email API allows your application to send secure, HIPAA-compliant email via Paubox and track deliveries and opens.

It extends the [Ruby Mail Library](https://github.com/mikel/mail) for seamless integration in your existing Ruby application. The API wrapper also allows you to construct and send messages directly without the Ruby Mail Library.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'paubox'
```

And then execute:

    $ bundle install

Or install it yourself as:

    $ gem install paubox

### Getting Paubox API Credentials
You will need to have a Paubox account. Please contact [Paubox Customer Success](https://paubox.zendesk.com/hc/en-us) for details on gaining access to the Transactional Email API alpha testing program.

### Configuring API Credentials
Include your API credentials in an initializer (e.g. config/initializers/paubox.rb in Rails).

Keep your API credentials out of version control. Store these in environmental variables.

	Paubox.configure do |config|
     config.api_key = ENV['PAUBOX_API_KEY']
     config.api_user = ENV['PAUBOX_API_USER']
    end

## Usage

### Sending Messages with the Ruby Mail Library

Using the Ruby Mail Library? Sending via Paubox is easy. Just build a message as normal and set Mail::Paubox as the delivery method.

	message = Mail.new do
      from            'you@yourdomain.com'
      to              'someone@somewhere.com'
      subject         'HIPAA-compliant email made easy'

      text_part do
        body          'This message will be sent securely by Paubox.'
      end

      html_part do
        content_type  'text/html; charset=UTF-8'
        body          '<h1>This message will be sent securely by Paubox.</h1>'
      end

      delivery_method Mail::Paubox
    end
    
    message.deliver!
    => {"message"=>"Service OK", "sourceTrackingId"=>"2a3c048485aa4cf6"}
    
    message.source_tracking_id
    => "2a3c048485aa4cf6"

### Sending Messages without ensuring TLS

If you want to send non-PHI mail that does not need to be HIPAA-compliant, you can allow the message delivery to take place even if a TLS connection is unavailable.

	message = Mail.new do
      from            'you@yourdomain.com'
      to              'someone@somewhere.com'
      subject         'Sending non-PHI'
      body            'This message delivery will not enforce TLS transmission.'

      delivery_method Mail::Paubox
    end
    
    message.allow_non_tls = true
    message.deliver!

### Sending Messages using just the Paubox API
	
	args = { from: 'you@yourdomain.com',
	         to: 'someone@domain.com, someone_else@domain.com',
             cc: ['another@domain.com', 'yetanother@domain.com'],
             bcc: 'bcc-recipient@domain.com',
             reply_to: 'reply-to@yourdomain.com',
             subject: 'Testing!',
             text_content: 'Hello World!',
             html_content: '<h1>Hello World!</h1>' }
             
	message = Message.new(args)
	
	client = Paubox::Client.new
	client.deliver_mail(message)
	=> {"message"=>"Service OK", "sourceTrackingId"=>"2a3c048485aa4cf6"}

	


## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/paubox/paubox_ruby.


## License

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

## Copyright
Copyright &copy; 2018, Paubox Inc.

