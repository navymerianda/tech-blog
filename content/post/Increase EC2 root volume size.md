---
title: "Increase EC2 instance root volume size"
date: 2020-11-08
tags: ["EC2", "root", "volume"]
draft: false
---

Couple of ways for increasing root volume size in your EC2 instances.
------

Approach 1: Modify Existing Volume
-----
This is probably the easiest way to get the resized volume up and running. But, it's going to take up some time to resize the existing volume, higher the existing volume size, longer the time it's going to take to completely resize it to your desired capacity. Next, before we move on to modifying volume it's important to back up the existing volume. Take a snapshot of the existing volume, just to make sure we don't lose any data if something fails along the way.

Once the snapshot is ready, make sure to stop the running EC2 instance before the next step. Now, let's go ahead and extend the volume by using the modifying volume option.

Here are the steps:

![Test Instance Initial](https://navymerianda.com/images/Test-Instance-Initial.jpg)

![Stop Instance](https://navymerianda.com/images/Stop-Instance.jpg)

![Detach Vol](https://navymerianda.com/images/Detach-Vol.jpg)

![Modify Vol](https://navymerianda.com/images/Modify-Vol.jpg)

![Attach Vol](https://navymerianda.com/images/Attach-Vol.jpg)

![Post Resize Vol](https://navymerianda.com/images/Post-Resizing.jpg)

![Mount Types List](https://navymerianda.com/images/Mount-Types-Post.jpg)

Approach 2: Using Snapshots
-----
This approach works great if you already have everything like flask libraries, nginx server and any other libraries or framework wherein we have setup the environment already. Else, we will have to spend a considerable amount of time doing the same. Or even if we want to spin up more EC2 instances with the same environment.

Make sure to stop the instances before going further.

We will need to create a new volume by providing a snapshot id of the volume you had previously taken the snapshot of.
Once the volume is created, you will be required to detach an older volume of a smaller size and attach the new volume
which was created with a snapshot. If all the steps were followed correctly, we should be able to see the extended size
of your root volume.