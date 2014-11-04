---
layout: post
title: Post in multiple Facebook groups using Facebook JavaScript SDK and Graph API
date: 2014-09-01 17:23:01 
disqus: true
---

I recently started a blog and I figured out that Facebook groups are one of the best places to promote my posts and quicky get some users to my site. If you have a lot of groups and you are already doing the same thing then you know that it takes a lot of time to post to all groups one by one. So I decided to make a simple script to do that in one click using Facebook JavaScript SDK and Facebook Graph API.

![My helpful screenshot]({{ site.url }}/assets/images/post-to-multiple-facebook-groups.png)

**Important!** Please don't use this code to spam your Facebook groups. You may be banned for spamming by group admins or temporary blocked by Facebook!
I guess Facebook will temporary block you from posting to groups if you post to large number of groups in short period of time (I got restricted from posting and joining groups for two weeks while testing this). 

Here is simple explanation of how it works.

The <code>getMyGroups()</code> function will load your groups into HTML drop-down list element that allows multiple selections.

{% highlight js %}
function getMyGroups() {
    FB.api('/me/groups', function(response) {
        var groupList = document.getElementById('groups');
        response.data.forEach(function(group) {
            var opt = document.createElement("option");
            opt.value = group.id;
            opt.innerHTML = group.name;
            groupList.appendChild(opt);
        });
    }, {
        scope: 'user_groups,publish_actions'
    });
}
{% endhighlight %}


After that you can call <code>postToGroups()</code> function to post in selected groups. I put this delay variable to introduce a delay
between two post requests. This may help you to avoid being banned from posting into groups by Facebook.

{% highlight js %}
function postToGroups() {
    var groupList = document.getElementById('groups');

    var selectedGroups = [];
    for (var i = 0; i < groupList.length; i++) {
        if (groupList[i].selected) {
            selectedGroups.push(groupList[i].value);
        }
    }

    var delay = parseInt(document.getElementById("delay").value, 10);

    function postOrFinish() {
        if (selectedGroups.length > 0) {
            var groupId = selectedGroups.pop();
            var message = document.getElementById("message").value;
            var link = document.getElementById("link").value;
            FB.api(
                "/" + groupId + "/feed",
                "POST", {
                    "message": message,
                    "link": link
                },
                function(response) {
                    if (response.error) {
                        console.log(response.error);
                    }
                    setTimeout(postOrFinish, delay * 1000);
                });
        } else {
            alert('All done!');
        }
    }

    postOrFinish();
}
{% endhighlight %}

And here is the full working example. First it will load and initialize the Facebook JavaScript SDK in your HTML page. After that you can call previous functions. Use your app ID where indicated.


{% highlight html %}
<!DOCTYPE html>
<html>
<head>
<title>Post to Multiple Facebook Groups</title>
<meta charset="UTF-8">
</head>
<body>
<script>
    // This is called with the results from from FB.getLoginStatus().
    function statusChangeCallback(response) {
        console.log('statusChangeCallback');
        console.log(response);
        // The response object is returned with a status field that lets the
        // app know the current login status of the person.
        // Full docs on the response object can be found in the documentation
        // for FB.getLoginStatus().
        if (response.status === 'connected') {
            // Logged into your app and Facebook.
            testAPI();
        } else if (response.status === 'not_authorized') {
            // The person is logged into Facebook, but not your app.
            document.getElementById('status').innerHTML = 'Please log ' +
                'into this app.';
        } else {
            // The person is not logged into Facebook, so we're not sure if
            // they are logged into this app or not.
            document.getElementById('status').innerHTML = 'Please log ' +
                'into Facebook.';
        }
    }

    // This function is called when someone finishes with the Login
    // Button.  See the onlogin handler attached to it in the sample
    // code below.
    function checkLoginState() {
        FB.getLoginStatus(function(response) {
            statusChangeCallback(response);
        });
    }

    window.fbAsyncInit = function() {
        FB.init({
            appId: 'your-app-id',
            cookie: true, // enable cookies to allow the server to access 
            // the session
            xfbml: true, // parse social plugins on this page
            version: 'v2.1' // use version 2.1
        });

        // Now that we've initialized the JavaScript SDK, we call 
        // FB.getLoginStatus().  This function gets the state of the
        // person visiting this page and can return one of three states to
        // the callback you provide.  They can be:
        //
        // 1. Logged into your app ('connected')
        // 2. Logged into Facebook, but not your app ('not_authorized')
        // 3. Not logged into Facebook and can't tell if they are logged into
        //    your app or not.
        //
        // These three cases are handled in the callback function.

        FB.getLoginStatus(function(response) {
            statusChangeCallback(response);
        });

    };

    // Load the SDK asynchronously
    (function(d, s, id) {
        var js, fjs = d.getElementsByTagName(s)[0];
        if (d.getElementById(id)) return;
        js = d.createElement(s);
        js.id = id;
        js.src = "//connect.facebook.net/en_US/sdk.js";
        fjs.parentNode.insertBefore(js, fjs);
    }(document, 'script', 'facebook-jssdk'));

    // Here we run a very simple test of the Graph API after login is
    // successful.  See statusChangeCallback() for when this call is made.
    function testAPI() {
        console.log('Welcome!  Fetching your information.... ');
        FB.api('/me', function(response) {
            console.log('Successful login for: ' + response.name);
            document.getElementById('status').innerHTML =
                'Thanks for logging in, ' + response.name + '!';
        });
    }

    // This function reads your Facebook groups.
    function getMyGroups() {
        FB.api('/me/groups', function(response) {
            var groupList = document.getElementById('groups');
            response.data.forEach(function(group) {
                var opt = document.createElement("option");
                opt.value = group.id;
                opt.innerHTML = group.name;
                groupList.appendChild(opt);
            });
        }, {
            scope: 'user_groups,publish_actions'
        });
    }

    function postToSelectedGroups() {
        var groupList = document.getElementById('groups');

        var selectedGroupIds = [];
        for (var i = 0; i < groupList.length; i++) {
            if (groupList[i].selected) {
                selectedGroupIds.push(groupList[i].value);
            }
        }        

        var delay = parseInt(document.getElementById("delay").value, 10);

        function postOrFinish() {
            if (selectedGroupIds.length > 0) {
                var groupId = selectedGroupIds.pop();
                var message = document.getElementById("message").value;
                var link = document.getElementById("link").value;
                FB.api(
                    "/" + groupId + "/feed",
                    "POST", {
                        "message": message,
                        "link": link
                    },
                    function(response) {
                        if (response.error) {
                            console.log(response.error);
                        }
                        setTimeout(postOrFinish, delay * 1000);
                    });
            } else {
                alert('All done!');
            }
        }

        postOrFinish();
    }
</script>
<!--
   Below we include the Login Button social plugin. This button uses
   the JavaScript SDK to present a graphical Login button that triggers
   the FB.login() function when clicked.
   -->
<fb:login-button scope="public_profile,email,user_groups,publish_actions" onlogin="checkLoginState();"></fb:login-button>
<div id="status"></div>

<label for="message">Message</label>
<textarea rows="3" placeholder="Type your message here." id="message"></textarea>

<label for="link">Link</label>
<input type="text" value="" id="link" placeholder="Your link goes here." />

<button type="button" onclick="getMyGroups();">Load Groups</button>
<label for="groups">Select your groups</label>
<select multiple id="groups"></select>

<label for="link">Delay (number of second between requests)</label>
<input type="text" id="delay" />

<button type="button" onclick="postToSelectedGroups();">Post to Groups</button>

</body>
</html>
{% endhighlight %}

You can also find this code in my [GitHub repository](https://github.com/gognjen/post-to-multiple-facebook-groups) and see it in action on [JSFiddle](http://jsfiddle.net/gognjen/jLrfad9v/).

If you are facing any problem please share it with me below in comments or send me an email. 
