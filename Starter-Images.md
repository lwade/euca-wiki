## Listing of Starter EMIs

Below is a list of the starter images that will be made available on emis.eucalyptus.com and eustore. 

<table>
    <tr>
       <th>URL to Image</th>
       <th>Title</th>
       <th>Owner</th>
       <th>Latest test</th>
       <th>Notes</th>
    </tr>
    <tr>
       <td>https://s3-eu-west-1.amazonaws.com/centos-6.3-x64/centos6.tgz</td>
       <td>CentOS 6.3 x86_64</td>
       <td>Olivier Renault - olivier.renault@eucalyptus.com</td>
       <td><a href="https://github.com/eucalyptus/image-verification-results/blob/master/centos6.3-eutester-testresults.txt">Test Results</a></td>
       <td>SELinux / iptables disabled. Root disk of 4.5G. Root user enable. It works w/ kexec kernel ( default kernel is provided as part of tar ).</td>
    </tr>
    <tr>
       <td>http://cloud-images.ubuntu.com/releases/precise/release/ubuntu-12.04-server-cloudimg-amd64.tar.gz</td>
       <td>Ubuntu 12.04 LTS x86_64 - Official Ubuntu Image</td>
       <td>Tom Ellis - tom.ellis@eucalyptus.com</td>
       <td>None</td>
       <td>Latest official Ubuntu image from cloud-images.ubuntu.com (if we use this link, it'll always pull the latest version of the image as they get updated frequently)</td>
    </tr>
    <tr>
       <td>http://uec-images.ubuntu.com/releases/10.04/release/ubuntu-10.04-server-cloudimg-amd64.tar.gz</td>
       <td>Ubuntu 10.04 LTS x86_64 - Official Ubuntu Image</td>
       <td>Tom Ellis - tom.ellis@eucalyptus.com</td>
       <td>None</td>
       <td>Latest official Ubuntu image from cloud-images.ubuntu.com (if we use this link, it'll always pull the latest version of the image as they get updated frequently)</td>
    </tr>
    </tr>
    <tr>
       <td>https://s3-eu-west-1.amazonaws.com/fedora-emi/f16-x86_64.tgz</td>
       <td>Fedora 16 x86_64</td>
       <td>Olivier Renault - olivier.renault@eucalyptus.com</td>
       <td><a href="https://github.com/eucalyptus/image-verification-results/blob/master/fedora16-eutester-testresults.txt">Test results</a></td>
       <td>SELinux / iptables disabled. Root disk of 4.5G. Root user enabled. It works w/ kexec kernel ( default kernel is provided as part of tar )</td>
    </tr>
    <tr>
       <td>https://s3-eu-west-1.amazonaws.com/fedora-emi/f17-x86_64.tgz</td>
       <td>Fedora 17 x86_64</td>
       <td>Olivier Renault - olivier.renault@eucalyptus.com</td>
       <td>None</td>
       <td>SELinux / iptables disabled. Root disk of 4.5G. Root user enabled. It works w/ kexec kernel ( default kernel is provided as part of tar )</td>
    </tr>
    <tr>
       <td>https://s3-eu-west-1.amazonaws.com/opensuse-images/opensuse-12.2-x86_64-emi.tar.gz</td>
       <td>OpenSUSE 12.2 x86_64</td>
       <td>Lester Wade - lester@eucalyptus.com</td>
       <td><a href="https://github.com/eucalyptus/image-verification-results/blob/master/opensuse12.2-eutester-testresults.txt">Test Results</a></td>
       <td>SUSE Firewall off.  Root disk of 2.5G.  Root user enabled.  Working with kexec kernel and ramdisk. OpenSUSE minimal base package set.</td>
    </tr>
    <tr>
       <td></td>
       <td></td>
       <td></td>
       <td></td>
       <td></td>
    </tr>
    <tr>
       <td></td>
       <td></td>
       <td></td>
       <td></td>
       <td></td>
    </tr>
</table>

*****

[[category.images]]