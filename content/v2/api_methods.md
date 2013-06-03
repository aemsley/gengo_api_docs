---
title: API Methods | Gengo API
---

# API Methods
Here is a list of the Gengo API methods:

[account/stats __(GET)__](/v2/account/#stats-get)
: Retrieves account stats, such as orders made.

[account/balance __(GET)__](/v2/account/#balance-get)
: Retrieves account balance in credits.

[translate/glossary __(GET)__](/v2/glossary/#glossaries-get)
: Retrieves a list of glossaries that belongs to the authenticated user.

[translate/glossary/{id} __(GET)__](/v2/glossary/#glossary-get)
: Retrieves a glossary by ID.

[translate/job/{id} __(GET)__](/v2/job/#job-get)
: Retrieves a specific job.

[translate/job/{id} __(PUT)__](/v2/job/#job-put)
: Updates a job to translate.

[translate/job/{id} __(DELETE)__](/v2/job/#comment_post)
: Cancels the job. You can only cancel a job if it has not already been started  by a translator.

[translate/job/{id}/preview __(GET)__](/v2/job/#preview-get')
: Renders a JPEG preview image of the translated text.

[translate/job/{id}/revision/{rev_id} __(GET)__](/v2/job/#revision-get)
: Gets a specific revision for a job.

[translate/job/{id}/revisions __(GET)__](/v2/job/#revisions-get)
: Gets list of revision resources for a job. Revisions are created each time a translator or Senior Translator updates the text.

[translate/job/{id}/feedback __(GET)__](/v2/job/#feedback-get)
: Retrieves the feedback you have submitted for a particular job.

[translate/job/{id}/comment __(POST)__](/v2/job/#comment_post)
: Submits a new comment to the job's comment thread.

[translate/job/{id}/comments __(GET)__](/v2/job/#comments-get)
: Retrieves the comment thread for a job.

[translate/jobs/group/{group_id} __(GET)__](/v2/jobs/#group-get)
: Retrieves a group of jobs that were submitted together, using their group ID.

[translate/jobs __(GET)__](/v2/jobs/#jobs-get)
: Retrieves a list of resources for the most recent jobs, filtered by the given parameters.

[translate/jobs/{ids} __(GET)__](/v2/jobs/#jobs-get-by-ids)
: Retrieves a list of jobs specified by a comma-separated list of job IDs.

[translate/jobs __(POST)__](/v2/jobs/#jobs-post)
: Submits a job or group of jobs to translate.

[translate/service/language_pairs __(GET)__](/v2/service/#language_pairs-get)
: Returns supported translation language pairs, tiers, and prices (in credits).

[translate/service/languages __(GET)__](/v2/service/#language-get)
: Returns a list of supported languages and their language codes.

[translate/service/quote __(POST)__](/v2/service/#quote-post)
: Returns credit quote and unit count for text based on content, tier, and language pair for job or jobs submitted.

[translate/service/quote/file __(POST)__](/v2/service/#quote-files-post)
: Uploads files to Gengo and returns a quote for each file, with an identifier for when client is ready to place the actual order. Price quote is based on content, tier, and language pair.