Eucalyptus strives for 100% API compatibility with all AWS services that we currently support.  Our goal is to be a "drop-in replacement AWS region", or as close to that as humanly possible.

To that end, we want to test against all AWS tools in the open source ecosystem, identify those places where we are compatible, and find and fix all places where we are not compatible.

## <font color="green">Green: Just Works</font>

Green means a service "just works". Pointing region and credential variables of the service to Eucalyptus results in a perfectly working service.

## <font background="black" color="yellow">Yellow: Works with Tweaks</font>

Yellow means a service works well enough to be useful, though code tweaks are necessary and fully equivalent functionality may not be available.

## <font color="red">Red: Does Not Work</font>


* _*AWS SDK for Java*_ 
** Owned by: Engineering **
** Tested by: ??? **
** Notes: known issues with Walrus should be fixed as of 3.3m4, needs retesting
** Last update: 11 Mar 2013

Red means that a service cannot be made to work without substantial modification, or at all.

Here's a list of the projects we're currently targeting (where "targeting" means "we have identified an individual who is responsible for assessing our compatibility):

*****

## Old Table

For reference until this is all changed.

**Status**. <font color="red">Red</font> means "known broken". <font color="yellow">Yellow</font> means "works well with tweaks."  <font color="green">Green</font> means "works out of the box with simple configuration".

<table>
  <tr><td><b>Project</b></td><td><b>Project URL</b></td><td><b>Owner</b></td><td><b>Status</b></td><td><b>Notes</b></td></tr>
  <tr><td>AWS SDK for Java</td><td>&nbsp;</td><td>Engineering</td><td><font color="yellow">Yellow</font></td><td>Known issues with S3; on track for resolution in 3.3 sprint 4 (updated: 11 Mar 2013)</td></tr>
  <tr><td>AWS SDK for PHP</td><td>&nbsp;</td><td>John Jiang</td><td><font color="yellow"></td><td>Works with tweaks. See wiki page for latest: [[AWS SDK for PHP]]</td>
  <tr><td>awscli</td><td>http://aws.amazon.com/cli/</td><td>[[Andy Grimm]]</td><td><font color="yellow">Yellow</font></td><td>http://agrimmsreality.blogspot.com/2013/01/using-aws-cli-with-eucalyptus.html</td></tr>
  <tr><td>AWS toolkit for Eclipse</td><td>https://github.com/aws/aws-toolkit-eclipse</td><td>[[Colby Dyess]]</td><td><font color="yellow">Yellow</font></td><td>Short takeaway: EC2 functionality mostly "just works", some known issues around S3 functionality. More details here: [[Eclipse Plugin]]</td></tr>  
  <tr><td>Fog</td><td>http://fog.io/1.8.0/index.html</td><td>[[Harold Spencer Jr.]]</td><td><font color="yellow">Yellow</font></td><td>Lots of notes here: [[Fog]]</td></tr>
<tr><td>Jenkins</td><td>https://wiki.jenkins-ci.org/display/JENKINS/Amazon+EC2+Plugin</td><td>[[Lester Wade]]</td><td><font color="yellow">Yellow</font></td><td>Version 1.17 of the EC2 plugin doesn't work. Jenkins tries to get endpoints via the register URL which fails unless endpoint is https://cloudIP:8443/register. Once past this point, connectivity test yields "login failure: all modules ignored". Version 1.14 works however.</td></tr>
  <tr><td>AppScale</td><td>&nbsp;</td><td>[[shaon]]</td><td><font color="yellow">Yellow</font></td><td>http://mdshaonimran.wordpress.com/2013/03/01/run-appscale-on-eucalyptus/</td></tr>
  <tr><td>Scalr</td><td>&nbsp;</td><td>[[Vic Iglesias]]</td><td><font color="yellow">Yellow</font></td><td>http://testingclouds.wordpress.com/2013/01/23/using-scalr-for-automation-of-your-eucalyptus-cloud/</td></tr>
  <tr><td>Stackato</td><td>&nbsp;</td><td>[[Harold Spencer Jr.]]</td><td><font color="yellow">Yellow</font></td><td>Image runs, but the Stackato services do not yet work. More info: [[Stackato-Image]]</td></tr>
  <tr><td>Netflix Edda</td><td>&nbsp;</td><td>Dan Nurmi</td><td><font color="yellow">Yellow</font></td><td><b>demoed at netflix</b> / http://nurmiblog.wordpress.com/2013/01/22/inspired-by-netflix/</td></tr>
  <tr><td>Netflix Asgard</td><td>&nbsp;</td><td>[[grze]]</td><td><font color="yellow">Yellow</font></td><td><b>demoed at netflix</b></td></tr>
  <tr><td>Netflix Chaos Monkey</td><td>&nbsp;</td><td>[[grze]]</td><td><font color="yellow">Yellow</font></td><td><b>demoed at netflix</b></td></tr>
  <tr><td>AWS .Net SDK</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>AWS SDK for Android</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>AWS toolkit for iOS</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>JetS3t</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>RightScale AWS Gems</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
</table>

When we find issues, we should file them against the [AWS Compatibility component](https://eucalyptus.atlassian.net/browse/EUCA/component/10201).

*****

[[category.aws-compatibility]]
