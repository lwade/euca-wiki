**Author:** David Kavanagh, et al

**Website:** <a href="http://code.google.com/p/typica/">http://code.google.com/p/typica/</a>

**Inquiry Email:** <a href="mailto:http://code.google.com/p/typica/people/list">http://code.google.com/p/typica/people/list</a>

**Download URL:** <a href="http://code.google.com/p/typica/downloads/list">http://code.google.com/p/typica/downloads/list</a>

**License:** Apache License 2.0

***

<p>A simple API using the QUERY interfaces which supports a variety of the Amazon Web Services.</p>

<h2>Example Usage</h2>

<pre><code>Jec2 ec2 = new Jec2(props.getProperty("aws.accessId"), 
props.getProperty("aws.secretKey"), false, "your-host", 8773); 
ec2.setResourcePrefix("/services/Eucalyptus"); 
ec2.setSignatureVersion(1);
</code></pre>