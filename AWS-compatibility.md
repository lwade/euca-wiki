Eucalyptus strives for 100% API compatibility with all AWS services that we currently support.  Our goal is to be a "drop-in replacement AWS region", or as close to that as humanly possible.

To that end, we want to test against all AWS tools in the open source ecosystem, identify those places where we are compatible, and find and fix all places where we are not compatible.

Here's a list of the projects we're currently targeting (where "targeting" means "an individual who is responsible for assessing our compatibility):

<table>
  <tr><td><b>Project</b></td><td><b>Project URL</b></td><td><b>Owner</b></td><td><b>Notes</b></td></tr>
  <tr><td>AWS CLI (beta)</td><td>http://aws.amazon.com/cli/</td><td>[[Greg DeKoenigsberg]]</td><td>&nbsp;</td></tr>
  <tr><td>AWS .Net SDK</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td></tr>
  <tr><td>AWS SDK for Java</td><td>&nbsp;</td><td>Engineering</td><td>&nbsp;</td></tr>
  <tr><td>AWS SDK for Android</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td></tr>
  <tr><td>AWS toolkit for Eclipse</td><td>https://github.com/aws/aws-toolkit-eclipse</td><td>[[Colby Dyess]]</td><td>&nbsp;</td></tr>
  <tr><td>AWS toolkit for iOS</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td></tr>
  <tr><td>JetS3t</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td></tr>
  <tr><td>RightScale AWS Gems</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td></tr>
  <tr><td>Fog</td><td>http://fog.io/1.8.0/index.html</td><td>[[Harold Spencer Jr.]]</td><td>[[Fog with Eucalyptus]]</td></tr>
  <tr><td>Jenkins</td><td>https://wiki.jenkins-ci.org/display/JENKINS/Amazon+EC2+Plugin</td><td>[[Lester Wade]]</td><td>&nbsp;</td></tr>
  <tr><td>Asgard</td><td>&nbsp;</td><td>open</td><td>&nbsp;</td></tr>
</table>

When we find issues, we should file them against the [AWS Compatibility component](https://eucalyptus.atlassian.net/browse/EUCA/component/10201).

*****

[[category.aws-compatibility]]