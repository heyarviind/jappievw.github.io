---
layout: post
title: automated database changes = code + tool + pipeline
excerpt: tbd
---

So, why not treat the database schema as code, use a tool to apply the code to a database and have a delivery pipeline promote this to testing, acceptance and production environments? Indeed, just like we do with regular software these days.

Required components:
1. Code:
    1. Having a definition of the schema.
    1. Having that definition in a version controlled repository.
    1. Having processes in place to review changes to the main branch of the repository by knowledgeable peers.
1. Tool:
    1. Able to identify the state of the schema in a database.
    1. Able to transition a database step by step to match the definition in code. Idempotent
    1. Raise a detailed error in case the transitioning fails.
1. Pipeline:
    1. Responsible for managing the process to promote a schema definition to different environments.
    1. Runs the tool with the right parameters for each schema and environment.
    1. Only promotes the definition up until production when no error occurred.
    1. Provides interface for the output of the tool.
    1. Manages who is allowed to promote.
