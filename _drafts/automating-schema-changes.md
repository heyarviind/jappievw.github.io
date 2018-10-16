---
layout: post
title: No excuse to manually apply schema changes
excerpt: The world around us has an increased focus for privacy and security. Still, there are many databases of which developers or DBA's make changes to its schema manually. 4 reasons the time is now to automate those changes.
---

While digging through some old projects I stumbled upon a wiki page containing a list of database patches for a project.

Maintaining such a list of database patches in a wiki is a primitive solution to keep track of changes and on which environments they were executed. It was relatively feature rich though: the order on page matches the order of execution, GitHub wiki provided information on who did what and when and the obvious link to the Jira issue. 

However, the way of working was far from optimal. Often we forgot to just update the wiki. Or we found ourselves debugging a software issue which after a while happened to be caused by a not-yet-applied patch. Not to forget that sometimes the patches were executed against the wrong database. Or a patch of an abandoned feature branch had accidentally been executed. Good luck tracking down the actual state of the database schema. 

![Primitive method to keep track of database changes](/images/automating-schema-changes/old-method.jpg "Primitive method to keep track of database changes")

Most of these challenges and issues can be overcome by automating the process of database changes. In this post I give a primer on automation and explain four major reasons why this is important:

0. Insight.
0. Treating environments equal
0. Predictable state
0. Speed of development

## Automation = code + tool + pipeline

For software it's quite normal to keep this is

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


## Insight.

Keeping track of who made what change to which database, for what specific reason/business value and when it was done in which environment specifically gives a tremendous amount of insight. The more details and context are collected, the more valuable. Having people maintaining a wiki is very error prone and lots of details and context is lost. When database patches are applied in a delivery pipeline all this information is highly reliable and without loss of details and context. This serves multiple purposes.

Early this year the [General Data Protection Regulation](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation) became enforceable. Companies processing personal data must take _"appropriate technical and organisational measures"_ to protect this data. Maintaining insight in all changes is crucial for this. Being able to prove to regulators that only automated processes are able to change databases is a big plus.

Another purpose is the triaging or research for a post mortem of a production incident. The more detailed and reliable insights around database changes are at hand helps in correlating changes to the operational metrics. Not having to second-guess and crawling wiki pages makes the mean time to recovery faster as well. Not to forget involving the right people at the right point in time.

## 2. Treating environments equal

Traditionally, all database patches are applied manually by developers or DBAs. Often (pre-)production databases are handled by different people/teams and thus treated differently. However, it always requires opening a terminal, finding your way into the network to the database and applying the statements of the patch one by one. This process specifically requires lots of focus and attention. This is hard and error prone. Imagine copy and paste issues in the middle of your patch, accidentally applying a patch to the wrong database, or the uncertainty during a long running statement on a busy table.

By defining all database patches in code and using tooling to apply the changes the human error component can greatly be reduced. Next to that, a predefined delivery pipeline prevents applying changes in the wrong environments. Using the role based access controls of the deployment solution can still ensure that the right people/teams trigger applying the database patches on the right database.

In the case where a DBA is applying the database changes manually lots of time can be freed up. This provides new opportunities to enhance the deployment process and  support developers in crafting the database patches. 

However, be aware that the execution of the patches can still be different in different environments due to - for example - database workload. 


## 3. Predictable state

Let's introduce the concept of database drift. A database is said to be in drift when the actual schema definition differs from the accumulation of all defined database patches. This means that the state of a database is not predictable any longer. When applying new database patches this can lead to errors. 

Quick example: when trying to create a new index in a MySQL table the error `ERROR 1061 (42000): Duplicate key name 'index_name'` can pop up. As all the database patches need to be applied the operator can create the index with a new name. Now this table unexpectedly has an extra index defined. The database is at drift. With every new change there is the risk that the patch might fail. This uncertainty is very unwanted, especially when patching a production database.

When only automated processes can change the schema of a database it can't go adrift. Having all databases in this predictable state serves multiple purposes.

As soon as you are promoting a set of database patches up to the production environment they can't fail any longer due to syntactical errors, missing objects or similar. Load on the database can obviously still throw a spanner in the works.

Tracking down a production incident due to a database error can now most often also be triaged in a non-production environment. Data protection officers and security teams will be very happy with this.


## 4. Speed of development

Although it's listed as the last one, it doesn't mean it's less important. The faster developers can make changes, test and promote them up until production environments, the more value can be delivered. By keeping database patches in version control and using tooling to apply those development speed can be increased.

A developer can build up his own local development database by just running the tooling. In case of upstream changes the tooling only has to be ran again to ensure the database is fully up to date. Any new database patch is based of the latest version. An accidental `DROP TABLE` in a developer's local database will not impact the productivity of other developers.

As applying database patches has becomes an automated routine doing more and smaller changes can be done. This will also greatly reduce the impact of each single patch.

With a bit of creativity it is even possible validate a database patch on syntax before it is merged in the main code branch. As this is not trivial, I'll discuss this in a separate post.


## Conclusion

This post pointed out 4 major reasons to treat database changes as code and having a pipeline to apply the changes. It provides full full insight in all changes happening to your databases in all environments. Next to that all environments will be treated in exactly the same way. Database drift is a fact of the past, all database will be in a predictable state. Last but not least it greatly increases the speed of development.

Automation of database changes does not take away typical challenges of database locks during deployment, executing changes which are not compatible with applications using the database, removing data which shouldn't have been removed, etc. This still requires creative craftmanship and proper technical and organizational safety nets. 

Followup posts will focus on more detailed topics. Please leave any question or feedback below in the comments!
