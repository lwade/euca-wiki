_(original author: Vivek Khurana / no_mind @ irc.freenode.net)_

# Introduction

The purpose of Eucalyptus-hadoop integration is to provide with a set of tools which will allow users to run hadoop based jobs inside eucalyptus based cloud without the need of deploying or configuring "hadoop":http://hadoop.apache.org/. This could be achieved by building APIs, a command line based tool and a web based tool that will allow the users to interact with eucalyptus-hadoop based cloud.

# Scope of this document

This document covers the following
* Use cases for euca-hadoop cloud
* Specs for hadoop image for euca.
* Method for creating/launching euca-hadoop cloud.
* API specifications for euca-hadoop cloud.
* Specifications for CLI based tools for running and managing jobs.
* Specifications for web based tools for running and managing jobs.

# Overall Description

Eucalyptus-Hadoop integration is aims to provide an easy to use hadoop based cloud setup which will allow users to run hadoop based jobs without the need to setup and configure hadoop. The overall system layout:

<pre>
Clients:
##########################################################
#   Euca-Hadoop Web UI     #     Euca-Hadoop CLI Tool    #
##########################################################

Eucalyptus:
##########################################################
#                  Euca-Hadoop API                       #
##########################################################
#                  Hadoop Instance                       #
##########################################################

</pre>

!euca-hadoop-integration-overall.png!

This project would  have following components

* Customized "EMI":http://open.eucalyptus.com/wiki/EucalyptusImageManagement_v2.0 for running hadoop. This will be the default EMI that will be fired when euca-hadoop cloud is started.
* Euca-hadoop API mapping subsystem: This subsystem will integrate provide a layer inside eucalyptus instance. This layer will map user issued commands to hadoop commands.
* Euca-hadoop command line tool: This component will run on client/user machine and will allow a user to create and manage hadoop jobs.
* Euca-hadoop web based management console: This component will run inside eucalyptus but will proved a web based console for creating and managing hadoop jobs.


# Use Cases

These use cases are taken form hadoop tutorials and our aim will be to implement them using euca-hadoop cloud tools

## UC-1: Create a euca-hadoop cloud

* User login to eucalyptus based system.
* User launches a euca-hadoop EMI.
* Modify Euca-hadoop cli tool and enter instance details.
* Create hadoop jobs using cli tool
* Access euca-hadoop web app.

## UC-2: Create hadoop job on euca-hadoop cloud (streaming)

Hadoop allows job creation using streaming API or using Pig/Hive components. When using streaming API the job is created by specifying job steps. Each job step has a mapper (script or function used to execute map function), reducer (script or function used to execute map function), input file to be processed and path to output file. To create a streaming job a user will take following steps.
* Upload mapper code file (if any) to Walrus.
* Upload reducer code file (if any) to Walrus.
* Upload the file to be processed to Walrus.
* Input Walrus path for output file.
* Create a job in hadoop cloud using cli tool by specifying, mapper, reducer, input, path to output bucket in Walrus, number of instances and instance types.
* Check the job status to confirm that the job is running.

## UCW-2: Create hadoop job on euca-hadoop cloud from web app (streaming)

* Create a new hadoop job from the web interface.
* Input the path for mapper code file from Walrus or mapper function. Alternatively upload file from the web interface.
* Input the path for reducer code file from Walrus or mapper function. Alternatively upload file from the web interface.
* Input path to file to be processed or upload file.
* Click on create job.
* Check the response for job created.

## UC-3: Create multi-step job on euca-hadoop cloud (streaming)

Multi-step job will require user to create a json file and specify the json file while creating multi-step hadoop job.
* Create a json file describing all the steps.
* Create a new job by specifying the json file. (no need to separately mentioning mapper, reducer, input and output)

## UCW-3: Create multi-step job on euca-cloud from web interface.

* Create a multi-step job in euca-cloud.
* Add step by inputing mapper, reducer, file to process and output path.
* Add as many steps as required.
* Once all steps are added, create the job by clicking create job button.
* Check response for job created.

## UC-4: List all jobs and job status

* Run the euca-hadoop cli tool with --list option.
* A list of all alive jobs along with job status and job id is shown.

## UCW-4: List all jobs and job status in web interface.

* Login to web interface.
* Select list all jobs.
* View the list of jobs

## UC-5: Describe a job status

Hadoop allows a user to inquire running job status. To find status of a job a user can do following.
* Query euca-hadoop cloud by using --describe and passing jobflowid.
* A detailed list is returned as JSON object.

## UCW-5: Describe a job status in web interface.

* Login to web interface
* List all jobs.
* Click on a job to view its details.
* Detailed description of job status is shown below the job.

## UC-6: Add steps to running process.

* Create a job in euca-hadoop cloud
* Add step to a running, non-terminated job by specifying jobflowid, mapper, reducer, input options.

## UCW-6: Add steps to running job with web interface.

* Login to web interface.
* List all jobs
* select the job you need to modify. A context menu will show over the job.
* Select add step from context menu.
* Input mapper, reducer and other arguments to addstep
* Select "Add more steps" to add another step or click on "modify job" to modify the process.

## UC-7: Terminate a running job

* Terminate a job by passing --terminate option and jobflowid to the euca-hadoop cli tool

## UCW7: Terminate a running job from web interface.

* Login to web interface.
* List all jobs
* select the job you need to modify. A context menu will show over the job.
* Select add terminate from context menu.
* Confirm job termination when prompted.
* Check the response to confirm job termination.

# Non-functional requirements

* Eucalyptus based cloud deployment should be available for this project to run.
* Web interface should run on all browsers.

# References

* Amazon AWS Elastic Reduce http://aws.amazon.com/elasticmapreduce/
<document in progress>