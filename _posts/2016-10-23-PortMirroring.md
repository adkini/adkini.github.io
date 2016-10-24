---
layout: post
title: "Port Mirroring in Ovs - Tap as a Service"
date: 2016-10-23 
---


This article will explain port mirroring in Openstack provisioned networks using Ovs ( Open vSwitch ). There are many commands for doing the same. However recently I came across a much simpler way to configure port mirroring on Ovs, especially on Openstack Networks.    

#### What are ports?  
Well, ports can be of two types( software ports on which process binds and hardware ports that can be found on switches ) and often we confuse between them. Here we are talking of hardware ports in switches that certainly can be emulated in software ( ovs :)). Hosts are connected on each of these ports on a switch. Switch is a Layer 2 network device that basically understand MAC addresses and is generally used between a router and hosts to distribute internal traffic much more efficiently.    

#### What is Open vSwitch?  

Lets come to the main point. Open vswitch is a virtual switch widely used in many virtual deployments. It is widely used in neutron ( Network component ) of Openstack. It basically does all the work of a hardware switch , but in a virtual environment. Hence it also has virtual ports that hosts connect to.    

#### Port Mirroring.  

The main purpose of this article is to explain port mirroring. Why do we need it ? Well to monitor network traffic can be one use case. Lets say we need to see all traffic passing through the switch. We basically mirror all the traffic from different ports onto a single port . We analyse all the traffic on single port. 
How do we do it? . There are lot of ovs commands that can help you to achieve it. But recently i came across a much simpler way to do so.  

#### Tap as a service.  

This is a relatively new project to introduce port mirroring on openstack provisioned networks. This allows to mirror traffic from one port to another port and monitor traffic on a single port. Basically there are 2 things : tap service and tap flow. tap service instance has a neutron port serving as the destination side of the mirrored traffic. Tap flow represents association between a source port and tap service instance.  

####How to use it?  
Launch instances on openstack. Once instances are launched, we need to figure out the ports to which these instances are attached. We can find them in network section of openstack dashboard. Once we get the port numbers, we run the following commands.  

		neutron tap-service-create --name TAPSERVICENAME --port DESTINATIONPORTNUMBER    

		neutron tap-flow-create --name TAPFLOWNAME --port SOURCEPORTNUMBER --tap-service TAPSERVICENAME --direction BOTH  

TAPSERVICENAME - put in the name of tap service of your wish.    

DESTINATIONPORTNUMBER - put in the port id of the switch to which the instance is attached. Can be found in network-> instancenetwork -> ports.We can see the mirrored traffic here.    

TAPFLOWNAME - put in the name of tap flow of your wish.    

SOURCEPORTNUMBER - put in the port if of the switch to which the instance is attached. This should be the port whose traffic has to be mirrored.    

  
We can attach multiple tap flows. All traffic on the ports of tap flow will be mirrored on to the tap service port. We can attach a VM on this tap-service port and consume the traffic. We can do all type of traffic analysis on this.  

  
References:  
1. [Taas](https://www.openstack.org/videos/video/tap-as-a-service-what-you-need-to-know-now)  
2. [Taas-github](https://github.com/openstack/tap-as-a-service)  
3. [Openstack](https://www.openstack.org/)
4. [Open vSwitch](http://www.openvswitch.org/)
5. [Taas-explanation](https://www.gigamon.com/blog/2015/10/26/tap-as-a-service-enabling-traffic-monitoring-in-openstack-clouds-2/)  



