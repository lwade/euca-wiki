Eucalyptus strives for 100% API compatibility with all AWS services that we currently support.  Our goal is to be a "drop-in replacement AWS region", or as close to that as humanly possible.

To that end, we want to test against all AWS tools in the open source ecosystem, identify those places where we are compatible, and find and fix all places where we are not compatible.

Here's a list of the projects we're currently targeting (where "targeting" means "an individual who is responsible for assessing our compatibility):

* AWS .Net SDK
* AWS SDK for Java
* AWS SDK for Android
* AWS toolkit for Eclipse
* AWS toolkit for iOS
* JetS3t
* RightScale AWS Gems (https://github.com/rightscale/right_aws)
* Fog: https://github.com/fog/fog
* Jenkins
* Asgard

When we find issues, we should file them against the [AWS Compatibility component](https://eucalyptus.atlassian.net/browse/EUCA/component/10201).