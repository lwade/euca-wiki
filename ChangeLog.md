<h1>Changelog</h1>

</div>

<h3>Version 1.6.2 (2010-02-12)</h3>

<ul>

<li> Stability/Performance point-release.</li>

<li> Back-end (CC/NC) scale improvements.</li>

<li> CLC fixes including backend database pooling and persistence

improvements, SSL support.</li>

<li> euca_conf fixes including the ability to list nodes (and VMs).</li>

<li> Fixes to Walrus for specific use cases, bugs and stability, as

well as beta support for the S3 server logging API, fixes to

bittorrent support.</li>

<li> Storage Controller scalability improvements.</li>

<li> Updated packages for current versions of supported distributions.</li>

<li> Closes many bugs, including:

<code><pre>#477776 #461156 #477776 #498379 #498379 #517769 #518601 #513842 #453416 #328146</pre></code></li>

</ul>



<h3 id="Version1.62009-11-05">Version 1.6.1 (2009-11-05)<a title="Link to this section" class="anchor" href="Version1.62009-11-05"> </a></h3>

<ul>
<li>Added support for multiple clusters (availability zones)</li><li>Allow components (cloud, walrus, sc, cc, nc) to be hosted on different machines</li><li>Support for dynamic instance and bucket DNS</li><li>Better startup (no need for ant anymore, much faster)</li><li>Reworked part of the network provisioning code for multi-cluster and better handling public IP addressing</li><li>Hypervisor improvements<ul><li>switched default kvm ethernet interface to e1000</li><li>serialized calls to the xen hypervisor</li></ul></li><li>Added helpers to integrate with nagios and/or ganglia</li><li>The Web UI is now theme-able (at compile time)</li><li>A lot of work toward long term stability: run the code thru different code/memory checker, re-engineer the java stack to be faster and more robust.</li><li>Closes bugs:

<code><pre>#426469 #426576 #428546 #428654 #428710 #430260 #430356 #430888 #430922 #431939 #430830 #431933 #431935 #426469 #431934 #431937 #431931 #426467 #430830 #426389 #426389 #430944 #432604 #432823 #436274 #436276 #436290 #436313 #436407 #436885 #436917 #437014 #437026 #436407 #430957 #430226 #388934 #440744 #440817 #440950 #441033 #441035 #441020 #441019 #443325 #443325 #443314 #444838 #445206 #444908 #445206 #444908 #445997 #447585 #447555 #444838 #449135 #438565 #449948 #430266 #398867 #449948 #401996 #449944 #450915 #451795 #454405 #456877 #456878 #461394 #461444 #460085 #404842 #462940 #456878 #406049</pre></code></li></ul>



<h3 id="Version1.5.22009-07-17">Version 1.5.2 (2009-07-17)<a title="Link to this section" class="anchor" href="Version1.5.22009-07-17"> </a></h3>

<ul><li>A lot of bug fixes and improvements</li><li>Eucalytpus now runs as user 'eucalyptus' by default</li><li>added new UI tab 'Extras' that includes links to pre-packaged images and client tools</li><li>Fixed support for client tools (including fixes to the REST interface).</li>
<li>Closes bugs:
<code><pre>#368975 #375809 #375805 #376575 #354787 #382522 #357350 #375105 #359855 #384069 #359855 #357499 #384117 #384119 #375093 #384119 #356580 #384123 #359855 #356389 #384069 #384119 #357849 #359855 #384124 #384126 #384126 #384652 #385660 #386430 #357440</pre></code></li></ul>

<h3 id="Version1.5.12009-05-08">Version 1.5.1 (2009-05-08)<a title="Link to this section" class="anchor" href="Version1.5.12009-05-08"> </a></h3>

<ul><li>Elastic Block Store (EBS) support (volumes &amp; snapshots)</li><li>Walrus improvements:<ul><li>Support for groups in ACLS

</li><li>Fixed issues with meta data support</li><li>Web browser form-based uploads via HTTP POST</li><li>Object copying</li><li>Query string authentication</li><li>Support for arbitrary key names</li><li>Compressed image downloads and fixes to image caching

</li><li>Reduced memory requirement</li></ul></li><li>Network improvement: new <tt>MANAGED-NOVLAN</tt> mode



</li><li>Node-side improvements:<ul><li>Support for the KVM hypervisor </li><li>Compression &amp; retries on Walrus downloads

</li><li>Reworked caching (now with configurable limit)</li></ul></li><li>Web UI improvements:<ul><li>Cloud registration with Rightscale (from admin's 'Credentials' tab)</li><li>New configuration options for Walrus</li><li>Better screening of usernames

</li><li>Fixed account confirmation glitches</li></ul></li><li>Building and installation improvements<ul><li>Better Java installation checking</li><li>New command-line administration: <tt>euca_conf -addcluster ... -addnode ...</tt></li><li>Non-root user deployment of Eucalyptus



</li><li>Binary packages for more distributions (Ubuntu et al)</li></ul></li></ul><h3 id="Version1.42009-01-05">Version 1.4 (2009-01-05)<a title="Link to this section" class="anchor" href="Version1.42009-01-05"> </a></h3>

<ul><li>Added new networking subsystem that no longer depends on VDE</li><li>Added support for elastic IP assignment and security using the 'MANAGED' networking mode</li><li>Cluster controller scheduling policy can now be configured in eucalyptus.conf (ROUNDROBIN and GREEDY currently supported)</li><li>Cluster controller now handles concurrent requests (no longer have to restrict apache to allow only one connection at a time)</li><li>Added Walrus: an Amazon S3 interface compatible storage manager. Walrus handles storage of user data as well as filesystem images, kernels, and ramdisks. </li><li>Node Controller improvements:

<ul><li>Retrieval of images from Walrus instead of NFS-mounted file system</li><li>New caching and cleanup code, including start-time integrity checks</li><li>On-the-fly script-based generation of libvirt XML configuration</li><li>Script-based discovery of node resources (no assumptions about stat)</li><li>MAX_CORES overrides actual number of cores both down and up</li><li>Moved libvirt errors to nc.log and suppressed harmless ones</li><li>Serialized some Xen invocations to guard against non-determinism</li><li>Added proper swap creation, also "ephemeral" disk support</li><li>More robust instance state reporting to Cluster Controller

</li></ul></li><li>Web interface improvements:<ul><li>Added cloud/Walrus configuration, including clusters and VM types</li><li>Revamped 'credentials' tab with new options to edit user information and hide/show "secret" strings</li><li>Editing of user information for the administrator, including confirmation dialog for deletion of users</li><li>User-initiated password recovery mechanism</li><li>Fixed a couple of bugs: ' ' in username, spurious double additions</li></ul></li><li>Cloud Controller:

<ul><li>User, Cluster, and System keys are now stored in PKCS12 keystores and have moved to var/eucalyptus/keys</li><li>Configuration is handled entirely through the Web interface</li><li>Clusters dynamically added/started/stopped</li><li>Webservices operations complete up to EC2 2008-05-05 (w/o EBS):</li><li>"Elastic IP" address support</li><li>Image registration and attribute manipulation</li><li>GetConsole and RebootInstances support</li><li>Working Security Groups support for clusters in MANAGED network mode</li><li>Instance Metadata service (including userData)</li><li>Workaround to use Amazon's tools for registering kernels &amp; ramdisks

</li></ul></li><li>Revamped logging throughout, with five levels a la log4j</li><li>More standard build procedure: configure; make; make install</li><li>More robust start-time checking</li></ul><h3 id="Version1.32008-08-27">Version 1.3 (2008-08-27)<a title="Link to this section" class="anchor" href="Version1.32008-08-27"> </a></h3><ul><li>Added support for the new ec2 tools (1.3-24159 and newer)</li></ul><h3 id="Version1.22008-07-29">Version 1.2 (2008-07-29)<a title="Link to this section" class="anchor" href="Version1.22008-07-29"> </a></h3>



<ul><li>Added stand-alone RPM packages for non-rocks installation</li><li>Added image caching to reduce instance creation time</li><li>Added instance networking configuration options to eucalyptus.conf</li><li>Bug Fixes<ul><li>Improved installation-time error checking</li><li>Removed possibility of instance ID collision</li><li>Improved VDE runtime management</li><li>Improved VDE cleanup

</li><li>Resolved occasional NC instance loss problem</li><li>Resolved EC2 client timing issue that resulted in parsing errors on client</li></ul></li></ul><h3 id="Version1.12008-07-01">Version 1.1 (2008-07-01)<a title="Link to this section" class="anchor" href="Version1.12008-07-01"> </a></h3><ul><li>Added WS-security for internal communication</li><li>Added URL Query Interface for interacting with Eucalyptus</li><li>Cluster Controller improvements:



<ul><li>Instance caching added to improve performance under certain conditions</li><li>Thread locks removed to improve performance

</li><li>NC resource information gathered asynchronously to improve scheduler performance</li></ul></li><li>Network control improvements:<ul><li>Added ability to configure 'public' instance interface network parameters (instead of hardcoded 10. network)

</li><li>Lots of reliability changes</li></ul></li><li>Cloud Controller improvements:<ul><li>Pure in-memory database</li><li>Image registration over WS interface</li><li>Improved build procedure</li></ul></li><li>Web interface improvements: <ul><li>For all users (query interface credentials, listing of available images) </li><li>For the administrator (addition, approval, disabling, and deletion of users; disabling of images)</li></ul></li><li>Numerous bug fixes, improving stability and performance. In particular, but not limited to:<ul><li>Recovering Cloud Controller system state</li><li>Timeout-related error reporting</li><li>Slimmer log files, with timestamps



</li></ul></li></ul><h3 id="Version1.02008-05-29">Version 1.0 (2008-05-29)<a title="Link to this section" class="anchor" href="Version1.02008-05-29"> </a></h3>

<ul><li>First public version (limited-feature binary-only beta)</li> <div id="book-navigation-1188" class="book-navigation">

*****

[[category.legacy]]
