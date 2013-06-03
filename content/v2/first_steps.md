---
title: First steps | Gengo API
---

# First Steps

### Follow these steps to get going with the API without any bumps:

1. [Create a sandbox account and obtain an API key.](#step-1-create-a-sandbox-account-and-obtain-an-api-key)
2. [Play in the sandbox with your API keys.](#step-2-play-in-the-sandbox-with-your-api-keys)
    * [Basic example: GET language pairs](#basic-get-call)
    * [Basic example: POST to translate/jobs](#basic-post-call)
3. Set a default callback and testing it. 

## Step 1: Create a sandbox account and obtain an API key
Every API call must be authorized using an API key. While you're testing, you should use the sandbox and generate your keys from there. In this tutorial I will show you how to create a sandbox account, so that you can get your API keys to make calls to the Gengo Translate API.

First you need to create a free sandbox account. Click **Create a sandbox account** below, enter your email and password, and verify your account through the email sent to you.

[Create a sandbox account](http://sandbox.mygengo.com/sandbox)

After logging in, go to your Account section, and click **API settings** in the right-hand menu.

This is where your API keys will be stored. To create a new key, click **Generate Key**.

Each public key has a corresponding private key and callback URL. The public key is what identifies you - similar to your login name. The private key is like your password - do NOT share it with anyone, or they may be able to access your account. 

The callback URL is helpful if you want Gengo to send status updates about your job to your server or application.

Copy the public and private keys so you can use them in your application.

    #!php
    <?php
    $public_key = "rspZJxEnswelpvS0)tdwM]7uPjkcgR%@k_mN[Z1ac_3a=#EN%r=]cKwxq98-XQdK";
    $private_key = "IlUyZP5TISBSxRzEm0mil$L}-0FxeX(24W1d#TkY{qNkh42Q3B}m2)XJi_nYqrl^";
    ?>

That's it! In the next step are some API calls in action.

##Step 2: Play in the sandbox with your API keys

### Basic GET call
In this tutorial you will learn how to make API calls using some command-line PHP scripts. All of these examples assume the following constant values:

    #!php
    <?php
    $url = 'http://api.sandbox.mygengo.com/v2/';
    $public_key = "rspZJxEnswelpvS0)tdwM]7uPjkcgR%@k_mN[Z1ac_3a=#EN%r=]cKwxq98-XQdK";
    $private_key = "IlUyZP5TISBSxRzEm0mil$L}-0FxeX(24W1d#TkY{qNkh42Q3B}m2)XJi_nYqrl^";
    $response_type = 'json'; // choose response type; 'json' or 'xml'
    $header = array('Accept: application/'.$response_type);
    ?>


### Retrieving language pairs

First, make a simple call to retrieve a list of target languages supported for a given source language, in this case Japanese. The example code creates the required authentication parameters for us, but you will want to read about the [authentication process](/v2/authentication/) in the API documentation.

So, to retrieve the languages Gengo can translate Japanese into, pass "ja" as the parameter to "translate/service/language_pairs".


    #!php
    <?php
    $query = array('api_key' => $public_key, 'ts' => gmdate('U'), 'lc_src' = 'ja');
    $query = http_build_query($query);

    // calculate the API signature required for this call
    $hmac = hash_hmac('sha1', $query['ts'], $private_key);
    $query .= "&api_sig={$hmac}";
    $url .= 'translate/service/language_pairs?' . $query;

    $ch = curl_init($url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, $header);
    $response = curl_exec($ch);
    curl_close($ch);
    print_r(json_decode($response));
    ?>


Return value:

<%= headers 200 %>
<%= json :lang_pairs %>

What you get back is data about each supported target language for Japanese, including the quality tier and unit price. Here you can see that  Gengo can currently translate Japanese into English and Spanish, each at Standard, Pro, and Ultra levels, as well as with machine translation.

So, that's a very simple example of how to retrieve data through the Gengo Translate API.

### Basic POST call

Now let's submit some jobs for human translation. The sandbox allows you to quickly add fake credits so you can easily submit jobs. 

First, log in and add some fake credits to your account. Then open the example jobs-post.php script and edit fields for two jobs to submit. In the "body_src" field, add some text to be translated, and check that the source, target, and tier parameters are what you want. 

You can also send some custom data to associate with each job. This is data specific to your service and won't be touched at any time. Custom data will be returned verbatim whenever the job is requested.


    #!php
    <?php
    $job1 = array(
        'slug' => 'job test 1',
        'body_src' => 'one two three four',
        'lc_src' => 'en',
        'lc_tgt' => 'ja',
        'tier' => 'standard',
        'auto_approve' => 1,
        'custom_data' => 'some custom data untouched by Gengo.',
        );

    $job2 = array(
        'slug' => 'job test 2',
        'body_src' => 'five six seven eight',
        'lc_src' => 'en',
        'lc_tgt' => 'ja',
        'tier' => 'standard',
        'comment' => 'This one has a comment.'
        );

    $jobs = array('job_1' => $job1, 'job_2' => $job2);
    $data = array('jobs' => $jobs);

    // if you wanted these done by the same translator, add the "as_group" parameter
    // $data = array('jobs' => $jobs, 'as_group' => 1);

    $params = array(
        'api_key' => $api_key,
        'ts' => gmdate('U'),
        'data' => json_encode($data)
    );


    $hmac = hash_hmac('sha1', $params['ts'], $private_key);
    $params['api_sig'] = $hmac;
    $enc_params = json_encode($params);

    $url .= 'translate/jobs';
    $ch = curl_init($url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $params);
    curl_setopt($ch, CURLOPT_HTTPHEADER, $header);
    $response = curl_exec($ch);
    curl_close($ch);
    ?>


You'll want to review documentation for the "translate/jobs" entry-point to see what other parameters are available, but for this example job-grouping is off (which means different translators can work on each job).

So, let's post these two jobs:

<pre class='terminal'>
php -f jobs-post.php
</pre>

The response will let you know various statistics about the order:

<%= headers 200 %>
<%= json :jobs_post %>

In the response from a job `post`, one of the most important pieces of info is the `order ID`, which can be used to retrieve information about the order, such as the job IDs of the jobs placed:

<%= headers 200 %>
<%= json :jobs_order_get %>
