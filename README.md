marytts-server
===========

HTTP server front end to [MaryTTS](http://github.com/marytts/marytts) (text-to-speech engine).

Usage
-------

The server sends wave formatted audio for the given text and voice inputs.

The following main query string parameters can be specified:
- `text`: The text to turn into speech
- `locale` (optional): The voice locale (e.g. "de" for German or "en_US" for 
    American English). It defaults to the first langauge of the 
    `Accept-Language` header, and otherwise the default locale for the server.
- `gender` (optional): Allows you to select the voice for the locale based on
  its gender (i.e. 'male' or 'female').
- `voice` (optional): If there are multiple MaryTTS voices installed for a
  language, you can choose the specific one with this.
- `style` (optional): Allows you to specify a style for voices that support
  multiple styles.
- `effects` (optional): Allows you to specify effects for the voice.

To play the audio on a web page you can embed it with the `<audio>` tag, e.g.:
```
<audio autoplay>
  <source src="maryserver.example.com/?text=Hello+World" type="audio/wav">
</audio>
```

Securing requests
-------

If you wish to secure your requests so that a third party couldn't use your
text-to-speech server for audio clips not related to your application, you can
secure `marytts-server` by setting the `SECRET` environment variable, which will
then require requests to be signed with that secret using HMAC-SHA256.

You can also specify the `expires` (Unix timestamp) parameter with your requests
so that they can only be played for a certain amount of time.

The string you need to sign is the concatenation of the various options (with
blanks for the ones you didn't specify), i.e.
`text + locale + gender + voice + style + effects + expires`.

The HMAC-SHA256 result should be sent in the `signature` parameter. 
So, for example, if your `SECRET` was `` and you want a request that expires
in say 2020, then the fully signed URL would be:
```
maryserver.example.com/?text=Hallo&locale=de&expires=1607670000&signature=
```

Here's some sample PHP code for signing a request an embedding it into a page:


Deployment
---------

MaryTTS server uses Jetty and it can easily be deployed to Heroku. Just clone
this repository and then push it to a Heroku remote. That will trigger the Maven
build which will fetch all of the necessary dependencies.

To build it locally with Maven run `mvn install` and then to execute the server 
with the following command:
```
java -cp target/classes:target/dependency/*:target/voices/lib/* maryserver.MaryServer
```

Potential enhancements
-----------

If you want to add more voices or languages, you can modify `pom.xml` to pull in 
more language jars and download more voices. 

A list of voices is shown in the 
[marytts-components.xml](https://github.com/marytts/marytts/blob/master/download/marytts-components.xml)
file in the marytts project.

Another idea would be to support ogg/vorbis encoding using 
[VorbisJava](https://github.com/Gagravarr/VorbisJava).

Utilizing caching (perhaps via Redis) could also be useful.

License
-------

This code itself itself is licensed under the simplified BSD License (see License.md)

MaryTTS is licensed under the 
