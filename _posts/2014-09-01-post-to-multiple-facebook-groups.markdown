---
layout: post
title: Post to Multiple Facebook Groups
date: 2014-09-01 17:23:01 
---

Everyone knows that facebook is one of best place for all the bloggers to promote or share their blog posts. If you share posts on your facebook timeline or wall than very few peoples saw your posts. In this case, facebook groups can drive more traffic to your posts.

So, first we need to join lots of groups and post our blog posts there. But it takes too much time to post all groups one by one. I'm also facing the same problem before. Now I got a solution of it and today I will share a great trick to post all facebook groups in only one click.

Note : You can't post on unjoined groups and please don't spam in groups.You may got banned for spamming in groups by group owners.
You have follow above steps again to post again on all groups.Above trick will save your lots of time and you also get decent traffic from facebook.We hope you like above post.If you are facing any problem please share it with us below in comments, we will try to solve this.


{% highlight html %}
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Post to Multiple Facebook Groups</title>
    <meta name="robots" content="noindex, nofollow">
    <!-- Bootstrap -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">

    <!-- HTML5 Shim and Respond.js IE8 support of HTML5 elements and media queries -->
    <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
    <!--[if lt IE 9]>
      <script src="https://oss.maxcdn.com/html5shiv/3.7.2/html5shiv.min.js"></script>
      <script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>
    <![endif]-->
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
            appId: "Put your App ID here",
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
        FB.api('/me/groups', function(response) {
            var newSelect = document.getElementById('groups');
            response.data.forEach(function(group) {
                console.log(group.name);
                var opt = document.createElement("option");
                opt.value = group.id;
                opt.innerHTML = group.name; // whatever property it has          
                // then append it to the select element
                newSelect.appendChild(opt);
            });
        },{scope: 'user_groups,publish_actions'});
    }

    function postToGroups() {
        var newSelect = document.getElementById('groups');                
        var que = [];        
        for (var i = 0; i < newSelect.length; i++) {
            if (newSelect[i].selected) {
                que.push(newSelect[i].value);
            }
        }
        var delay = parseInt(document.getElementById("delay").value, 10) * 1000;       
        function postOrFinish(){
            if(que.length > 0){
                var id = que.pop();                               
                FB.api(
                    "/" + id + "/feed",
                    "POST", {
                        "message": document.getElementById("message").value,
                        "link": document.getElementById("link").value
                    },                              
                    function(response) {
                        console.log(response.error);                                   
                        setTimeout(postOrFinish, delay);
                    });
            }
            else
            {
               console.log('done')
               alert('done!');
            }
        }                     
        postOrFinish();        
    }
</script>
<div class="container">

<form class="form-horizontal">            
    <div class="form-group">
      <div class="col-lg-10 col-lg-offset-2">
        <!-- Below we include the Login Button social plugin. This button uses the JavaScript SDK to present a graphical Login button that triggers the FB.login() function when clicked. -->
        <fb:login-button scope="public_profile,email" onlogin="checkLoginState();"></fb:login-button>    
      </div>
    </div>
    <div class="form-group">
      <label for="textArea" class="col-lg-2 control-label">Message</label>
      <div class="col-lg-10">
        <textarea class="form-control" rows="3" placeholder="Type your message here." id="message"></textarea>        
      </div>
    </div>
        <div class="form-group">
      <label for="link" class="col-lg-2 control-label">Link</label>
      <div class="col-lg-10">
        <input type="text" value="" class="form-control" id="link" placeholder="Your link goes here.">        
      </div>
    </div>
    
    <div class="form-group">
      <label for="select" class="col-lg-2 control-label">Selects</label>
      <div class="col-lg-10">
        <select multiple="" class="form-control" id="groups">
        </select>
        <span class="help-block">Select your groups.</span>
      </div>
    </div>
    <div class="form-group">
      <label for="link" class="col-lg-2 control-label">Delay</label>
      <div class="col-lg-10">
        <input type="text" class="form-control" id="delay">        
        <span class="help-block">Number of second between two requests.</span>
      </div>
    </div>
    <div class="form-group">
      <div class="col-lg-10 col-lg-offset-2">
        <button type="button" onclick="postToGroups();" id="post_button" class="btn btn-primary">Post</button>
      </div>
    </div>
</form>
</div>
    <!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
    <!-- Include all compiled plugins (below), or include individual files as needed -->
    <script src="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js"></script>
  </body>
</html>
{% endhighlight %}

