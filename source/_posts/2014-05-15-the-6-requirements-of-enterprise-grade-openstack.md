---
id: 1519
title: The 6 Requirements of Enterprise-grade OpenStack
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1519
permalink: /archives/1519
dsq_thread_id:
  - 2687549539
categories:
  - Uncategorized
tags:
  - 잡담
---
> Many attempts to solve the private cloud management challenges stop at installation. Installation is just the beginning of your journey, and how easy it is doesn’t matter if your cloud is then hard to manage on a daily basis. As we all know, running a production system is not easy. In fact, private clouds are significantly more complex than traditional infrastructure approaches in many aspects. To simplify the issue, at scale, the cloud pioneers, such as Google, Amazon, and Facebook have all adopted a pod, cluster, or block based approach to designing, deploying, and managing their cloud. Google has clusters; Facebook has triplets; but it’s all ultimately the same: a Lego brick-like repeatable approach to building your cloud and datacenter.[4] Enterprise-grade OpenStack-powered cloud operating systems will need to provide a similar approach to cloud organization.

<http://cloudscaling.com/blog/openstack/the-6-requirements-of-enterprise-grade-openstack-part-1/>

## OpenStack Default Networking is Bust

> A very brief explanation of the default networking models shortcomings is in order. I will keep it very simple, but am happy to follow up later with more details. The flat and multi_host networking model requires a single shared VLAN for all elastic (floating) IP addresses. This requires running spanning tree protocol (STP) across your switch fabric, a notoriously dangerous approach if you want high network uptime. I’ve asked the question at multiple conferences to a full room: “*How many of you think STP is evil?*” and have had nearly everyone in the room raise their hand.
> 
> Perhaps more importantly, both flat and multi\_host networking models require you to route your public IP addresses from your network edge down to the hypervisor nodes. This is really not an acceptable approach in any modern enterprise. Here’s a diagram showing multi\_host mode:

<img class="alignnone" src="http://img.readitlater.com/i/www.cloudscaling.com/wp-content/uploads/2014/05/6-Requirements-for-Enterprise-grade-OpenStack-Supporting-Material.0081/RS/w704.jpg" alt="" width="704" height="528" />

> By contrast, single\_host mode suffers from the singular sin of trying to make a single x86 server the core networking hub through which all traffic between VLANs and the Internet runs.[2] Basically, take your high performance switch fabric and throw it in the trash because your maximum bandwidth is whatever a Linux server can push. Again, this is not an acceptable or even credible approach to networking. To be fair though, OpenStack competitors also took a similar approach to this. Here’s a diagram on single\_host mode:

<img class="alignnone" src="http://img.readitlater.com/i/www.cloudscaling.com/wp-content/uploads/2014/05/6-Requirements-for-Enterprise-grade-OpenStack-Supporting-Material.007/RS/w704.jpg" alt="" width="704" height="528" />

<http://www.cloudscaling.com/blog/openstack/the-6-requirements-of-enterprise-grade-openstack-part-3/>