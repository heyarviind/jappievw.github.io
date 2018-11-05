---
title: "Four Reasons to Automate Schema Changes"
date: 2018-10-16T15:54:14-04:00
author: "Jasper van Wanrooy"
description: "Historically, most databases received their schema updates manually. For software it is already common practice to automate deployments. 4 reasons the time is now to automate database changes as well."
featured_image: "four-reasons-to-automate-schema-changes-old-method.jpg"
images:
- "/images/blog/four-reasons-to-automate-schema-changes-old-method.jpg"
draft: false
---

While digging through some old projects I stumbled upon a wiki page containing a list of database patches for a project. Maintaining such a list of database patches in a wiki is a primitive solution to keep track of changes and on which environments they were executed. It was relatively feature rich though: the order on page matches the order of execution, GitHub wiki provided information on who did what and when and the obvious link to the Jira issue. 

However, the way of working was far from optimal. Forgetting to update the wiki. Debugging a software issue which after a (long) while happened to be caused by a not-yet-applied patch. Applying patches to the wrong database. That forgotten temporary database patch to demo a feature branch which causes headaches weeks/months later. Having to apply an emergency patch to the production database when the DBA is just on its way home. All real life examples people had or still have. Also all examples are caused by relying on manual work.

Most of these challenges and issues can be overcome by automating the process of database changes. Why not treat the database schema as code, use a tool to apply the code to a database and have a delivery pipeline promote this to testing, acceptance and production environments? Just like we do for our software.

In this post I'll explain four major reasons why this is important:

1. Insight.
1. Treating environments equal
1. Predictable state
1. Speed of development

## Insight.

Insight in changes to the database means keeping track of who made what change to which database, for what specific reason/business value and when it was done in which specific environment. The more details and context are collected the more valuable the insight is. Having people maintaining a wiki is very error prone and lots of details and context is lost. When database patches are applied in a delivery pipeline all this information is highly reliable and without loss of details and context. This serves multiple purposes.

Early this year the [General Data Protection Regulation](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation) became enforceable. Companies processing personal data must take _"appropriate technical and organisational measures"_ to protect this data. Maintaining insight in all changes is crucial for this. Being able to prove to regulators that only automated processes are able to change databases is a big plus if not a requirement.

Another purpose is the triaging or research for a post mortem of a production incident. The more detailed and reliable insights around database changes are at hand helps in correlating changes to the operational metrics. Not having to second-guess and crawling wiki pages makes the mean time to recovery faster as well. Not to forget involving the right people at the right point in time.

## 2. Treating environments equal

Traditionally, all database patches are applied manually by DBAs, sysadmins or developers. Often (pre-)production databases are handled by different people/teams and thus treated differently. However, it always requires opening a terminal, finding your way into the network to the database and applying the statements of the patch one by one. This process specifically requires lots of focus and attention. This is hard and error prone. Imagine copy and paste issues in the middle of your patch, accidentally applying a patch to the wrong database, or the uncertainty during a long running statement on a busy table.

By defining all database patches in code and using tooling to apply these the human error component can greatly be reduced. Next to that, a predefined delivery pipeline prevents applying changes in the wrong environments. Using the role based access controls of a deployment solution can still ensure that the right people/teams are able to apply the database patches on the right database environment.

In the case where a DBA is applying the database changes manually lots of time can be freed up. This provides new opportunities to enhance the deployment process and  support developers in crafting the database patches. 

However, be aware that the execution of the patches can still be different in different environments due to - for example - database workload. 


## 3. Predictable state

Let's introduce the concept of database drift. A database is said to be in drift when the actual database schema differs from the schema definition in code or the accumulation of database patches in the wiki. It boils down to the fact that the state of the database schema is not predictable any longer. When applying new patches this can lead to errors.

Quick example: when trying to create a new index in a MySQL table the error `ERROR 1061 (42000): Duplicate key name 'index_name'` can pop up. In the rush to get the database patches applied the engineer can quickly create the index with a new name. Now this table unexpectedly has an extra index defined. The database is now at drift. With every new change there is the risk that the patch might fail. This uncertainty is very unwanted, especially when patching a production database.

When only automated processes can change the schema of a database it can't go adrift. Having all databases in this predictable state and being able to rely on this serves multiple purposes.

Fail early and fail fast. The testing environment will have the same schema as production. Any syntax error or reference to a non-existing object are captured during development rather than deployment. The chance of backfire when deploying is negligible.

Tracking down a production incident due to a database schema issue can also be reproduced in a non-production environment. Data protection officers and security teams will become very happy with this.


## 4. Speed of development

Although it's listed as the last one, it doesn't mean it's less important. The faster developers can make changes, test and promote them up until production environments, the more value can be delivered. By keeping database patches in version control and using tooling to apply those, development speed can be increased.

Developers can build and use local development databases by just running the tooling. In case of upstream changes the tooling only has to be ran again to ensure the database is fully up to date. Any new database patch is based of the latest version. An accidental `DROP TABLE` in a developer's local database will not impact the productivity of other developers.

As applying database patches has become an automated routine doing more and smaller changes can be done. This will also greatly reduce the impact of each single patch.

With a bit of creativity and effort it is even possible to validate a database patch on syntax before it is merged in the main code branch. As this is not trivial, I'll discuss this in a separate post.


## Conclusion

This post pointed out 4 major reasons to treat database changes as code and having a pipeline to apply the changes. It provides full full insight in all changes happening to your databases in all environments. Next to that all environments will be treated in exactly the same way. Database drift is a fact of the past, all database will be in a predictable state. Last but not least it greatly increases the speed of development.

Automation of database changes does not take away typical challenges like database locks during deployment, executing changes which are not compatible with applications using the database, removing data which shouldn't have been removed, etc. This still requires craftsmanship and proper technical and organizational safety nets. 

Followup posts will focus on more detailed topics. Please leave any question or feedback below in the comments!
