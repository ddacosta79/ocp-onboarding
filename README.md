# Red Hat Openshift Projects Onboarding

## Overview
The goal of this repo is to provide Openshift Project Onboarding automation with a Jenkins Pipeline. 

The Jenkins Pipeline provides : 
* Creation of two environements (dev & pro) for a single project
* Granting access to the project with predifined groups
* Create Resource Quota to each project


## Prerequisites
* An Openshift service account (in the provided samples, the service account is named "jenkins")
* The Jenkins service account needs permissions to create and edit Openshift projects. Two sample yaml files are provided in this repo to create a [custom cluster role](https://github.com/ddacosta79/ocp-onboarding/tree/master/files/cluster-role.yaml) and [role binding](https://github.com/ddacosta79/ocp-onboarding/tree/master/files/role-binding.yaml)
* The Jenkins instance needs openshift plugin [OpenShift Client Jenkins Plugin](https://github.com/jenkinsci/openshift-client-plugin/blob/master/README.md)
* Openshift User Groups.

