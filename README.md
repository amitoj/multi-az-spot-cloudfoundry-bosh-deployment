### multi-az-spot-cloudfoundry-bosh-deployment

Good cloud software (like Cloud Foundry) should be engineered to have no single points of failures.
Resiliancy is achieved by by running multiple redundant version of each component.
The expectation is that VM failures happen regularly (but are isolated to one of the redundant copies).

AWS datacentre regions are engineered in a similar way; being split into multiple availability zones.  
The expectation is that failures are isolated to one zone.

AWS, Google and Azure are in a price war; and seem committed to matching on-demand prices.  That is good, but not the whole story.

The under reported price is that of the AWS spot market; which can be up to [*10 x cheaper* than on demand prices](http://ec2pricing.iconara.info/#!/eu-west-1?sort=ram:desc&os=linux&period=monthly).

| Instance | On demand / month | 
|----------|-------------------|
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


### diagram
```
               AZ 1                                                AZ 2                                               AZ 3                 
                                                                                                                                           
+-+PUBLIC+--------------------------+              +-+PUBLIC+--------------------------+              +-+PUBLIC+--------------------------+
|                                   |              |                                   |              |                                   |
|  ha_proxy_z1                      |              |                                   |              |                                   |
|  postgres_z1                      |              |                                   |              |                                   |
|  nats_z1                          |              |  nats_z2                          |              |                                   |
|  etcd_leader_z1                   |              |                                   |              |                                   |
|  etcd_z1                          |              |  etcd_z2                          |              |                                   |
|                                   |              |                                   |              |                                   |
|  router_z1                        |              |  router_z2                        |              |                                   |
|  uaa_z1                           |              |  uaa_z2                           |              |                                   |
|  api_z1                           |              |  api_z2                           |              |                                   |
|  hm9000_z1                        |              |  hm9000_z2                        |              |                                   |
|  loggregator_z1                   |              |  loggregator_z12                  |              |                                   |
|  loggregator_trafficcontroller_z1 |              |  loggregator_trafficcontroller_z2 |              |                                   |
|  runner_z1	                    |              |  runner_z2                        |              |                                   |
|                                   |              |                                   |              |                                   |
+-----------------------------------+              +-----------------------------------+              +-----------------------------------+

```
