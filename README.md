signature
=========

[![Build Status](https://secure.travis-ci.org/markburns/signature.png?branch=master)](http://travis-ci.org/markburns/signature)

Examples
--------

Client example

```ruby
params       = {:some => 'parameters'}
token        = Signature::Token.new('my_key', 'my_secret')
request      = Signature::Request.new('POST', '/api/thing', params)
auth_hash    = request.sign(token)
query_params = params.merge(auth_hash)

HTTParty.post('http://myservice/api/thing', {
  :query => query_params
})
```

`query_params` looks like:

```ruby
{
  :some           => "parameters",
  :auth_timestamp => 1273231888,
  :auth_signature => "28b6bb0f242f71064916fad6ae463fe91f5adc302222dfc02c348ae1941eaf80",
  :auth_version   => "1.0",
  :auth_key       => "my_key"
}

```
Server example (sinatra)

```ruby
error Signature::AuthenticationError do |controller|
  error = controller.env["sinatra.error"]
  halt 401, "401 UNAUTHORIZED: #{error.message}\n"
end

post '/api/thing' do
  request = Signature::Request.new('POST', env["REQUEST_PATH"], params)
  # This will raise a Signature::AuthenticationError if request does not authenticate
  token = request.authenticate do |key|
    Signature::Token.new(key, lookup_secret(key))
  end

  # Do whatever you need to do
end
```

Pre-requisites
------------
Tested against these Ruby versions
  * 1.8.7
  * 1.9.2
  * 1.9.3
  * rbx-18mode
  * rbx-19mode

However JRuby currently fails with
java.security.NoSuchAlgorithmException: sha256 MessageDigest not available
  * jruby-18mode # JRuby in 1.8 mode
  * jruby-19mode # JRuby in 1.9 mode


Developing
----------

    bundle
    bundle exec rspec spec/*_spec.rb

Copyright
---------

Copyright (c) 2010 Martyn Loughran. See LICENSE for details.
