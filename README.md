# RGB against COVID 🎨

The goal is to build an awesome application that solves
some kind of challange related to the COVID-19. Another important
aspect of this project that in some way it has to integrate
RGB LED lighting. However you don't have to deal with the LED part,
it will be available to you through an HTTP interface
as a service. The lights will be streamed 24/7 online and
you can control them with HTTP requests.
Other than these, the application might do ANYTHING.
A few examples:

- If the value of some stock reaches some bottom
threshold, the LEDs start to rapidly flash in red color as a warning
- The LEDs show the current infection rate color coded in real time
- Some kind of educational mini game that somehow uses the LEDs

These are just examples, the more creative your solution the better.

The LEDs that will be streamed is only a
prototype configuration, you can imagine that it could be
built anywhere in any size or shape.

## Functional requirements

- The application needs to have a web component (It might
have other components than a web backend and frontend
but that's the minimum).
- It has to be deployed to somewhere that is available to anyone.

## Extra points

- At least 40% unit/integration test coverage
- Proper CI/CD pipeline
- Documentation(e.g.: markdowns on github that describes the available features and how to use them)

## Controlling the LEDs

You can control the LEDs through an HTTP interface. You essentially need
to send a POST request to http://backend.bassboost.hu/hackathon/send/{key}
You need to also provide a JSON in the request body which describes
how the lights turn on.

The request body should look like this:

```json
{
  "configText": <configuration>
}
```

The value of `configText` should be a string which is a parseable json where the quotation marks are all escaped with the `\` character.
This is where you can find the specification of the `configText` value:
[LED configuration](bassboost_config.md)

The LEDs will only respond if the response has a 200 OK status. If you get
a 400 Bad Request, you will also receive some additional information about
the nature of the problem.

### Music synchronization

You can also synchronize the LEDs to the music you are listening on Spotify and make music reactive effects.
For that you also need to send in you configuration first as stated above, then you need to send a POST
request to http://backend.bassboost.hu/hackathon/sync/{key}

The request body should look like this:

```json
{
  "spotifyAccessToken": String,
  "spotifyRefreshToken": String
}
```

For more information on how to obtain tokens from Spotify, turn to their [documentation](https://developer.spotify.com/documentation/general/guides/authorization-guide/).

## Reserve the LEDs

Since it's a shared resource we need a system in order to avoid chaos.
If we were to randomly send requests then we will ultimately just screw up
each others tests. For this reason you need to reserve the LEDs before
you can send configuration requests to it.
This also works via HTTP requests.

Each people is going to have a private key which can be used 
to lock the resource so others are not able to send configurations to it.

### Lock

In order to claim the LEDs to yourself you need to send a POST request
to http://backend.bassboost.hu/hackathon/lock/{key} where the key path variable
is your private key. You also need to send another parameter in the request body
in the following format:

```json
{
  "duration": <Integer value between 1 and 20>
}
```

It is the number of minutes you'd like to keep your reservation active. It
has a minimum value of 1 and a maximum value of 20. There's nothing to stop you from repeating the reservation when it ends, it's just to ensure that your reservation has an expiration time and if you forget to unlock, others might
be able to use it anyway.

The reservation is only successful if the response has a 200 OK status.
If you get a 400 Bad Request, you will get additional information about
the nature of your problem or you might get 409 Conflict which means
someone else is already locking the resource

### Unlock

If you don't want to keep up your reservation anymore you can send a POST request to http://backend.bassboost.hu/hackathon/unlock/{key} without
a request body.

Your reservation ends immediately and other might lock it.
