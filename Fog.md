**Author:** geemus (Wesley Beary)

**Website:** <a href="http://fog.io">http://fog.io</a>

**Download URL:** <a href="https://github.com/fog/fog">https://github.com/fog/fog</a>

**License:** MIT

***

## Information Regarding Installation

### Installation

Follow instructions found here:

http://fog.io/about/getting_started.html

API documentation found here:

http://rubydoc.info/github/fog/fog

* Dependency packages needed (install before fog)*

- irb (for testing Fog)
- rubygems
- libxml2 libxml2-dev
- libxslt1.1 libxslt1-dev

## Status of Testing

### EC2

So far, most testing has been done by hand to help troubleshoot Puppet Cloud Provisioner (http://docs.puppetlabs.com/guides/cloud_pack_getting_started.html).  Need to test out all EC2 related commands supported by Eucalyptus that are provided. Need to write a ruby script that tests everything. 

Setup is follows (using irb):

require 'irb/completion' *for tab completion - makes it easier*

require 'rubygems'

require 'fog'

connection = Fog::Compute.new({
:provider => "AWS",
:aws_access_key_id => "<EC2_ACCESS_KEY>",
:aws_secret_access_key => "<EC2_SECRET_KEY>",
:endpoint => "<EC2_URL>",
:region => "eucalyptus",
:version => "2010-08-31"})

**Note: its important that version is set to 2010-08-31.  Thats the latest supported EC2 API version in Eucalyptus ( this is true as of Eucalyptus 3.2 - thee version tested with Fog 1.9.0 )

**should get back a response similar to this**
=> #<Fog::Compute::AWS::Real:69849146345100 @port=8773 @hmac=#<Fog::HMAC:0x7f0e0791c848 @digest=#<OpenSSL::Digest::Digest: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855>, @signer=#<Proc:0x00007f0e0ae0d598@/var/lib/gems/1.8/gems/fog-1.9.0/lib/fog/core/hmac.rb:30>, @key="OpqZKSPzQmYb3mQEdUGK2rsWme95FBdFzabnzC8T"> @use_iam_profile=nil @aws_session_token=nil @instrumentor_name="fog.aws.compute" @connection_options={} @aws_secret_access_key="<EC2_SECRET_KEY>" @path="/services/Eucalyptus" @version="2010-08-31" @instrumentor=nil @aws_access_key_id="<EC2_ACCESS_KEY>" @connection=#<Fog::Connection:0x7f0e0791c3e8 @persistent=nil, @excon=#<Excon::Connection:7f0e0791c230 @connection={:port=>"8773", :ssl_verify_peer=>true, :host=>"eucalyptus.euca-hasp.eucalyptus-systems.com", :read_timeout=>60, :host_port=>"eucalyptus.euca-hasp.eucalyptus-systems.com:8773", :connect_timeout=>60, :write_timeout=>60, :scheme=>"http", :retry_limit=>4, :instrumentor_name=>"excon", :query=>nil, :mock=>false, :path=>"/services/Eucalyptus", :chunk_size=>1048576, :ssl_ca_file=>"/var/lib/gems/1.8/gems/excon-0.16.10/data/cacert.pem", :headers=>{"User-Agent"=>"fog/1.9.0"}, :nonblock=>false} @socket_key="eucalyptus.euca-hasp.eucalyptus-systems.com:8773" @proxy=nil>> @aws_credentials_expire_at=nil @scheme="http" @host="eucalyptus.euca-hasp.eucalyptus-systems.com" @endpoint="http://eucalyptus.euca-hasp.eucalyptus-systems.com:8773/services/Eucalyptus" @region="eucalyptus">

Tested the following commands:

- connection.images
- connection.describe_availability_zones
- connection.describe_key_pairs
- connection.describe_instances
- connection.describe_volumes

### S3/Walrus

Same status as EC2 testing.  Need to write ruby script to test it out. Setup is as follows:

storage = Fog::Storage.new({
:provider => "AWS",
:aws_access_key_id => "<EC2_ACCESS_KEY>",
:aws_secret_access_key => "<EC2_SECRET_KEY>",
:endpoint => "<S3_URL>"})

**Should see something similar to the following**
=> #<Fog::Storage::AWS::Real:69849115229360 @port=8773 @hmac=#<Fog::HMAC:0x7f0e074d7b70 @digest=#<OpenSSL::Digest::Digest: da39a3ee5e6b4b0d3255bfef95601890afd80709>, @signer=#<Proc:0x00007f0e0ae0e6a0@/var/lib/gems/1.8/gems/fog-1.9.0/lib/fog/core/hmac.rb:22>, @key="OpqZKSPzQmYb3mQEdUGK2rsWme95FBdFzabnzC8T"> @use_iam_profile=nil @aws_session_token=nil @connection_options={} @aws_secret_access_key="<EC2_SECRET_KEY>" @path="/services/Walrus" @aws_access_key_id="<EC2_ACCESS_KEY>" @connection=#<Fog::Connection:0x7f0e074d76e8 @persistent=nil, @excon=#<Excon::Connection:7f0e074d75f8 @connection={:port=>"8773", :ssl_verify_peer=>true, :host=>"walrus.euca-hasp.eucalyptus-systems.com", :read_timeout=>60, :host_port=>"walrus.euca-hasp.eucalyptus-systems.com:8773", :connect_timeout=>60, :write_timeout=>60, :scheme=>"http", :retry_limit=>4, :instrumentor_name=>"excon", :query=>nil, :mock=>false, :path=>"/services/Walrus", :chunk_size=>1048576, :ssl_ca_file=>"/var/lib/gems/1.8/gems/excon-0.16.10/data/cacert.pem", :headers=>{"User-Agent"=>"fog/1.9.0"}, :nonblock=>false} @socket_key="walrus.euca-hasp.eucalyptus-systems.com:8773" @proxy=nil>> @aws_credentials_expire_at=nil @scheme="http" @host="walrus.euca-hasp.eucalyptus-systems.com" @endpoint="http://walrus.euca-hasp.eucalyptus-systems.com:8773/services/Walrus">

Tested the following commands:

- storage.directories *lists buckets*

## Additional Information

Would be good to add mocks (http://rubydoc.info/github/fog/fog#Mocks) to Fog so that testing can be done without a Eucalyptus cloud.  Haven't scoped it out, but it would be helpful.