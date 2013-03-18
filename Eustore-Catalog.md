## Eustore Image Catalog Format

The Eustore Image Catalog is currently implemented as a single file that presents the images that are available to Eucalyptus users via the eustore command.

The format for the image catalog is documented here. The catalog is in JSON format and contains a version number along with a list of images. Those images have metadata associated with them. Each image has a name which is a number, derived by taking the crc32 of the md5 hash of the tarball. This creates a reasonably unique 10 digit number. This number can be used to uniquely identify images and possibly to validate the downloaded tarball since it is derived from the file itself.

There are images here: http://emis.eucalyptus.com/

The catalog looks something like this:

<pre>
{
 "version": "eustore-catalog-2011-12-29",
 "images": [
 {
   "name": "4150406313",
   "description": "CentOS 5 1.3GB root, Hypervisor-Specific Kernels",
   "version": "2011.07.02",
   "architecture": "i386",
   "single-kernel": "False",
   "hypervisors-supported": ["xen", "kvm"],
   "os": "centos",
   "url": "starter-emis/euca-centos-2011.07.02-i386.tgz",
   "date": "20110716161844",
   "recipe": "centos-based",
   "stamp": "6e69-7565",
   "contact": "images@lists.eucalyptus.com"
 },
 {
   "name": "1450309069",
   "description": "Debian 6 1.3GB root, Single Kernel",
   "version": "2012.1.14",
   "architecture": "x86_64",
   "single-kernel": "True",
   "hypervisors-supported": ["xen", "kvm"],
   "os": "debian",
   "url": "starter-emis/euca-debian-2012.1.14-x86_64.tgz",
   "date": "20120114152138",
   "recipe": "debian-based",
   "stamp": "3752-f34a",
   "contact": "images@lists.eucalyptus.com"
 }
 ]
}
</pre>

Note, that url is relative to where this catalog was retrieved from, but can also be absolute.

Longer term, the catalog can be generated from a database on demand.

The name is generated using code such as the following reference;

<pre>
  file = open(filename, 'r')
  m = hashlib.md5()
  m.update(file.read())
  hash = m.hexdigest()
  crc = str(zlib.crc32(hash)& 0xffffffffL)
  print crc.rjust(10,"0")
</pre>

The some fields such as the "stamp" are define in the image_id file description: https://projects.eucalyptus.com/redmine/projects/images/wiki/

## Eustore Catalog Update script

For reference. This script should be checked in at some point.

<pre>
#!/usr/bin/env python
 
import os
import sys
import urllib2
import json
import hashlib
import zlib
from optparse import OptionParser
 
def main():
parser = OptionParser(usage='catalog')
options, args = parser.parse_args()
catfile = open(args[0], 'r')
parsed_cat = json.loads(catfile.read())
image_list = parsed_cat['images']
for image in image_list:
# print image['name']
filename = image['url']
filename = filename[filename.rfind("/")+1:]
try:
file = open(filename, 'r')
m = hashlib.md5()
m.update(file.read())
hash = m.hexdigest()
crc = str(zlib.crc32(hash)& 0xffffffffL)
# print crc.rjust(10,"0")
image['name'] = crc.rjust(10,"0")
except (IOError):
print "file not found: "+filename
 
print json.dumps(parsed_cat, indent=2)
 
if __name__ == "__main__":
main()
</pre>