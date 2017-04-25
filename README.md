# openshift-ansible-glusterfs-test
Configurations for testing GlusterFS integration with openshift-ansible

This repository contains inventory/hosts files for use with
[openshift-ansible](https://github.com/openshift/openshift-ansible) to allow
OpenShift clusters (new or existing) to use GlusterFS volumes for storage for
their pods. The intention is to demonstrate common deployment scenarios and
their configuration parameters. This is by no means an exhaustive list of
configuration possibilities, but it should give a reasonable starting point for
most environments.

A `hosts.base` file is provided to demonstrate the minimal configuration the
rest of the scenarios are based on.

The following is an explanation of the directory structure:
```
.
├── brownfield........ Deploying to existing OpenShift clusters.
│   ├── config........ Deploying using byo/openshift-glusterfs/config.yml
│   │   ├── external.. Using an external GlusterFS cluster
│   │   └── hosted.... Using a hosted/containerized GlusterFS cluster
│   └── registry...... Deploying using byo/openshift-glusterfs/registry.yml
│       ├── external
│       └── hosted
└── greenfield........ Deploying with a new OpenShift cluster installation
    └── config........ Deploying using byo/config.yml
        ├── external
        └── hosted
```

It is assumed that all nodes which will be used for GlusterFS meet the
pre-requisites outlined in the [gluster-kubernetes project](https://github.com/gluster/gluster-kubernetes).

The following configuration parameters are considered:
 * A glusterfs group, which indicates a GlusterFS cluster
 * A glusterfs_registry group, which indicates a GlusterFS cluster that will
   have a volume created for use by a hosted Docker registry
 * A cluster is marked for use as storage for a hosted Docker registry.
   * If only a single group (of the above) is specified, that group will have a
     volume created for use by the registry.
   * If both groups are specified, the glusterfs_registry group will be used
     for the registry volume.
 * In the `external` cases, heketi (GlusterFS' volume management REST API) can
   either be hosted or external as well.
 * In brownfield cases where GlusterFS will be used for registry storage and a
   registry already exists, the current registry volume may be swapped out for
   the new GlusterFS volume.

The following openshift-ansible playbooks are used:
 * [byo/config.yml](https://github.com/openshift/openshift-ansible/tree/master/playbooks/byo)
 * [byo/openshift-glusterfs/config.yml and byo/openshift-glusterfs/registry.yml](https://github.com/openshift/openshift-ansible/tree/master/playbooks/byo/openshift-glusterfs)

Some configuration notes:
 * It is recommend to always deploy at least three nodes for GlusterFS
   clusters.
 * The GlusterFS nodes do not need to have the same configuration and size of
   storage devices.
 * The storage for a hosted registry is not required to be on the same nodes as
   the hosted registry pods. That is, the nodes specified for GlusterFS storage
   for the registry do not need to be the same nodes as will run the registry
   pods.
