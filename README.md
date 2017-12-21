# Track Spotify Plays

A lambda function I run every twenty minutes via AWS Cloudwatch Events to make a persistent
log of my Spotify listening history.  For those "what was that song I was listening to?"
moments.

# Installation

To run this, you'll need to set up...

  * DynamoDB
  * AWS Lambda

Someone contributing a [SAM](https://aws.amazon.com/about-aws/whats-new/2016/11/introducing-the-aws-serverless-application-model/) config or something to automate this would be most welcome!

## DynamoDB Setup

You'll need to set up a DynamoDB table named `spotify-plays` with `played_at` as the partition key.

## Lambda Setup

  * You'll need to create the following environment variables:
    * `SPOTIFY_REFRESH_TOKEN`
    * `SPOTIFY_CLIENT_ID`
    * `SPOTIFY_CLIENT_SECRET`
  * Explain KMS
  * Set up CloudWatch Event
