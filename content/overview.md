---
title: Gengo API Overview
---

# Gengo API v2 Overview

* [Introduction](#introduction)
* [Methodology](#methodology)
	* [Call formats](#call-formats)
	* [Response formats](#response-formats)	
* [Ordering jobs](#ordering-jobs)
	* [Grouping jobs](#grouping-jobs)
	* [Specifying the translation tier](#translation-tier)
	* [Escaping text from a job](#escaping-text-from-a-job)
	* [Ordering machine translation](#order-machine-translation)
	* [Working without a database](#working-without-a-database)
	* [Using credits](#using-credits)
	* [Encoding](#encoding)
* [Job status](#job-status)
	* [Callbacks](#callbacks)
	* [Polling for job status](#polling)
	* [Cancelling jobs](#cancelling-jobs)
* [Comments](#comments)
* [The review process](#the-review-process)
	* [Approving jobs](#approving-jobs)
	* [Automatic approval when the job is ordered](#automatic-approval-at-order)
    * [Automatic approval by time-out](#automatic-approval-by-time-out)
    * [Revising jobs](#revising-jobs)
    * [Rejecting jobs](#rejecting-jobs)
* [Previewing a machine translation](#preview-machine-translation)
* [Working with the web UI](#working-with-the-web-UI)



## <a id="introduction"></a>Introduction

The Gengo API allows you to connect your app to Gengo to order human translation programmatically without using a manual web interface. Because human translation is not instant, our API is asynchronous (a bit like [Mechanical Turk](http://mturk.com)). This means that unlike some other web APIs like Flickr or Twitter, you need to allow time for translators to complete their translations. When you submit a job, Gengo's translators are notified and work on translations on a first-come, first-served basis using the Gengo web site's translation interface.

Gengo has a large team of translators (5000+), who are in all time zones around the globe. All translators who work for Gengo have passed at least one translation test.


## <a id="methodology"></a>Methodology

The Translate API attempts to follow a [REST-based architecture](http://en.wikipedia.org/wiki/Representational_state_transfer). The main resource (entry-point) revolves around jobs, and state changes on jobs should be done with PUT calls on the desired resource. For situations where certain HTTP methods cannot be used (such as PUT or DELETE), methods can be overridden with the "_method" parameter.

### <a id="call-formats"></a>Call formats
All API calls require the api\_key parameter to be sent. Authenticated calls must also be signed and sent with the api_sig parameter. If the call is by POST, parameters specific to the entry-point should be wrapped in a parameter named "data".

For example, an API call might send the following key-value pairs:

    array(
        'api_key' => ...,
        '_method'  => 'put',
        'api_sig' => ...
        'data' => ...
    )

A GET call on the other hand might have the following query string:
?api_key=...&api_sig=...&lc_src=..


### <a id="response-formats"></a>Response formats

All API responses contain either 1 or 2 key-value pairs. The second key's name  depends on whether the call was successful or not. The second key name for a successful call with an expected payload will be "response", i.e. {"opstat" : "ok", "response" : "..."} and a failed response will have second key name as "err", i.e. {"opstat" : "error", "err" : {"code" : ...}}

Currently the API can respond in either of two response formats: JSON and XML. Examples of successful and failed responses in each format can be found on the [example call and responses] page. You can specify the response format as an HTTP Header value ("Accept: application/json"), or send the format query parameter with the desired response type. If both an HTTP Header value and format parameter exist, the format parameter takes precedence. By default, responses are sent in JSON.


## <a id="ordering-jobs"></a>Ordering jobs

To order translation, make a [translate/jobs/ (POST)](/v2/jobs/#jobs-post) call (see the method page for details.) As a response, Gengo will return back an order\_id number and then begin inserting your jobs into our system. At this time, you can make a request to [translate/order/ (GET)](/v2/order/) and check the result of "jobs_queued" to see if your order is processed or not. Alternatively, once a translator starts working on one of the jobs, a callback will be sent indicating all jobs in your order are in the system.



### <a id="grouping-jobs"></a>Grouping jobs

If you want a set of jobs to be completed by one translator, you can group them when you place the [translate/jobs/ (POST)](/v2/jobs/#jobs-post) call. This is useful if you want to ensure consistency. A grouped order will take longer than a non-grouped order because the jobs will be completed sequentially instead of in parallel.


### <a id="translation-tier"></a>Specifying the translation tier (Standard, Pro, Ultra)

Part of ordering a job is to specify the translation tier, a parameter indicating the quality of the translation. There are three tiers available: Standard, Pro, and Ultra. These are described on our [Quality Policy](http://gengo.com/help/quality_policy) page.

**Note:** The "Business" tier on the Quality Policy page is equivalent to the "Pro" tier in the API.

You can also [order machine translation](#order-machine-translation).

### <a id="escaping-text-from-a-job"></a>Escaping text from a job

Sometimes you might want to include text within a job that you do not need to be translated (such as code blocks, or inline comments for the translator). To exclude text from the word count of a job, and indicate to the translator that it does not need to be translated, you can wrap text within triple square brackets [[[like this]]]. 

Please remember to close the triple brackets, and try to avoid having large areas of excluded text. It's better to break the job into parts instead.


### <a id="order-machine-translation"></a>Ordering machine translation

We've tried to make it convenient for you to use the Gengo API both for human and machine translation. While we're not big fans of the low quality machine translation can produce, we realise that not all users want, or can afford for, all of their content to be translated by humans. So it's easy for you to switch between the two, and machine translation is free. 

To order only machine translation, use the tier="machine" when you call the translate/jobs (POST) method and you will not be charged for your order. Note that machine translations have a character limit of 7500 characters.

We use Google's Translate API service to supply our machine translation. Let us know if there's another free service you'd like us to add by using the Feedback tab.

### <a id="working-without-a-database"></a>Working without a database

If you want to order translations within your app without using a database, you can use what we call "lazy loading" to request strings to be translated without having to pass an ID to our app every time you want to check on their progress or receive a translation back. 

For instance, if you have a simple PHP website that just has a few strings in it, you might not want to have to store the unique ID we return from our translate/jobs/ POST call - instead, you can use [translate/jobs/ (POST)](/v2/jobs/#jobs-post) and simply pass us a string. If it has already been requested by you, you won't be charged again for the translation - and if has been translated you will receive back the translation at that point.

### <a id="using-credits"></a>Using credits

All human translation jobs ordered using the API require up-front payment of Gengo credits. Credits are deducted from your account when you order jobs with a [translate/jobs/ (POST)](/v2/jobs/#jobs-post) call (except for machine translations ordered with the "machine" tier). Buy credits by logging into the mygengo.com site and visiting your Account page. The current version of the Gengo Translate API requires  a positive credit balance for jobs to be translated. If there are not enough credits available when a job is submitted using the API, the response will indicate this.

### <a id="encoding"></a>Encoding

Gengo expects all data to be UTF-8 encoded and will respond with UTF-8 encoded data. Make sure you encode your data in UTF-8 to avoid issues.

## <a id="job-status"></a>Job status

When a job is added to our system, it travels through a series of states (see [Job statuses](/v2/job_statuses) for a list of all statues). In general, a job passes through the following states:

The first state is "available" which means our translators can now see the job and notifications will be sent out. When a translator picks up a job, the status changes to "pending" which means a translator is currently working on that job. The next status depends on the value of the job's auto\-approve parameter when the order was placed:

* **If auto\_approve was set to 0 (false)**, the next status is "reviewable". At this time you can make a [translate/job/ (PUT)](v2/job/#job-put) call to update the status. 
* **If auto_approve was set to 1 (true)**, then the status is automatically set to "approved" and your translation is available.


### <a id="callbacks"></a>Using callbacks to get job status

Gengo recommends using callbacks as a way to be notified when a translation job is ready. Each api_key (obtained from the Account area) can be assigned a default callback URL telling Gengo where to send notifications when a job is ready for approval. Similar to PayPal’s IPN methodology, this allows an API implementation to be notified about translation jobs when they are ready. See the [Callback URLs](/v2/callback_urls/) page for more information.

### <a id="polling"></a>Polling for job status

Each job has a unique URL that will return the status of the job. A typical response will mention its status ("incomplete", "reviewable", etc.) and include the translated content if completed and approved. If the job is not yet complete, a machine translation will be offered temporarily. Excessive polling will result in a suspended account, so please be reasonable about using this method. We currently recommend polling no more than once every 30 minutes. 


### <a id="cancelling-jobs"></a>Cancelling jobs

Call [translate/job/{id} (DELETE)](/v2/job/#job-delete) to cancel a job. If you are unable to use the DELETE http method, you can fake the method by adding the _method="delete" parameter. You may only cancel translations while their status is "available" - this is to avoid wasting translator time.


## <a id="comments"></a>Comments

You can add comments to each job, whatever status it is in. Use the [translate/job/{id}/comment (POST)](/v2/job/#comment-post) method to add comments to a job that you have already submitted, and attach a comment to your job within the [translate/jobs/ (POST)](/v2/jobs/#jobs-post) call when you order.

We recommend you add comments to all jobs to give context and direction to the translator. Jobs that have comments are usually picked up sooner and translated more quickly than those that don't. Translators can also add comments to a job to add detail, or ask for clarifications.

If your system will not be able to respond to comments, or you do not plan to, it is helpful to add a comment saying something like "Our system is unable to respond to any translator comments"  to prevent the translator from waiting for a response from you. We may add a further parameter for this specific case; let us know what you think using the "Feedback" tab.

Please note that you are prohibited from soliciting contact details from a Gengo translator by our terms of service.


## <a id="the-review-process"></a>The review process

Because we charge for human translation, we offer our customers the opportunity to review and formally approve jobs. To handle the approval process in your application, you will need to build an interface for your users to review the preview text, and potentially to review a captcha.

By approving a job you are telling us that you are happy with the translation and that you require no further work from the translator for that job. Please be aware that it is extremely difficult for us to make changes or offer any kind of refund after a job has been approved.




### <a id="approving-jobs"></a>Approving jobs

To approve a job, send a [translate/job/{id} (PUT)](/v2/job/#job-put) call with the action set to "approve". You can also add feedback at this time, consisting of a rating from 1-5, a comment for the translator and a comment for Gengo. The feedback rating and comment for the translator are sent to the translator, and Gengo receives the rating, comment for translator and comment for Gengo.

It really helps us to receive feedback ratings for each translation, so can see how well our translators are doing, reward good work, and provide guidance to any translator who is not performing well. In the future, we may also use the feedback ratings to prioritize matching translators and jobs.

For this reason, it is  not helpful to use automatic feedback rating commands into your application (for example, by always rating each job as 3/5). So please only submit feedback that has been explicitly provided by a human, otherwise leave it blank.

### <a id="automatic-approval-at-order"></a>Automatic approval when the job is ordered
If you prefer not to manually approve jobs, or your system lacks the capability to do so, add the auto\_approve="true" parameter to your job requests. This will instantly approve jobs when they are submitted and provide you with the full translated text. Please note that by using the auto_approve parameter you waive your right to reject or request revisions on a translation.

### <a id="automatic-approval-by-time-out"></a>Automatic approval by time-out

If a job is in the reviewable state for a certain time, it will be automatically approved so that translators do not have to wait unnecessarily for confirmation or payment. The period is 72 hours, or 24 hours for each 1000 words, whichever is larger. For example, a 5,000 word job would have an auto-approve period of 24 hours x 5 = 120 hours.

### <a id="revising-jobs"></a> Revising jobs

If a job requires corrections, you can request them using the [translate/job/{id} (PUT)](/v2/job/#job-put) call, and the "revise" action. Please be as descriptive as possible with your comment when you request a correction.

### <a id="rejecting-jobs"></a>Rejecting jobs

If you are unhappy with a translation from Gengo, you may reject the job and either receive a credits refund, or pass the job to a different translator. For more details on what you can expect from our translation, please visit our [Quality Policy](http://gengo.com/help/quality_policy/) page, which outlines what constitutes a fair or unfair rejection.

All rejections are reviewed by our staff, and we take each one seriously. Normally we will contact the translator and review what has gone wrong with that particular translation. While we are obviously happy to uphold fair rejections, if you persistently reject jobs without reason, your account will be suspended as this kind of behaviour is unfair to the translator and time-consuming for us.

To reject a job, use the [translate/job/{id} (PUT)](/v2/job/#job-put) call with the action parameter set to "reject". To ensure that rejections are made by humans (because we don't want machines rejecting translations), we require you to complete a captcha when you submit a rejection. See the [translate/job/{id} (PUT)](/v2/job/#job-put) page for more details.


## <a id="preview-machine-translation"> </a>Previewing a machine translation

If you have ordered human translation, and are still waiting for the job to be reviewable, you can see a machine translation preview by including the pre_mt="1" parameter. This might be useful if you have new content that you want to appear on a page, and you don't want to wait for the human translation before publishing.

## <a id="working-with-the-web-UI"></a>Working with the web UI

If you place an order using the web form, you can retrieve it using the API by using the job ID in calls like [translate/job/{id} (GET)](/v2/job/#job-get). The job ID is found on your customer dashboard.  Please note that jobs ordered using the web form do not trigger API callbacks.

Jobs placed through the API are visible on your customer dashboard at <a href="http:\\mygengo.com">mygengo.com</a>.






