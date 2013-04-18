**Author:** Mitch Garnaat, et al

**Website:** <a href="https://github.com/boto/boto">https://github.com/boto/boto</a>

**Download URL:** <a href="http://pypi.python.org/pypi/boto">http://pypi.python.org/pypi/boto</a>

**License:** MIT

***

<p>boto is an integrated interface to current and future infrastructural services compatible with AWS.</p>

<h2>Example Usage</h2>

<h3>Setup boto</h3>

<p>Install boto, setup the environment, and so on.</p>

<pre><code>As root,

python setup.py install
</code></pre>

<h3>Using boto</h3>

<h4>Eucalyptus EC2 interface</h4>

<pre><code>region = RegionInfo(name="eucalyptus", endpoint="hostname")

connection = boto.connect_ec2(aws_access_key_id="access key",
                      aws_secret_access_key="secret",
                      is_secure=False,
                      region=region,
                      port=8773,
                      path="/services/Eucalyptus")

#Run commands

zones = connection.get_all_zones()</code></pre>

<p>where, "hostname" is the name of the Eucalyptus front end.</p>

<p>For instance, if EC2_URL is set to <a href="http://192.168.9.1:8773/services/Eucalyptus" title="http://192.168.9.1:8773/services/Eucalyptus">http://192.168.9.1:8773/services/Eucalyptus</a>,</p>

<p>hostname should be "192.168.9.1"</p>

<h4>Eucalyptus S3 interface</h4>

<pre><code>
calling_format=boto.s3.connection.OrdinaryCallingFormat()
connection = boto.s3.Connection(aws_access_key_id="access key",
                      aws_secret_access_key="secret",
                      is_secure=False,
                      host="hostname",
                      port=8773,
                      calling_format=calling_format,
                      path="/services/Walrus")

#Run commands

bucket_instance = connection.get_bucket(bucket)
keys = bucket.get_all_keys()
for k in keys:
    #do something
</code></pre>

*****

[[category.tools]]