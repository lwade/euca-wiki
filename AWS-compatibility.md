Eucalyptus strives for 100% API compatibility with all AWS services that we currently support.  Our goal is to be a "drop-in replacement AWS region", or as close to that as humanly possible.

To that end, we want to test against all AWS tools in the open source ecosystem, identify those places where we are compatible, and find and fix all places where we are not compatible.

Here's a list of the projects we're currently targeting (where "targeting" means "we have identified an individual who is responsible for assessing our compatibility):

**Status**. <font color="red">Red</font> means "known broken". <font color="yellow">Yellow</font> means "works to some degree with tweaks."  <font color="green">Green</font> means "works out of the box with simple configuration".

<table>
  <tr><td><b>Project</b></td><td><b>Project URL</b></td><td><b>Owner</b></td><td><b>Status</b></td><td><b>Notes</b></td></tr>
  <tr><td>awscli</td><td>http://aws.amazon.com/cli/</td><td>[[Andy Grimm]]</td><td><font color="yellow">Yellow</font></td><td>http://agrimmsreality.blogspot.com/2013/01/using-aws-cli-with-eucalyptus.html</td></tr>
  <tr><td>AWS toolkit for Eclipse</td><td>https://github.com/aws/aws-toolkit-eclipse</td><td>[[Colby Dyess]]</td><td><font color="yellow">Yellow</font></td><td>[[Eclipse Plugin]]</td></tr>  
  <tr><td>Fog</td><td>http://fog.io/1.8.0/index.html</td><td>[[Harold Spencer Jr.]]</td><td><font color="yellow">Yellow</font></td><td>Lots of notes here: [[Fog]]</td></tr>
  <tr><td>Jenkins</td><td>https://wiki.jenkins-ci.org/display/JENKINS/Amazon+EC2+Plugin</td><td>[[Lester Wade]]</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>AppScale</td><td>&nbsp;</td><td>[[Harold Spencer Jr.]]</td><td><font color="red">Red</font></td><td>Image runs, but the AppScale services do not yet work. Problem is that Euca does not currently follow the split internal/external DNS model of AWS. It may be that other PaaSes share this exact issue.</td></tr>
  <tr><td>Stackato</td><td>&nbsp;</td><td>[[Harold Spencer Jr.]]</td><td><font color="red">Red</font></td><td>Image runs, but the Stackato services do not yet work. More info: [[Stackato-Image]]</td></tr>
  <tr><td>Netflix Edda</td><td>&nbsp;</td><td>Dan Nurmi</td><td><font color="yellow">Yellow</font></td><td>http://nurmiblog.wordpress.com/2013/01/22/inspired-by-netflix/</td></tr>
  <tr><td>AWS .Net SDK</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>AWS SDK for Java</td><td>&nbsp;</td><td>Engineering</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>AWS SDK for Android</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>AWS toolkit for iOS</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>JetS3t</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>RightScale AWS Gems</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>Netflix Asgard</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>Netflix Chaos Monkey</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
  <tr><td>Netflix Janitor Monkey</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td><td>&nbsp;</td></tr>
</table>

When we find issues, we should file them against the [AWS Compatibility component](https://eucalyptus.atlassian.net/browse/EUCA/component/10201).

*****

[[category.aws-compatibility]]