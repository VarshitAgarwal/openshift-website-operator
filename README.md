Getting Started With Operators
===================================

Okay. This explaination does require some of the basic Openshift knowledge about a couple of things. Here are they and should you need more about them, given are the resources. 

1. [Service Account](https://docs.openshift.com/container-platform/4.2/authentication/understanding-and-creating-service-accounts.html) A damn good blog for learning about ServiceAccounts.
2. [Routes](https://docs.openshift.com/container-platform/4.2/networking/configuring_ingress_cluster_traffic/configuring-ingress-cluster-traffic-ingress-controller.html) A place to learn about Openshift Ingress and Routes
3. [Role Based Access Control](https://docs.openshift.com/container-platform/4.2/authentication/using-rbac.html) Red Hat documentation for learning about Role based access control.
4. [Security Context Constraints](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html) Red Hat documentation for learning about security context constraint.

Now when I say you need the basics, you only need to ba able to give permissions using the oc adm policy and give scc access to the service accounts. 
In case of service and routes, you need to know how to expose a service in order to create a route.

Tested On
--------------------
1. A working Openshift 4.2 cluster running on AWS.

Things you'll need
--------------------
1. A working Openshift Cluster

For installing CRC Openshift 4, follow the following:

* [OpenShift 4 IPI Installation](https://developers.redhat.com/blog/2019/09/05/red-hat-openshift-4-on-your-laptop-introducing-red-hat-codeready-containers/) A tutorial about working with Openshift Installations

A bit about Kubernetes Operators. ( Skip if you know what are Operators )
--------------------
I am not going to give you any technical definitions but to be honest, when I was learning about them, finding resources, those technical definitions did not make much sense to me. 

I want you to think something simple, as a use case. 

You are moving out from your city to somewhere else, maybe to London, and you need a place to stay. You go to a housing company, and they give you the house and the keys as well to that house. You go there and then you live. 
Things are going good and they are all fine, until one day something goes wrong.

1. Your bedroom's window was broken as there were some people playing cricket outside.
2. Your bathroom's tap was leaking because of it was old and needed maintainance.
3. You wanted to install new tiles and add a new chimney in your kitchen but for that, you need to put all the stuff outside, carefully, making sure it does not break, and once the work is done, you need to put it back.

Now while you were living in that home, it was good. But now, you are kind of on your own to do the regular maintainance tasks and doing it all manually takes a lot of your time and you wanted to relax and watch NetFlix or learn a new skill. You are sad.

Surprisingly..

Your friend also moved to London a couple of weeks after you and he also needed a place to stay. He this time went to a different housing company which gave them a house to stay, the keys, and also they sent along a man from their company who would stay at hist home, under his conditions and the only thing he would do is to maintain the house. Now he also find himself in the same situation over as yours.

1. His bedroom's window was broken as there were some people playing cricket outside.
2. His bathroom's tap was leaking because of it was old and needed maintainance.
3. He wanted to install new tiles and add a new chimney in your kitchen but for that, he needed to put all the stuff outside, carefully, making sure it does not break, and once the work is done, he needed to put it back.

But only this time, he is not doing it by himself. The person who was sent by the housing company knows the house and has the knowledge of all those tasks, so rather than your friend doing it, the man is doing it all for him, automatically. Taking care of window maintainance, kitchen upgrades and tap leakage fixing. 

This is what an Operator is.
---------------------------
An operator is an extension of the application's engineering team you are deploying, which listens on the events related to this application, and takes decision and makes changes which can involve but not limited to, taking backups, handling updates or upgrades, starting new deployments and so on. In a nutshell, the operator is the man which comes with your application, that would do the day-2-operations for you on your behalf. 

The Operators has a maturity level. Starting from simple deployment to all the way from a much more complex auto-piloted mode where it takes care of backups, restores, upgrades and component failure.

 

Once you have the required Openshift cluster setup by CRC or some other ways, create the project and deploy Jenkins-ephemeral from the template.

> shub ocp4.2 ~/sample-pipeline % oc new-project jenkins-ci-cd  \
> shub ocp4.2 ~/sample-pipeline % oc new-app --name jenkins jenkins-ephemeral


