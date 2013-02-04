From [[Colby Dyess]] email to community-list, 1/31/2013:

What currently works:
    
* View running instances
* View, create, delete Volumes
* View, create and delete Snapshots
* View, create, modify and delete Security Groups

Remaining issues:

* Cannot reboot or stop instance
* Simply viewing the instance list gets you an NPE. I mean the list appears, but something broke here because you cannot perform any action on the instance. And selecting an instance from the list causes an "Unhandled event loop exception" in the ec2 UI code.
* Cannot view EMI list
* Eclipse constantly throws up  "Unable to query list of AMIs: The filter 'image-type' is invalid." and you can't get past it. No idea how to move forward.
* Cannot connect to Walrus
* Eclipse toolkit reports "Unable to connect". Doesn't look like a configuration issue, but I'll need to check the logs and report back.