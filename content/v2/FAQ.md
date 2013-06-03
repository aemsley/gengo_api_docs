---
title: FAQ | Gengo API
---

# Frequently Asked Questions



This page contains answers to a few questions we're frequently asked.

## Maintaining quality

__How can I be sure the same word or phrase is always translated in the same way?__


- __Use a style guide or a glossary.__ You can create a style guide and/or a glossary and send it with translations. A glossary can give preferred translations for specific words. A style guide can give information about audience (who will be reading the translation), general tone for the translation (such as very formal), branding, product names, or other product-related information. [Contact Gengo](mailto:api@gengo.com?Subject=Style%20Guide%20and%20Glossary) for instructions.

- __Use the opportunity to review the job and send corrections before approving it.__  When your job has the "reviewable" status, review the translation. If there are errors, return it to the translator with your corrections.
- __Use the comment parameter to give the translator instructions and context.__ 
 Use the [translate/job/{id}/comment (POST)](/v2/job/#comment-post) method to add comments to a job that you have already submitted, and attach a comment to your job in the [translate/jobs/ (POST)](/v2/jobs/#jobs-post) call when you order. However, do not use the comment field for very long instructions.


__How can I give more detailed instructions and context to the translator?__

The comment field is not meant for lengthy comments. If you need to give detailed instructions, you can enter instructions the comment field on how the translator can find detailed information. One strategy we've seen is to post a publicly-viewable document on Google docs and to put the URL for the document in the comment.




## Best implementation practices


__When I send a group of jobs, how can I be sure the translator sees them in the correct order?__

When sending text in a group, the jobs are not stored in order from first to last. Send  your jobs with the "position" parameter to ensure the translator sees the job in a logical order. This can also help improve translation quality, by providing more context for the jobs.

Use the position parameter when sending a <a href="http://developers.gengo.com/v2/payloads/">payload</a>.


__How can I track my jobs using something I assign, instead of just using the Gengo IDs?__


To keep track of your order using a value that you assign add the "custom\_data" parameter to your payload. (Each job and order will still receive an ID.)

The custom\_data parameter is similar to the slug parameter (which can be thought of as a "title” for your content) except for following:

- __slug__: required for each job, reserved for internal Gengo use, can be generic and non-unique
- __custom\_data__: not required, for developers to use, unique



__What is the best size for a job group?__

If you send a jobs in a single group, one translator is assigned to the entire group. This can affect the time that it takes to finish the translations simply due to the volume of the work. Additionally, some translators may be reluctant to take the job if it is very large, so it may take some time before the group is picked up for translation.

The recommended maximum number of jobs for a group is 300 (like the movie).

## Other Considerations
__Why did you return fewer jobs than I sent  you?__


To prevent a user being charged twice for a translation, we do not charge for duplicate text that is sent in the same payload. For example, if you send 10 jobs, but two of them are the duplicates of others in the payload, you're only charged for 8 jobs and only 8 jobs are returned. 

Please take this into account when developing your app, so that receiving fewer jobs than you sent doesn't cause a problem, such as an infinite hang while the program waits for the duplicate jobs.

See “There are repeated jobs in the jobs payload”, <a href="http://developers.gengo.com/v2/jobs/#jobs-post">here</a>.

__How can I force a second translation of something you already translated?__

Let’s assume you send one post job to Gengo. If you send the same post again, 
the previous translation is returned and we don't charge you. (We call this "lazy loading".) If you want Gengo to treat this post as a new job, send the "force" parameter with the <a href="http://developers.gengo.com/v2/payloads/">payload</a>. The force parameter will override this call and treating the post as a new job to be translated.


Please look <a href= "http://developers.gengo.com/overview/#working-without-a-database">here</a> and <a href="http://developers.gengo.com/v2/jobs/#job-group-get">here</a> for more about “lazy loading".


__When I am requesting a translation with the "Ultra" translation tier, how can I ensure my jobs are proofread?__

Currently, the Gengo back end does not support proofreading through grouped jobs. If you are sending a grouped job at the "Ultra" tier, either:

- send each job individually
- send them without the as_group parameter


For more on the as_group parameter, click <a href="http://developers.gengo.com/v2/jobs/#jobs-post">here</a>.

