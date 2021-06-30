# OCP Projects Onboarding

## Overview
The goal of this repo is to provide Openshift Project Onboarding automation.

The Jenkins Pipeline provides : 
* creation of two environement (dev & pro) for a single project
* granting access to the project with groups
* add resource quota to each project


## Prerequisites
* The Jenkins service account needs permissions to create and edit Openshift projects
* The Jenkins instance needs openshift plugin (OpenShift Client Jenkins Plugin)