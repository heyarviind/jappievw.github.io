---
title: "How to automate schema changes"
#date: 2018-10-17T15:54:14-04:00
description: "To be defined"
featured_image: ""
draft: true
---

So, why not treat the database schema as code, use a tool to apply the code to a database and have a delivery pipeline promote this to testing, acceptance and production environments? Indeed, just like we do with regular software these days.

How to automate schema changes? Automation = code + tool + pipeline. 

1. Code:
    1. Having a definition of the schema.
    1. Having that definition in a version controlled repository.
    1. Having processes in place to review changes to the main branch of the repository by knowledgeable peers.
    1. Code and a repository fosters democracy in database changes.
1. Tool:
    1. Able to find the difference between the defined schema and the actual schema in the database.
    1. Able to transition the database step by step up until it matches the defined schema. Idempotently.
    1. Raise a detailed error in case the transitioning fails.
1. Pipeline:
    1. Responsible for managing the process to promote a schema definition to different environments.
    1. Runs the tool with the right parameters for each schema and environment.
    1. Only promotes the definition up until production when no error occurred.
    1. Provides interface for the output of the tool.
    1. Manages who is allowed to promote.
