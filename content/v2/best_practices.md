---
title: Best Practices for Plugins & API Usage | Gengo API
---

# Best Practices for Plugins & API Usage

The Gengo API, while versatile, is only the technical bare-bones plumbing necessary to integrate translation into any application. If you're interested in building a plugin or application that uses the Gengo API, it's worth considering a few things. We've rounded up some information below that we've found helps make third party plugins and applications more successful in the long run.

##Use Gengo Passport to sign in

The Gengo API, like many others, relies on a system of API keys to sign and authenticate requests with. However, in most cases API keys as a system are less than ideal. 

Enter Gengo Passport, the simplest way to get users of your application authenticated with Gengo:

    #!javascript
    var passport = new MyGengoPassport({
        // Your application name
        appName: 'myWork',

        // HTML ID to set as the sign-in button
        button: 'mygengo_button',

        // Choose from 'largeBlue', 'largeWhite', 'smallBlue', 'smallWhite'
        buttonStyle: 'largeBlue',

        // Your custom function to run when they're signed in!
        on_authentication: function(data) {
            // data.public_key
            // data.private_key
        }
    });

All that's required to use this is to supply your application name, and users can be automatically taken through a process that authorizes your application to use their API keys. You'll need to store them yourself for later queries, but otherwise it's a simple one-step process that lets users hit the ground running on your application!

The flow will look a little something like this. At the moment it requires that the user already be signed up for Gengo, so we ask that you provide a link to register with us. We're working to release another version of Passport in the near future that will allow signing up from right within plugins.

<div id="mygengo_passport_signin" style="margin: 0 auto;"></div>

<div id="dat_form" style="display: none; margin: 0 auto 10px; width: 700px;">

    <p style="float: left; margin-right: 10px;">
                    <label style="display: block; font-weight: bold;">Your Public Key</label> <input id="public_key" style="width: 320px; padding: 3px; background: #f9f9f9; border: 1px solid #c9c9c9; border-radius: 2px; display: block;" value="" />
    </p>

    <p style="float: left;">
                    <label style="display: block; font-weight: bold;">Your Private Key</label> <input id="private_key" style="width: 320px; padding: 3px; background: #f9f9f9; border: 1px solid #c9c9c9; border-radius: 2px; display: block;" value="" />
    </p>
</div>

<script type="text/javascript" src="http://ogneg.com/js/passport.min.js"></script><script type="text/javascript">
        var passport = new MyGengoPassport({
            appName: 'myWork',
            button: document.getElementById('mygengo_passport_signin'),
            buttonStyle: 'largeBlue',
modal: false,
            on_authentication: function(data) {
                document.getElementById('public_key').value = data.public_key;
                document.getElementById('private_key').value = data.private_key;
                $('#dat_form').slideDown('slow');
            }
        });
</script>

##Use terms that make sense

When interacting with the Gengo API, data is shuttled back and forth as a "job". However, the typical interaction level a user has with Gengo finds them requesting "translations", and almost nowhere are they made aware of the concept of a "job". Consistency with the core Gengo experience ensures a user won't be confused when requesting translations in an otherwise foreign space.

## Provide in-depth headers

As the Gengo third party ecosystem continues to grow and generate traffic, it becomes more difficult to discern whether a specific version of an application is running into problems. Since the API is very "write-heavy", it's beneficial to be able to narrow down how bad translation requests make it into the system. If you provide us proper header information, we can spot this as it comes up and work with you to ensure your application is working as it needs to.

If you're not using one of <a href="/overview/client_libraries/">our programming libraries</a>, we ask that you send us a User-Agent in the following format:

Gengo app\_name; Version app\_version; your\_site\_url;

The documentation provided with the libraries should give examples on how to provide app information. Questions related to the libraries themselves can be asked on their respective repositories over at <a href="http://github.com/mygengo">our GitHub account</a>.

## Group jobs that belong together

A common issue translators encounter into is a lack of context on a job. Translations are rarely a one-to-one match, and providing as much context as possible ensures that translators will understand what actually needs to be said. <a href="http://gengo.com/api/developer-docs/methods/translate-jobs-post/">Gengo supports grouping jobs together</a> so that they'll all go through the same translator, meaning that there's little to no loss of context when the translator goes to work.

Blog posts are a superb example of this. If you're integrating with a blog client (WordPress, MovableType, etc.) to translate posts, you should send all aspects of the post at once (title, body, and anything else that might apply). This enables the translator to see the bigger picture, and it results in less hassle and possible confusion for the user.

## Be mindful of rate limits

We're pretty generous with rate limits, but it's still possible to run up against them. To avoid this happening, try to be mindful of the number of requests you make to the API.  For example, if jobs can be grouped together, it's wise to do it so it all goes to Gengo in one request instead of as several. Take advantage of our lazy-loading capabilities; we'll POST a job to you when it's completed, excessive polling isn't always necessary (or recommended!).

##Make sure users know they can escape text!

This is a bit more of a "power user" feature, but it's something we find our customers using fairly consistently. In any translation, blocks of text can be marked as "do not translate". To do this, surround the text surrounded in triple brackets. For example:

My teacher gave us [[[ 7 apples ]]] today.

In the example above, "7 apples" will be left untouched by the translator.
