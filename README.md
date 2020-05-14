Getting Started With Operators and Operator-SDK
===================================

About this Operator
-------------------
This is a simple but working operator which listens on the creation of resources in the website.example.com group and the version v1 for kind Website. Once the operator is deployed, it listens and watches on any creation of Website kind of resource in the project where the operator is running as a pod, you will react to it by starting a new deployment automatically.  


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
2. Installed operator-sdk on your linux machine and connection to the working openshift cluster.

For installing CRC Openshift 4, follow the following:

* [OpenShift 4 IPI Installation](https://developers.redhat.com/blog/2019/09/05/red-hat-openshift-4-on-your-laptop-introducing-red-hat-codeready-containers/) A tutorial about working with Openshift Installations.
* [Operator-SDK Installation](https://docs.openshift.com/container-platform/4.1/applications/operator_sdk/osdk-getting-started.html#osdk-installing-cli-gh-release_osdk-getting-started) Operator-SDK installation.

A bit about Kubernetes Operators. ( Skip if you know what are Operators )
--------------------
I am not going to give you any technical definitions but to be honest, when I was learning about them, finding resources, those technical definitions did not make much sense to me. 

I want you to think something simple, as a use case. 


```
You are moving out from your city to somewhere else, maybe to London, and you need a place to stay. You go to 
a housing company, and they give you the house and the keys as well to that house. You go there and then you live. 

Things are going good and they are all fine, until one day something goes wrong.

1. Your bedroom's window was broken as there were some people playing cricket outside.
2. Your bathroom's tap was leaking because of it was old and needed maintainance.
3. You wanted to install new tiles and add a new chimney in your kitchen but for that, you need to put all 
the stuff outside, carefully, making sure it does not break, and once the work is done, you need to put it back.

Now while you were living in that home, it was good. But now, you are kind of on your own to do the regular maintainance tasks and doing it all manually takes a lot of your time and you wanted to relax and watch NetFlix or learn a new skill. You are sad.
```
Surprisingly..
```
Your friend also moved to London a couple of weeks after you and he also needed a place to stay. He this time went to a different housing company which gave them a house to stay, the keys, and also they sent along a man from their company who would stay at hist home, under his conditions and the only thing he would do is to maintain the house. Now he also find himself in the same situation over as yours.

1. His bedroom's window was broken as there were some people playing cricket outside.
2. His bathroom's tap was leaking because of it was old and needed maintainance.
3. He wanted to install new tiles and add a new chimney in your kitchen but for that, he needed to put all the stuff outside, carefully, making sure it does not break, and once the work is done, he needed to put it back.

But only this time, he is not doing it by himself. The person who was sent by the housing company knows the house and has the knowledge of all those tasks, so rather than your friend doing it, the man is doing it all for him, automatically. Taking care of window maintainance, kitchen upgrades and tap leakage fixing. 
```
This is what an Operator is.
---------------------------
An operator is an extension of the application's engineering team you are deploying, which listens on the events related to this application, and takes decision and makes changes which can involve but not limited to, taking backups, handling updates or upgrades, starting new deployments and so on. In a nutshell, the operator is the man which comes with your application, that would do the day-2-operations for you on your behalf. 

The Operators has a maturity level. Starting from simple deployment to all the way from a much more complex auto-piloted mode where it takes care of backups, restores, upgrades and component failure.

About this Operator
-------------------
This is a simple


Working with this git repository.
---------------------------------
Once you have the working cluster and operator-sdk installed, you need to initialize the temporary working directory for ansible based operators.

shub ocp4.2 ~/sample-pipeline % mkdir ~/website-operator && cd ~/website-operator \
shub ocp4.2 ~/sample-pipeline % operator-sdk new website-app --type=ansible --api-version=website.example.com/v1 --kind=Website

IMPORTANT: If you are following this github which I am expecting you are, do not create your own operator. We are going to use the files from this git repository, and these are the same files created with the command.

> shub ocp4.2 ~/sample-pipeline % mkdir ~/website-operator/website-app && cd ~/website-operator/website-app \
> shub ocp4.2 ~/sample-pipeline % git clone https://github.com/shkatara/openshift-website-operator 

Make the CRD for your website-app
---------------------------------
1. Create the project
> shub ocp4.2 ~/sample-pipeline % oc new-project demo-operator \

2. Add the CRD to openshift
> shub ocp4.2 ~/sample-pipeline % oc create -f deploy/crds/website.example.com_websites_crd.yaml

A Custom Resource Definition is just a resource you specify to increate the vocabulary of Kubernetes. 

If you do, kubectl or oc get pods, you get a response, but if you do oc get bla, it gives an error of 'error: the server doesn't have a resource type "bla"'. So with the CRD, we are basically telling Openshift that you now know "bla"


Preparing the Project
----------------------
As I said this is a working Operator, but it still needs some refractoring. I will do it if I get time. But for now, there are some filesystem permission issues within the ansible execution inside the operator pod because of which the deployment fails. So you need to add the anyuid scc to your project.

> shub ocp4.2 ~/sample-pipeline % oc adm policy add-scc-to-user system:serviceaccount:demo-operator

Deploying the Operator
----------------------
This is an ansible based operator which is running inside of a pod, so you need to setup the following resources.

1. Create a service account that will run the operator.

> shub ocp4.2 ~/sample-pipeline % oc create  -f deploy/service_account.yaml

2. Give the service account enough access to create basic resources like services, pods, deployments as the operator will be creating these in your project.

> shub ocp4.2 ~/sample-pipeline % oc create  -f deploy/role.yml \
> shub ocp4.2 ~/sample-pipeline % oc create  -f deploy/role_binding.yaml

3. Deploy the operator
> shub ocp4.2 ~/sample-pipeline % oc create  -f deploy/operator.yml

This Operator is deployed from an image which is coming from "quay.io/shubhamkatara/openshift-website-operator", which on the creation of Website resource, deploys the "quay.io/shubhamkatara/openshift-website-operator-app" image.

Moment of truth
---------------

This operator is now waiting for the creation of a CR ( Custom Resource ), upon which it will take some actions by running a playbook and will orchestrate the deployment of a httpd based application from the image quay.io/shubhamkatara/openshift-website-operator-app.

Custom Resource is the instance of a Custom Resource Definition and something the Operator will watch on. 

> shub ocp4.2 ~/sample-pipeline % oc create  -f deploy/crds/website.example.com_v1_website_cr.yaml

As soon as this CR is created, a new deployment will be started and a new service will be created. This is because the operator was listening on the creation of "kind: Website" resource and started an ansible based playbook execution.
It does not create a route, so that is what you will have to do on your own.

> shub ocp4.2 ~/sample-pipeline % oc expose svc example-website-website-dc

Once deployed, go to the route and you will see the application available over at the route. :) 
