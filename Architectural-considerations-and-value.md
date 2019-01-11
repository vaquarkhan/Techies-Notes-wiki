- Well, in this course we've covered a mess of approaches and tools. But if you're left with the question, "Which one of these do I use?" Then this movie is for you. - Well, we're not going to tell you which you should use.

 - Don't bait and switch the nice people, James. - Well, but we are going to tell you how to decide which is right for you and your systems. 

- All these tools and approaches have their pros and cons. One size doesn't fit all. First you need to understand your needs. And then pick the CM approach that fits those needs. And then, finally, you'll select one or more tools to implement that approach.

Remember, you want to get the most value for the least effort. 

- Yeah, it's about value. You know, serverless is a great fit for simpler applications. Most environments are full of apps that do something pretty basic, run a report, transform a file, take repeated data uploads.

 - Yes, at my job we use Lambda-based serverless apps for things like software license generation and uploading telemetry data from customer systems. There's absolutely no reason to run our own hardware and fiddle with infrastructure provisioning at all, for this kind of basic functionality.

All we do is some initial Cloud formation setup for data stores and security rules and then the apps just get redeployed out of our CI system. 

- For a more complex system but one that you can code from Scratch using a microservices approach, use Docker containers. Something like Kubernetes or Amazon ECS to orchestrate them. At work we use a lot of this approach for parts of our stack. 

- Us too. I'm working on a web property that has been completely decomposed into Docker containers, running in Amazon ECS.
Adding new features and components to it is very template-friendly.

 - But what if you have a large distributed environment, like a university lab or a corporate network with heterogeneous systems and a variety of applications? In this case it's usually a good bet to use something like Chef or Puppet to make building your systems repeatable and protect against manual changes.

 - The convergent model is particularly strong when you have a large body of systems and you don't care which exact minute out of a 15-minute period they use to update themselves.


- Okay, but what about a website with high uptime requirements? There, unless your application has been written to maintain uptime while being updated, you need orchestration to coordinate that release.

 - This is where Ansible or another orchestrated approach is a good fit. In my office we have a bunch of appliances that we have to update in coordination with other services. So we use Rundeck to initiate the upgrades by kicking-off a master list Puppet run on each node to make the change when we want it.

- Now, that's not to say you can't use these approaches for other use cases. These are just some of the natural alignments that we found. - Yes, most of these tools are flexible enough to implement whatever model you want. But fighting against a system or tool's natural inclinations is usually not worthwhile.

 - Yeah, that's right. Well, okay, but what if you're using commercial instead of open-source software? Or if you're running on a Mac or Linux or Windows platform? - That's a good question, but it matters a lot less than you'd think nowadays.

Docker runs on Windows, Linux, and Mac platforms and will do Windows containers. Chef and Puppet support, while mostly Unix and Linux focused 10 years ago, is strong on Windows today.

 - Yeah, and as for commercial software, vendors from Oracle to IBM, they're eagerly making their products available on the Cloud or in Docker containers and so on. So, don't assume that just because something's newfangled it's not going to work in your tech stack until you check. - Commercial technologies, like WebLogic, have aggressively moved to these newer platform technologies.


Because the more effort their customers have to put into hosting, installing, and maintaining commercial software, the less value they realize, and therefore the less they're willing to pay for it. 

- Most shops maintain a variety of systems that fit several of these profiles. To address that, more and more tools are being developed to manage them. - And each tool has to be learned and maintained itself. Be careful to take a lean approach and only add tools to your toolchain when you really must.

- Yeah, and you're also going to want to be careful to not just look at a tool in isolation. Local optimization is a trap. Your overall toolchain should form an optimal value stream that not only meets your needs but the needs of the other people and teams that have to interact with it.

 - That's right, and don't forget the little details that make your tools effectively usable for others. Integrate your tools with your Dev's IDEs, put them in the same source control and artifact repositories, make them usable on the Dev desktop as well as in the server space.

Bringing both sides of the house together is what makes a DevTool or an OpsTool a DevOps tool.
