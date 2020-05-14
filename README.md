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

A bit about Operators.
--------------------
Now, This can easily be the easiest part 


Once you have the required Openshift cluster setup by CRC or some other ways, create the project and deploy Jenkins-ephemeral from the template.

> shub ocp4.2 ~/sample-pipeline % oc new-project jenkins-ci-cd  \
> shub ocp4.2 ~/sample-pipeline % oc new-app --name jenkins jenkins-ephemeral

Having the Jenkins server running, you need to create the project which Jenkins will use to deploy applications on. Usually this could be the same project where Jenkins is running, but it is a common approach to have one Jenkins server running and sharing it between multiple projects, such as dev projects.

> shub ocp4.2 ~/sample-pipeline % oc new-project jenkins-deploy

Because Jenkins is running in a different project and it will build and deploy applications in a different project, you will need to give at least edit access to the jenkins serviceaccount to your new jenkins-deploy project. 

> shub ocp4.2 ~/sample-pipeline % oc policy add-role-to-user edit system:serviceaccount:jenkins-ci-cd:jenkins 

2. In the jenkins-ci-cd project, there is a route created for the Jenkins server, use that to open Jenkins. As this is running on Openshift, you can use the same credentials to login as your own openshift cluster, thanks to the Openshift Login Plugin. 

Login using your credentials and go to Manage Jenkins ( on the left side ) ---> Configure System.

Find the "OpenShift Jenkins Sync" project and just add the project you made in the "Namespace" field. Make sure you do not remove the existing project name. Use a whitespace as a delimiter. Do not change anything else unless you know what you are doing. Hit save. 

3. Now you will need to start a new build using the Jenkinsfile given in the git repository.

> shub ocp4.2 ~/sample-pipeline % oc project jenkins-deploy \
> shub ocp4.2 ~/sample-pipeline % oc new-app --name from-jenkinsfile --code https://github.com/shkatara/jenkins-pipeline --context-dir shubham-pipeline

In some time, you should see a build starting in Openshift / Jenkins. If not, manually start it, 

> shub ocp4.2 ~/sample-pipeline % oc start-build from-jenkinsfile

4. Look at the build, it will start the pipeline and the pipeline will then start the building and prompt for the inputs for creating the route for the built application. Approve it and your application should be ready in the jenkins-deploy project. 

##Note: This might take some time because the pipeline does not wait for the build to finish, but just moves on and gives a prompt and creates a route. If that happens, the route will give a standard Openshift 404. Wait for some time, let the application build ( or even check for the builds yourself ) and once it says complete, you can access your application.

