### multi-az-spot-cloudfoundry-bosh-deployment

Good cloud software (like Cloud Foundry) should be engineered to have no single points of failures.
Resiliancy is achieved by by running multiple redundant version of each component.
The expectation is that VM failures happen regularly (but are isolated to one of the redundant copies).

AWS datacentre regions are engineered in a similar way; being split into multiple availability zones.  
The expectation is that failures are isolated to one zone.

AWS, Google and Azure are in a price war; and seem committed to matching on-demand prices.  That is good, but not the whole story.

The under reported price is that of the AWS spot market; which can be up to [*10 x cheaper* than on demand prices](http://ec2pricing.iconara.info/#!/eu-west-1?sort=ram:desc&os=linux&period=monthly).

| Instance | On demand / month | Spot / month | 
|----------|-------------------|--------------|
| m3.medium | $56 | $7 |
| r3.xlarge | $358 | $29 |

There are only 2 differences between the AWS spot instances and AWS on demand instances.

1.  Spot instances take longer to start (5 min vs 1 min)
2.  Spot instances "fail" more frequently; because when spot prices move above your bid price your instance gets terminate.

You might think that 2 would prevent you from using spot to host "always on" services like Cloud Foundry.

Except that:

1. There is very little spot contention in regions like eu-west-1; so spot prices are generally flat
2. Spot contention seems to be isolated to a single AZ

So, how can you take advantage of these massive spot market savings?

1.  By running good cloud software (like Cloud Foundry); with no single points of failure
2.  By spreading your deployment across 3 AZs, so a failure/price spike in one is an inconvienience rather than a disaster

And that is what this repository is all about :)

#### Generating an AWS manifest

1.  Copy `cf-shared-secrets.yml.SAMPLE` to `cf-shared-secrets.yml`.  Replace REPLACE_ME tokens

```
./generate_deployment_manifest aws cf-aws-stub.yml cf-shared-secrets.yml > deployment_manifest_aws.yml
```

#### Generating an Bosh-lite / Warden manifest
```
./generate_deployment_manifest warden cf-warden-stub.yml cf-shared-secrets.yml > deployment_manifest_warden.yml
```


### Sample bosh deploy

So you know what a successful "spot deploy" looks like :)

```
$ bosh -n deploy
Getting deployment properties from director...
Unable to get properties list from director, trying without it...
Compiling deployment manifest...
AWS spot instance support is an experimental feature.
Please log an issue at https://github.com/cloudfoundry/bosh/issues if you run into any issues related to spot instances.


Director task 78
  Started preparing deployment
  Started preparing deployment > Binding deployment. Done (00:00:00)
  Started preparing deployment > Binding releases. Done (00:00:00)
  Started preparing deployment > Binding existing deployment. Done (00:00:00)
  Started preparing deployment > Binding resource pools. Done (00:00:00)
  Started preparing deployment > Binding stemcells. Done (00:00:00)
  Started preparing deployment > Binding templates. Done (00:00:00)
  Started preparing deployment > Binding properties. Done (00:00:00)
  Started preparing deployment > Binding unallocated VMs. Done (00:00:01)
  Started preparing deployment > Binding instance networks. Done (00:00:00)
     Done preparing deployment (00:00:01)

  Started preparing package compilation > Finding packages to compile. Done (00:00:00)

  Started preparing dns > Binding DNS. Done (00:00:00)

  Started creating bound missing vms
  Started creating bound missing vms > small_ondemand_z1/0
  Started creating bound missing vms > small_z3/0
  Started creating bound missing vms > medium_z1/0
  Started creating bound missing vms > medium_z1/1
  Started creating bound missing vms > medium_z1/2
  Started creating bound missing vms > medium_z1/3
  Started creating bound missing vms > medium_z1/4
  Started creating bound missing vms > medium_z1/5
  Started creating bound missing vms > medium_z1/6
  Started creating bound missing vms > medium_z1/7
  Started creating bound missing vms > medium_z1/8
  Started creating bound missing vms > medium_z2/0
  Started creating bound missing vms > medium_z2/1
  Started creating bound missing vms > medium_z2/2
  Started creating bound missing vms > medium_z2/3
  Started creating bound missing vms > medium_z2/4
  Started creating bound missing vms > medium_z2/5
  Started creating bound missing vms > medium_z2/6
  Started creating bound missing vms > medium_z2/7
  Started creating bound missing vms > medium_z3/0
  Started creating bound missing vms > medium_z3/1
  Started creating bound missing vms > medium_z3/2
  Started creating bound missing vms > medium_z3/3
  Started creating bound missing vms > medium_z3/4
  Started creating bound missing vms > medium_z3/5
  Started creating bound missing vms > medium_z3/6
  Started creating bound missing vms > large_z1/0
  Started creating bound missing vms > large_z2/0
  Started creating bound missing vms > large_z3/0
  Started creating bound missing vms > runner_z1/0
  Started creating bound missing vms > runner_z2/0
  Started creating bound missing vms > runner_z3/0
     Done creating bound missing vms > small_ondemand_z1/0 (00:01:07)
  Started creating bound missing vms > router_z1/0
     Done creating bound missing vms > medium_z1/6 (00:02:42)
  Started creating bound missing vms > router_z1/1
     Done creating bound missing vms > medium_z2/5 (00:02:43)
  Started creating bound missing vms > router_z2/0
     Done creating bound missing vms > medium_z1/8 (00:02:44)
  Started creating bound missing vms > router_z2/1
     Done creating bound missing vms > medium_z2/2 (00:02:44)
  Started creating bound missing vms > router_z3/0
     Done creating bound missing vms > medium_z3/5 (00:02:44)
  Started creating bound missing vms > router_z3/1
     Done creating bound missing vms > medium_z2/1 (00:02:45)
     Done creating bound missing vms > medium_z3/2 (00:02:44)
     Done creating bound missing vms > medium_z2/6 (00:02:44)
     Done creating bound missing vms > medium_z1/3 (00:02:45)
     Done creating bound missing vms > medium_z1/2 (00:02:45)
     Done creating bound missing vms > medium_z3/0 (00:02:46)
     Done creating bound missing vms > medium_z1/4 (00:02:47)
     Done creating bound missing vms > medium_z3/6 (00:02:46)
     Done creating bound missing vms > medium_z2/3 (00:02:48)
     Done creating bound missing vms > medium_z3/4 (00:02:47)
     Done creating bound missing vms > medium_z3/1 (00:02:48)
     Done creating bound missing vms > medium_z2/4 (00:02:49)
     Done creating bound missing vms > medium_z1/1 (00:02:49)
     Done creating bound missing vms > medium_z2/7 (00:02:49)
     Done creating bound missing vms > medium_z1/0 (00:02:51)
     Done creating bound missing vms > medium_z1/7 (00:02:53)
     Done creating bound missing vms > medium_z2/0 (00:02:54)
     Done creating bound missing vms > medium_z3/3 (00:02:55)
     Done creating bound missing vms > medium_z1/5 (00:02:57)
     Done creating bound missing vms > runner_z2/0 (00:03:20)
     Done creating bound missing vms > runner_z1/0 (00:03:23)
     Done creating bound missing vms > runner_z3/0 (00:03:21)
     Done creating bound missing vms > large_z1/0 (00:03:24)
     Done creating bound missing vms > large_z3/0 (00:03:24)
     Done creating bound missing vms > large_z2/0 (00:03:29)
     Done creating bound missing vms > small_z3/0 (00:03:33)
     Done creating bound missing vms > router_z1/0 (00:03:33)
     Done creating bound missing vms > router_z2/1 (00:02:08)
     Done creating bound missing vms > router_z1/1 (00:02:11)
     Done creating bound missing vms > router_z3/1 (00:02:11)
     Done creating bound missing vms > router_z2/0 (00:02:13)
     Done creating bound missing vms > router_z3/0 (00:02:16)
     Done creating bound missing vms (00:05:00)

  Started binding instance vms
  Started binding instance vms > ha_proxy_z1/0
  Started binding instance vms > ha_proxy_z2/0
  Started binding instance vms > ha_proxy_z3/0
  Started binding instance vms > nats_z1/0
  Started binding instance vms > nats_z2/0
  Started binding instance vms > nats_z3/0
  Started binding instance vms > postgres_z1/0
  Started binding instance vms > uaa_z1/0
  Started binding instance vms > uaa_z2/0
  Started binding instance vms > uaa_z3/0
  Started binding instance vms > login_z1/0
  Started binding instance vms > login_z2/0
  Started binding instance vms > login_z3/0
  Started binding instance vms > api_z1/0
  Started binding instance vms > api_z2/0
  Started binding instance vms > api_z3/0
  Started binding instance vms > clock_global/0
  Started binding instance vms > api_worker_z1/0
  Started binding instance vms > api_worker_z2/0
  Started binding instance vms > api_worker_z3/0
  Started binding instance vms > etcd_z1/0
  Started binding instance vms > etcd_z2/0
  Started binding instance vms > etcd_z3/0
  Started binding instance vms > hm9000_z1/0
  Started binding instance vms > hm9000_z2/0
  Started binding instance vms > hm9000_z3/0
  Started binding instance vms > runner_z1/0
  Started binding instance vms > runner_z2/0
  Started binding instance vms > runner_z3/0
  Started binding instance vms > loggregator_z1/0
  Started binding instance vms > loggregator_z2/0
  Started binding instance vms > loggregator_z3/0
     Done binding instance vms > uaa_z3/0 (00:00:00)
  Started binding instance vms > loggregator_trafficcontroller_z1/0
     Done binding instance vms > nats_z3/0 (00:00:00)
  Started binding instance vms > loggregator_trafficcontroller_z2/0
     Done binding instance vms > uaa_z1/0 (00:00:00)
  Started binding instance vms > loggregator_trafficcontroller_z3/0
     Done binding instance vms > api_worker_z1/0 (00:00:00)
  Started binding instance vms > router_z1/0
     Done binding instance vms > etcd_z2/0 (00:00:00)
  Started binding instance vms > router_z2/0
     Done binding instance vms > ha_proxy_z2/0 (00:00:00)
  Started binding instance vms > router_z3/0
     Done binding instance vms > api_z2/0 (00:00:00)
     Done binding instance vms > nats_z1/0 (00:00:00)
     Done binding instance vms > ha_proxy_z1/0 (00:00:00)
     Done binding instance vms > postgres_z1/0 (00:00:00)
     Done binding instance vms > api_z1/0 (00:00:00)
     Done binding instance vms > login_z2/0 (00:00:00)
     Done binding instance vms > hm9000_z3/0 (00:00:00)
     Done binding instance vms > api_worker_z3/0 (00:00:00)
     Done binding instance vms > etcd_z1/0 (00:00:00)
     Done binding instance vms > uaa_z2/0 (00:00:00)
     Done binding instance vms > nats_z2/0 (00:00:00)
     Done binding instance vms > login_z3/0 (00:00:00)
     Done binding instance vms > loggregator_z2/0 (00:00:00)
     Done binding instance vms > runner_z2/0 (00:00:00)
     Done binding instance vms > runner_z3/0 (00:00:00)
     Done binding instance vms > loggregator_z3/0 (00:00:00)
     Done binding instance vms > loggregator_z1/0 (00:00:00)
     Done binding instance vms > hm9000_z1/0 (00:00:00)
     Done binding instance vms > login_z1/0 (00:00:00)
     Done binding instance vms > loggregator_trafficcontroller_z2/0 (00:00:00)
     Done binding instance vms > loggregator_trafficcontroller_z1/0 (00:00:00)
     Done binding instance vms > router_z2/0 (00:00:00)
     Done binding instance vms > router_z1/0 (00:00:00)
     Done binding instance vms > router_z3/0 (00:00:00)
     Done binding instance vms > loggregator_trafficcontroller_z3/0 (00:00:00)
     Done binding instance vms > clock_global/0 (00:00:00)
     Done binding instance vms > api_worker_z2/0 (00:00:00)
     Done binding instance vms > etcd_z3/0 (00:00:00)
     Done binding instance vms > hm9000_z2/0 (00:00:00)
     Done binding instance vms > api_z3/0 (00:00:00)
     Done binding instance vms > runner_z1/0 (00:00:00)
     Done binding instance vms > ha_proxy_z3/0 (00:00:01)
     Done binding instance vms (00:00:01)

  Started preparing configuration > Binding configuration. Done (00:00:10)

  Started updating job ha_proxy_z1 > ha_proxy_z1/0 (canary). Done (00:00:37)
  Started updating job ha_proxy_z2 > ha_proxy_z2/0 (canary). Done (00:00:37)
  Started updating job ha_proxy_z3 > ha_proxy_z3/0 (canary). Done (00:00:37)
  Started updating job nats_z1 > nats_z1/0 (canary). Done (00:00:40)
  Started updating job nats_z2 > nats_z2/0 (canary). Done (00:00:40)
  Started updating job nats_z3 > nats_z3/0 (canary). Done (00:00:40)
  Started updating job postgres_z1 > postgres_z1/0 (canary). Done (00:00:57)
  Started updating job uaa_z1 > uaa_z1/0 (canary). Done (00:00:44)
  Started updating job uaa_z2 > uaa_z2/0 (canary). Done (00:00:44)
  Started updating job uaa_z3 > uaa_z3/0 (canary). Done (00:00:44)
  Started updating job login_z1 > login_z1/0 (canary). Done (00:00:45)
  Started updating job login_z2 > login_z2/0 (canary). Done (00:00:45)
  Started updating job login_z3 > login_z3/0 (canary). Done (00:00:45)
  Started updating job api_z1 > api_z1/0 (canary). Done (00:01:54)
  Started updating job api_z2 > api_z2/0 (canary). Done (00:01:54)
  Started updating job api_z3 > api_z3/0 (canary). Done (00:01:53)
  Started updating job clock_global > clock_global/0 (canary). Done (00:01:11)
  Started updating job api_worker_z1 > api_worker_z1/0 (canary). Done (00:01:09)
  Started updating job api_worker_z2 > api_worker_z2/0 (canary). Done (00:01:22)
  Started updating job api_worker_z3 > api_worker_z3/0 (canary). Done (00:01:42)
  Started updating job etcd_z1 > etcd_z1/0 (canary). Done (00:00:54)
  Started updating job etcd_z2 > etcd_z2/0 (canary). Done (00:00:56)
  Started updating job etcd_z3 > etcd_z3/0 (canary). Done (00:00:59)
  Started updating job hm9000_z1 > hm9000_z1/0 (canary). Done (00:00:37)
  Started updating job hm9000_z2 > hm9000_z2/0 (canary). Done (00:00:37)
  Started updating job hm9000_z3 > hm9000_z3/0 (canary). Done (00:00:37)
  Started updating job runner_z1 > runner_z1/0 (canary). Done (00:01:12)
  Started updating job runner_z2 > runner_z2/0 (canary). Done (00:02:14)
  Started updating job runner_z3 > runner_z3/0 (canary). Done (00:02:14)
  Started updating job loggregator_z1 > loggregator_z1/0 (canary). Done (00:00:37)
  Started updating job loggregator_z2 > loggregator_z2/0 (canary). Done (00:00:37)
  Started updating job loggregator_z3 > loggregator_z3/0 (canary). Done (00:00:37)
  Started updating job loggregator_trafficcontroller_z1 > loggregator_trafficcontroller_z1/0 (canary). Done (00:00:37)
  Started updating job loggregator_trafficcontroller_z2 > loggregator_trafficcontroller_z2/0 (canary). Done (00:00:37)
  Started updating job loggregator_trafficcontroller_z3 > loggregator_trafficcontroller_z3/0 (canary). Done (00:00:37)
  Started updating job router_z1 > router_z1/0 (canary). Done (00:00:43)
  Started updating job router_z2 > router_z2/0 (canary). Done (00:00:44)
  Started updating job router_z3 > router_z3/0 (canary). Done (00:00:43)

Task 78 done

Started	2014-05-14 20:10:19 UTC
Finished	2014-05-14 20:52:03 UTC
Duration	00:41:44

Deployed `deployment_manifest_aws.yml' to `bosh-cityindex-logsearch-io'
```
