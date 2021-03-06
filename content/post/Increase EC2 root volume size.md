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

* Screenshot of the instance which we are going to resize.

![Test Instance Initial](https://navymerianda.com/images/Test-Instance-Initial.png)

* Make sure to stop the instance before going further.

![Stop Instance](https://navymerianda.com/images/Stop-Instance.jpg)

* Detach the volume.

![Detach Vol](https://navymerianda.com/images/Detach-Vol.jpg)

* Modify the volume to your desired capacity.

![Modify Vol](https://navymerianda.com/images/Modify-Vol.jpg)

* Process of modifying the volume is time consuming depending on the size of your volume, please be patient through the process.

![Modifying Vol](https://navymerianda.com/images/Processing-Modify-Vol.jpg)

* Re-attach volume to the EC2 instance.

![Attach Vol](https://navymerianda.com/images/Attach-Vol.jpg)

* Screenshot of the volume post resize.

![Post Resize Vol](https://navymerianda.com/images/Post-Resizing.png)

* List of mount types of all our volumes.

![Mount Types List](https://navymerianda.com/images/Mount-Types-Post.jpg)

Approach 2: Using Snapshots
-----
This approach works great if you already have everything like flask libraries, nginx server and any other libraries or framework wherein we have setup the environment already. Else, we will have to spend a considerable amount of time doing the same. Or even if we want to spin up more EC2 instances with the same environment.

Make sure to stop the instances before going further.

We will need to create a new volume by providing a snapshot id of the volume you had previously taken the snapshot of.
Once the volume is created, you will be required to detach an older volume of a smaller size and attach the new volume
which was created with a snapshot. If all the steps were followed correctly, we should be able to see the extended size
of your root volume.

* Make sure to stop the instance before going further.

![Stop Instance](https://navymerianda.com/images/Stop-Instance.jpg)

* Detach the volume.

![Detach Vol](https://navymerianda.com/images/Detach-Vol.jpg)

* Create a snapshot of the volume you want to be resized.

![Create Snapshot](https://navymerianda.com/images/Create-Snapshot.jpg)

* Once the snapshot is created, we move on to creating new volume of the desired capacity from the snapshot created previously.

![Snapshot Copy](https://navymerianda.com/images/Snapshot-Copy.jpg)

![Snapshot Page](https://navymerianda.com/images/Create-Vol-Snapshot.jpg)

* Re-attach volume to the EC2 instance.

![Attach Vol](https://navymerianda.com/images/Attach-Vol.jpg)