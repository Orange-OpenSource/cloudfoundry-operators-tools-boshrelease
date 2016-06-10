# BOSH Release for cloudfoundry-operators-tools

## Purpose

The purpose of this bosh release is to offer some preconfigured cloudfoundry applications components, for use in Bosh / Cloudfoundry operations.

The idea is to ease bosh ops activities, providing them usefull features, packaged in a bosh standard way (manifest, errand, etc ..)


### Cachet Server and Monitor

Cachet server enables cloudfoundry operator to give health status for end users. 

This release provide 2 bosh errands, pushing cf application for :
* cachet server
* cachet monitor

usefull links:
* https://github.com/ArthurHlt/Cachet-cf
* https://github.com/CastawayLabs/cachet-monitor/

prerequisite:
* a cloudfoundry org admin account
* cf marketplace with mysql for cachet persistent
* optional redis cf marketplace service, for cachet caching
* open cf application security group, so that cachet monitor can reach cachet server




## Usage

To use this bosh release, first upload it to your bosh:

```
bosh target BOSH_HOST
git clone https://github.com/cloudfoundry-community/cloudfoundry-operators-tools-boshrelease.git
cd cloudfoundry-operators-tools-boshrelease
bosh upload release releases/cloudfoundry-operators-tools-1.yml
```

For [bosh-lite](https://github.com/cloudfoundry/bosh-lite), you can quickly create a deployment manifest & deploy a cluster:

```
templates/make_manifest warden
bosh -n deploy
```

For AWS EC2, create a single VM:

```
templates/make_manifest aws-ec2
bosh -n deploy
```

### Override security groups

For AWS & Openstack, the default deployment assumes there is a `default` security group. If you wish to use a different security group(s) then you can pass in additional configuration when running `make_manifest` above.

Create a file `my-networking.yml`:

``` yaml
---
networks:
  - name: cloudfoundry-operators-tools1
    type: dynamic
    cloud_properties:
      security_groups:
        - cloudfoundry-operators-tools
```

Where `- cloudfoundry-operators-tools` means you wish to use an existing security group called `cloudfoundry-operators-tools`.

You now suffix this file path to the `make_manifest` command:

```
templates/make_manifest openstack-nova my-networking.yml
bosh -n deploy
```

### Development

As a developer of this release, create new releases and upload them:

```
bosh create release --force && bosh -n upload release
```

### Final releases

To share final releases:

```
bosh create release --final
```

By default the version number will be bumped to the next major number. You can specify alternate versions:


```
bosh create release --final --version 2.1
```

After the first release you need to contact [Dmitriy Kalinin](mailto://dkalinin@pivotal.io) to request your project is added to https://bosh.io/releases (as mentioned in README above).
