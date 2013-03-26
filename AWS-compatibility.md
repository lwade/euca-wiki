Eucalyptus strives for 100% API compatibility with all AWS services that we currently support.  Our goal is to be a "drop-in replacement AWS region", or as close to that as humanly possible.

To that end, we want to test against all AWS tools in the open source ecosystem, identify those places where we are compatible, and find and fix all places where we are not compatible. All issues should be filed against the [AWS Compatibility component](https://eucalyptus.atlassian.net/browse/EUCA/component/10201).


## <font color="green">Green: Just Works</font>

Green means a service "just works". Pointing region and credential variables of the service to a properly-configured Eucalyptus results in a perfectly working service.

## <font background="black" color="yellow">Yellow: Works with Tweaks</font>

Yellow means a service works well enough to be useful, though code tweaks are necessary and fully equivalent functionality may not be available.

* _**Fog**_
 * Project URL: http://fog.io
 * Driver: [[Harold Spencer Jr.]]
 * Known bugs: FIXME
 * Notes: [[Fog]]. **Notable: Fog with Walrus requires Euca DNS to be set up?**
 * Last update: 16 Mar 2013

* _**AWS Toolkit for Eclipse**_
 * Project URL: https://github.com/aws/aws-toolkit-eclipse
 * Driver: [[Colby Dyess]]
 * Known bugs: FIXME
 * Notes: EC2 functionality mostly "just works", some known issues around S3 functionality. More details here: [[Eclipse Plugin]]
 * Last update: 12 Mar 2013

* _**AWS SDK for Java**_ 
 * Project URL: FIXME
 * Driver: FIXME
 * Known bugs: FIXME
 * Notes: known issues with Walrus should be fixed as of 3.3m4, needs retesting
 * Last update: 11 Mar 2013

* _**NetflixOSS Edda**_
 * Project URL: FIXME
 * Driver: Dan Nurmi
 * Known bugs: FIXME
 * Notes: Demoed at Netflix, 13 Mar 2013. Blog post describing setup: http://nurmiblog.wordpress.com/2013/01/22/inspired-by-netflix/
 * Last update: 13 Mar 2013

* _**Netflix Asgard**_
 * Project URL: FIXME
 * Driver: [[grze]]
 * Known bugs: FIXME
 * Notes: demoed at Netflix, 13 Mar 2013.
 * Last update: 13 Mar 2013

* _**Netflix Chaos Monkey**_
 * Project URL: FIXME
 * Driver: [[grze]]
 * Known bugs: FIXME
 * Notes: demoed at Netflix, 13 Mar 2013.
 * Last update: 13 Mar 2013

* _**AWS SDK for PHP**_
 * Project URL: FIXME
 * Driver: John Jiang
 * Known bugs: FIXME
 * Notes: [[AWS SDK for PHP]]
 * Last update: 11 Mar 2013

* _**AWS CLI**_
 * Project URL: http://aws.amazon.com/cli/
 * Driver: Andy Grimm
 * Known bugs: FIXME
 * Notes: http://agrimmsreality.blogspot.com/2013/01/using-aws-cli-with-eucalyptus.html
 * Last update: 3 Jan 2013

* _**Jenkins for EC2**_
 * Project URL: https://wiki.jenkins-ci.org/display/JENKINS/Amazon+EC2+Plugin
 * Driver: [[Lester Wade]]
 * Known bugs: FIXME
 * Notes: Version 1.17 of the EC2 plugin does not work. Jenkins tries to get endpoints via the register URL which fails unless endpoint is https://cloudIP:8443/register. Once past this point, connectivity test yields "login failure: all modules ignored". Version 1.14 works however.
 * Last update: FIXME

Red means that a service cannot be made to work without substantial modification, or at all.

Here's a list of the projects we're currently targeting (where "targeting" means "we have identified an individual who is responsible for assessing our compatibility):

*****

## Other Projects

For reference and later testing.

<table>
  <tr><td>AppScale</td><td>&nbsp;</td><td>[[shaon]]</td><td><font color="yellow">Yellow</font></td><td>http://mdshaonimran.wordpress.com/2013/03/01/run-appscale-on-eucalyptus/</td></tr>
  <tr><td>Scalr</td><td>&nbsp;</td><td>[[Vic Iglesias]]</td><td><font color="yellow">Yellow</font></td><td>http://testingclouds.wordpress.com/2013/01/23/using-scalr-for-automation-of-your-eucalyptus-cloud/</td></tr>
  <tr><td>Stackato</td><td>&nbsp;</td><td>[[Harold Spencer Jr.]]</td><td><font color="yellow">Yellow</font></td><td>Image runs, but the Stackato services do not yet work. More info: [[Stackato-Image]]</td></tr>
  <tr><td>AWS .Net SDK</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>AWS SDK for Android</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>AWS toolkit for iOS</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>JetS3t</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>RightScale AWS Gems</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
</table>

*****

[[category.aws-compatibility]]
