# Track Spotify Plays

A lambda function I run every twenty minutes via AWS Cloudwatch Events to make a persistent
log of my Spotify listening history.  For those "what was that song I was listening to?"
moments.

# Installation

To run this, you'll need to set up...

  * DynamoDB
  * A Spotify Application
  * AWS Lambda

Someone contributing a [SAM](https://aws.amazon.com/about-aws/whats-new/2016/11/introducing-the-aws-serverless-application-model/) config or something to automate this would be most welcome!

## DynamoDB Setup

You'll need to set up a DynamoDB table named `spotify-plays` with `played_at` as the partition key.

## Spotify Application Setup

First, [register a new Spotify application](https://developer.spotify.com/my-applications/).  Make note of the `Client ID` and `Client Secret` - you'll need them later.

Now you need to generate an auth token for your Spotify user, so that you can query the API for data specific to your account.
There's probably a better way to do this (contributions welcome!), but I cheat and do the following:

  * Set my application's `Redirect URIs` to http://localhost:8888/callback
  * Set up a fake webserver on port 8888 using `nc -l localhost 8888`
  * Visit https://accounts.spotify.com/authorize?client_id=$SPOTIFY_CLIENT_ID&response_type=code&redirect_uri=http://localhost:8888/callback&scope=user-read-recently-played in my browser
  * Make note of the `code` transmitted to `nc`
  * Use `curl` to ask for a `refresh token`: `curl -X POST -d client_id=$SPOTIFY_CLIENT_ID -d client_secret=$SPOTIFY_CLIENT_SECRET -d grant_type=authorization_code -d code=$code -d redirect_uri=http://localhost:8888/callback https://accounts.spotify.com/api/token`
  * Make note of the `refresh_token` in the response from `curl` - you'll need it in a bit.

## Lambda Setup

Set up a new Lambda function using Python 3.  I uploaded the code as a zip, including its dependencies, like so:

```
$ pip install -t . -r requirements.txt
$ zip -r /tmp/app.zip *
```

You'll need to create the following environment variables - these correspond to the values I told you to keep track of earlier:

  * `SPOTIFY_REFRESH_TOKEN`
  * `SPOTIFY_CLIENT_ID`
  * `SPOTIFY_CLIENT_SECRET`

You'll need to use KMS to encrypt these values (since the code assumes they're encrypted).

Finally, you'll want to set up a CloudWatch Event to trigger the function every so often - I set mine to trigger every twenty minutes.
