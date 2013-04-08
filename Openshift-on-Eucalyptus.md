
## Introduction

This article explains how to setup openshift origin on a, Fedora 18 instance running on a Eucalyptus private cloud. In order to get Fedora 18 up and running as an instance on Eucalyptus you should follow this article , [Fedora 18 EMI](https://github.com/eucalyptus/eucalyptus/wiki/Fedora-18-Image)

## Setup

The setup is very simple (POC based), it consists of 1 instance running Fedora 18, this instance would act as both broker as well as node in the openshift setup.

The instance type chosen is m1.xlarge that provides 2CPU/2048MB RAM/20GB of disk, the EMI is bfEBS hence providing persistent disk for root filesystem.

## Configuration

In order to setup Openshift origin we rely on the [openshift ansible playbook](https://github.com/maxamillion/ansible-openshift_origin)

The instance does not have ansible and git installed, so the first thing to do would be get that, one could do it at runtime (when the instance gets created) or after logging into the instance.

```
yum -y install git ansible
```