# Dynalock

*Dynalock* is a distributed lock that uses *DynamoDB*.

## Background

At *Tourlane* we were running cronjobs through *Amazon ECS*. Once the cluster became
too big, *CloudWatch* was scheduling tasks, even if the same task was already
present. *Dynalock* solves this issue, ensuring that any new task exits and fails
before starting the real work.

The first assumption is that something like this should exist, but normally
not as a command line program (if you found any, please let us know). So we
created our own.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'dynalock'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install dynalock

You need to create a table in *DynamoDB* with "id" as a primary key, and "expires" as expires.
The default table name is "locks".

## Usage

Set the following environment variables to their proper values:

    AWS_ACCESS_KEY_ID
    AWS_REGION
    AWS_SECRET_ACCESS_KEY

### Usage inside ruby

```ruby
require 'dynalock'

include Dynalock::Lock

acquire_lock(context: "my_lock", table: TABLE, owner: @owner, expire_time: 10)
refresh_lock(context: "my_lock", table: TABLE, owner: @owner, expire_time: 10)
with_lock(context: "my_lock", table: TABLE, owner: @owner) { "Only run this" }
```

Most of the paramenters are optional

```ruby
acquire_lock(context: "my_lock")
refresh_lock(context: "my_lock")
with_lock(context: "my_lock") { "Only run this" }
```

### Usage through the command line

```sh
$ dynalock my_program
```

This will try to acquire a lock in *DynamoDB* for 10 seconds, and refresh it every 5 seconds and run your program. The command will be context.

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run
`rake test` to run the tests. You can also run `bin/console` for an interactive
prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To
release a new version, update the version number in `version.rb`, and then run
`bundle exec rake release`, which will create a git tag for the version, push
git commits and tags, and push the `.gem` file to
[rubygems.org](https://rubygems.org).

## How to run tests locally

By default, the test suite will try to connect to AWS DynamoDB.

To work locally instead, you can use the [local version of DynamoDB](https://aws.amazon.com/about-aws/whats-new/2018/08/use-amazon-dynamodb-local-more-easily-with-the-new-docker-image/) via Docker:

```
docker run -p 8000:8000 amazon/dynamodb-local
```

Then instruct the test suite to create the table & target that local instance:

```
DYNAMODB_CREATE_TEST_TABLE=1 DYNAMODB_ENDPOINT=http://localhost:8000 bundle exec rake
```

## Contributing

Bug reports and pull requests are welcome on *GitHub* at
https://github.com/tourlane/dynalock. This project is intended to be a safe,
welcoming space for collaboration, and contributors are expected to adhere to
the [Contributor Covenant](http://contributor-covenant.org) code of conduct.

## License

The gem is available as open source under the terms of the [MIT
License](https://opensource.org/licenses/MIT).

## Code of Conduct

Everyone interacting in the *Dynalock* project’s codebases, issue trackers, chat
rooms and mailing lists is expected to follow the [code of
conduct](https://github.com/tourlane/dynalock/blob/master/CODE_OF_CONDUCT.md).

