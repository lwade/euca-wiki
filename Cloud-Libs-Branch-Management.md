Purpose
-------

While Ivy 3rd party library is still on the horizon, in the mean time we need to update certain Java libraries. To do this, changes to the CLC Makefile have been made so that it is easier to manage multiple branches of cloud-libs. This wiki page is intended to explain why this is necessary and how to properly manage branches of cloud-libs.

Note: throughout this document I'll use *cloud-libs* to refer to the Eucalyptus Cloud Libs [repository](https://github.com/eucalyptus/eucalyptus-cloud-libs).

Things you really need to know about what has changed
-----------------------------------------------------

1. The 3.3 development branches will now use the *testing* branch of cloud-libs. I have tried to avoid making any changes that would affect 3.2 development, and as such the 3.2 Eucalyptus branches will continue to use the *master* branch of cloud-libs.

2. The *testing* branch contains just a few new libraries, though we intend to update others as well.

3. Management of cloud-libs is now more robust. When building Eucalyptus, the Makefile will check which version of Eucalyptus you're building to determine which branch of cloud-libs to use. You may also specify your own cloud-libs branch if you'd like when you build.

4. Eucalyptus will now attempt to refresh the cloud-libs repository when switching branches. For example, if you are switching back and forth between maint/3.2/testing and testing, which use different cloud-libs branches; Eucalyptus will make sure you are using the correct cloud-libs *as long as you remember to run the clean target*.

Your workflow might look like this:

    $ git checkout maint/3.2/testing
    $ make
    <cloud-libs 'master' branch is checked out and your code is built>
    $ git checkout testing
    $ make clean
    $ make
    <make will see there is an existing 'lib' git repository and checkout the 'testing' branch>

Updated dependencies
--------------------

* *commons-codec* updated to version *1.4*
* *commons-io* updated to version *2.0.1*
* *commons-lang* updated to version *2.6*

Specifying a different cloud-libs branch
----------------------------------------

If you're building Eucalyptus 3.3 and would like to use a branch of cloud-libs other than *testing*, you may override this when building like this:
   
    $ make CLOUD_LIBS_BRANCH=mylibs

You might want to do that if, say, you'd like to test some experimental Java dependencies you've added to your branch.
