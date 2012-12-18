We now have an ami2emi translation tool!  See:

https://github.com/eucalyptus/ami2emi

## The Big Idea

There are lots of AMIs on AWS.  Some of them can be readily translated and uploaded to a Eucalyptus cloud as EMIs.  The ideal candidates currently:

* Use pv-grub on the AWS side, and will therefore use [[kexec-loader]] on the Euca side;
* Are instance-store rather than EBS-backed;
* Are ubuntu images (since there are tons of them and that's what we're testing first.)

There are hundreds of possible images that are targets for translation, and we're running ami2emi over a whole ton of them.  AMIs that are successfully converted and boot in Euca are listed below.  (Note: we're only doing basic functional testing. If can ssh to the new image, that's all we can really test in an automatable fashion. There may well be configuration issues to be resolved after moving the AMI over, but this gets you a large part of the way there.)

## Successfully Tested AMIs To Date

**All of these images are in us-east-1.**

<pre>
ami-002bf169	bitnami-cloud/wordpress/bitnami-wordpress-3.3.2-0-multisite-linux-ubuntu-12.04-x86_64-s3.manifest.xml
ami-00d47069	bitnami-cloud/ezpublish/bitnami-ezpublish-2012.4-0-linux-ubuntu-12.04-x86_64-s3.manifest.xml
ami-01209768	bitnami-cloud/sugarcrm/bitnami-sugarcrm-6.5.4-0-linux-ubuntu-12.04.2-x86_64-s3.manifest.xml
ami-022a946b	bitnami-cloud/lampstack/bitnami-lampstack-5.3.17-1-linux-ubuntu-12.04.1-x86_64-s3.manifest.xml
ami-0275ce6b	bitnami-cloud/reviewboard/bitnami-reviewboard-1.6.13-0-linux-ubuntu-12.04.1-x86_64-s3.manifest.xml
ami-02bc046b	bitnami-cloud/piwik/bitnami-piwik-1.9.1-0-linux-ubuntu-12.04.1-x86_64-s3.manifest.xml
ami-03c1736a	bitnami-cloud/jenkins/bitnami-jenkins-1.483-0-linux-ubuntu-12.04.2-x86_64-s3.manifest.xml
ami-04d26d6d	bitnami-cloud/gitorious/bitnami-gitorious-2.3.1-0-linux-ubuntu-12.04.1-x86_64-s3.manifest.xml
ami-05d2016c	bitnami-cloud/rubystack/bitnami-rubystack-3.2.1-0-linux-x64-ubuntu-10.04.manifest.xml
ami-071ec96e	bitnami-cloud/coppermine/bitnami-coppermine-1.5.18-0-linux-x64-ubuntu-10.04.manifest.xml
ami-076ac56e	bitnami-cloud/magento/bitnami-magento-1.7.0.2-0-linux-ubuntu-12.04-x86_64-s3.manifest.xml
ami-0a37ed63	bitnami-cloud/moodle/bitnami-moodle-2.2.2-1-linux-ubuntu-12.04-x86_64-s3.manifest.xml
ami-0a892b63	bitnami-cloud/redmine/bitnami-redmine-2.0.3-0-linux-ubuntu-12.04-x86_64-s3.manifest.xml
ami-0a9f3a63	bitnami-cloud/drupal/bitnami-drupal-6.26-0-linux-ubuntu-12.04-x86_64-s3.manifest.xml
ami-0ad57363	bitnami-cloud/redmine/bitnami-redmine-2.0.0-0-linux-ubuntu-12.04-x86_64-s3.manifest.xml
ami-0cc16265	bitnami-cloud/limesurvey/bitnami-limesurvey-1.92plus20120608-0-linux-ubuntu-12.04-x86_64-s3.manifest.xml
ami-0e8a2f67	bitnami-cloud/drupal/bitnami-drupal-7.14-0-linux-ubuntu-12.04-x86_64-s3.manifest.xml
ami-0f3c8e66	bitnami-cloud/phpbb/bitnami-phpbb-3.0.11-1-linux-ubuntu-12.04.2-x86_64-s3.manifest.xml
ami-1005df79	bitnami-cloud/magento/bitnami-magento-1.6.2.0-0-linux-ubuntu-12.04-x86_64-s3.manifest.xml
ami-105dff79	bitnami-cloud/lampstack/bitnami-lampstack-5.3.14-0-linux-ubuntu-12.04-x86_64-s3.manifest.xml
</pre>