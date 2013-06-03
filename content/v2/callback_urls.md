---
title: Callback URLs | Gengo API
---

# Callback URLs

__Assign a callback URL for Gengo to send you job and comment notifications. Callbacks are the most efficient way to get the latest data from us.__

Callback notifications are sent when:

* A job is ready for review.
* A job has been auto-approved (the auto_approve flag was set to 1 when submitting the job).
* A job is manually approved using our Gengo Dashboard customer interface.
* A job was ordered and put into our Queue, processed, and ready to be picked up by a translator.
* A comment has been posted for a job.

Callback URLs can be assigned per api\_key or per job. A callback URL assigned to a job takes precedence over the default callback URL set for an api_key. Up to three attempts will be made to submit data to the callback URL. The first attempt will be immediately when applicable, and subsequent attempts are an hour apart.


## Parameter formats

Parameters to callbacks are formatted in JSON and submitted with a POST call. For job-related notifications, a Job Payload will be POSTed inside a parameter named "job" as if it were a response. The following is an example of how a client might receive a callback submission for a job from Gengo:


    #!php
    <?php
    if (isset($_POST['job']))
        { $json_data = $_POST['job'];  }
    else
        { $json_data = false; }

    if ($json_data)
    {
        $data = json_decode($json_data);
    }
    ?>

Comment-related notifications are sent to the same callback when a translator submits a comment for a job. The payload will be in a parameter named "comment", and will look like this:

job_id
: _String_ for the Job ID for associated with this comment

body
: _String_ containing the body of the comment

ctime
: _String_ containing the Unix timestamp when this comment was submitted

custom_data
: _String_ containing any custom data associated with the job

Here's an example of how a client might receive a callback submission for a job comment:

    #!php
    <?php
    if (isset($_POST['comment']))
        { $json_data = $_POST['comment'];  }
    else
        { $json_data = false; }

    if ($json_data)
    {
        $data = json_decode($json_data);
        $comment = new stdClass();

        // job ID to which comment applies
        $comment->job_id = $data->job_id;

        // actual comment text$comment->body = $data->body;
        // comment creation time
        $comment->ctime = $data->ctime;

        // custom data from job (if any)
        $custom_data = $data->custom_data; // do stuff ...
    }
    ?>
