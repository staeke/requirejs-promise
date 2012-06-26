= requirejs-promise =

This is a plugin for Require.JS that allows modules to resolve asynchronously via jQuery Promises. The current AMD standard does not specify how to deal with asynchronous dependencies, and as such it is perfectly reasonable that Require.JS does not handle them. That said, I still find them unavoidable from time to time, so here we are.

== Status ==

IMPORTANT: THIS IS JUST A STUB! NO ACTUAL CODE YET! NOTHING TO SEE HERE!

== Licence ==

I'm putting this out under the Simplified BSD Licence (see LICENCE). Go nuts.

== Requirements ==

I'm starting off with jQuery Promises / Deferred objects, so you'll need to have jQuery (1.7.x or higher). I intend to keep up with jQuery releases and won't be trying to maintain support for older versions (although I won't be actively trying to cut it either), so I may need your help to maintain compatibility (if it's important to you).

You'll also (obviously) need Require.JS. Likewise, I'll always be targetting the current version.

As this is just a plugin, it doesn't make sense to have further requirements.

I do intend to eventually extend support to other implementations of Promises (with your help) so jQuery won't always be needed.

== Usage ==

You shouldn't need to include this directly in your HTML as a script element.

Configure Require.JS so that it has a path to resolve, for example:

    require.config({
        config: {
            paths: {
                // remember to leave out the .js on the end
                'promise': '/path/to/requirejs-promise'
            }
        }
    });

Your asynchronous module will probably look something like this:

    define('myAsyncModule', ['jquery'], function($) {
        var deferred = new $.Deferred(),
        myModule = {};

        // we'll use AJAX here, but you could do anything asynchronous
        $.ajax({
            url: '//domain/path/to/script'
            complete: function(jqxhr, textstatus) {
                if ($.inArray(jqxhr.status, [0, 200, 204, 304])) {
                    // TODO: do other stuff for successful AJAX
                    myModule.response = jqxhr.responseText;
                    // if your consumers are expecting something, give it to them
                    deferred.resolve(myModule);
                } else {
                    // TODO: do other stuff for failed AJAX
                    deferred.reject(null);
                }
            }
        });

        return deferred.promise();
    });

Then, when it comes time to actually load in an asynchronous Promise-based dependency, do something like this:

    require(['promise!myAsyncModule'], function(asyncModule) {
        // TODO: do something cool with asyncModule.response
        ...
    });

The goal of this little plugin is to make module usage completely seemless. Module resolution is asynchronous within Require.JS anyway, so (assuming everything goes according to plan) nobody downstream should notice that the dependency had a Promise.
