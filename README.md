Modern Mobile App Dev Workshop [![Build Status](https://travis-ci.org/openshift-labs/cloud-native-guides.svg?branch=ocp-3.10)](https://travis-ci.org/openshift-labs/cloud-native-guides)
===
This one day hands-on cloud-native workshop provides developers with and introduction to building cloud-native mobile applications using OpenShift, Spring Boot, NodeJS and Flutter.

The aim of this workshop is to walk you through the process of creating a pretty basic mobile app based on Flutter that leverages some microservices to show a list of musical events and a timeline-like comments related to the events.

Agenda
===
* Introduction to Cloud-Native apps 
* Introduction to and Flutter
* Building a CRUD service with Spring Boot to manage events
* Building a service to store/serve images with NodeJS
* Building our mobile application with Flutter
* Adding a new feature: a timeline-like service with NodeJS
* Adding a new module to our mobile app for the new feature
* Continuous Delivery 
* Debugging Services
* Telepresence?

Install Workshop Infrastructure
===

An [APB](https://hub.docker.com/r/cvicens/mobile-cloudnative-workshop-apb) is provided for 
deploying the Cloud-Native Workshop infra (lab instructions, Nexus, Gogs, Eclipse Che, etc) in a project 
on an OpenShift cluster via the service catalog. In order to add this APB to the OpenShift service catalog, log in 
as cluster admin and perform the following in the `openshift-ansible-service-broker` project :

1. Edit the `broker-config` configmap and add this snippet right after `registry:`:

  ```
    - name: gramola
      type: dockerhub
      org: cvicens
      tag: ocp-3.10
      white_list: [.*-apb$]
  ```

2. Redeploy the `asb` deployment

You can [read more in the docs](https://docs.openshift.com/container-platform/3.10/install_config/oab_broker_configuration.html#oab-config-registry-dockerhub) 
on how to configure the service catalog.

Note that if you are using the _OpenShift Workshop_ in RHPDS, this APB is already available in your service catalog.

![](images/service-catalog.png?raw=true)

As an alternative, you can also run the APB directly in a pod on OpenShift to install the workshop infra:

```
oc login
oc new-project lab-infra

>> OLD >> oc run apb --restart=Never --image="cvicens/mobile-cloudnative-workshop-apb:ocp-3.10" \
>> OLD >>    -- provision -vvv -e namespace=$(oc project -q) -e openshift_token=$(oc whoami -t)

export APB_VARIABLES="'{\"namespace\":\"$(oc project -q)\",\"openshift_token\":\"$(oc whoami -t)\"}'"
echo ${APB_VARIABLES}
oc run apb --restart=Never --image="cvicens/mobile-cloudnative-workshop-apb:ocp-3.10" -- provision -vvv ${APB_VARIABLES}
```

Or if you have Ansible installed locally, you can also run the Ansilbe playbooks directly on your machine:

> NOTE:
 sudo pip3 install openshift
 sudo ansible-galaxy install -r requirements-travis.yml

```
oc login
oc new-project lab-infra

ansible-playbook -vvv playbooks/provision.yml \
       -e namespace=$(oc project -q) \
       -e openshift_token=$(oc whoami -t) \
       -e openshift_master_url=$(oc whoami --show-server)
``` 

Lab Instructions on OpenShift
===

Note that if you have used the above workshop installer, the lab instructions are already deployed.

```
$ oc new-app osevg/workshopper:latest --name=guides \
    -e WORKSHOPS_URLS="https://raw.githubusercontent.com/openshift-labs/cloud-native-guides/ocp-3.10/_cloud-native-workshop.yml"
$ oc expose svc/guides
```

Local Lab Instructions
===
```
$ docker run -it -p 8080:8080 \
      -v $(pwd):/app-data \
      -e CONTENT_URL_PREFIX="file:///app-data" \
      -e WORKSHOPS_URLS="file:///app-data/_cloud-native-workshop.yml" \
      osevg/workshopper:latest
```
