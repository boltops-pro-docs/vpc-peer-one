<!-- note marker start -->
**NOTE**: This repo contains only the documentation for the private BoltsOps Pro repo code.
Original file: https://github.com/boltopspro/vpc-peer-one/blob/master/README.md
The docs are publish so they are available for interested customers.
For access to the source code, you must be a paying BoltOps Pro subscriber.
If are interested, you can contact us at contact@boltops.com or https://www.boltops.com

<!-- note marker end -->

# VPC Peer One Blueprint

[![Watch the video](https://img.boltops.com/boltopspro/video-preview/single/vpc-peer-one.png)](https://www.youtube.com/watch?v=dcWbUlupvQk)

[![BoltOps Badge](https://img.boltops.com/boltops/badges/boltops-badge.png)](https://www.boltops.com)

This blueprint peers VPCs within the same AWS account.

* Creates the VPC Peering connections.
* Sets up the routes tables to route the VPCs to each other. The route tables to use are all configurable.

If you are interested in peering VPCs in different AWS accounts, refer to [boltopspro/vpc-peer](https://github.com/boltopspro-docs/vpc-peer)

## Prerequisite

* Setup config/settings.yml: [Settings Setup](https://github.com/boltopspro-docs/reference-architecture/blob/master/docs/settings-setup.md)

## Usage

1. Add blueprint to Gemfile
2. Configure: configs/vpc-peer-one values
3. Deploy blueprint

## Add

Add the blueprint to your lono project's `Gemfile`.

```ruby
gem "vpc-peer-one", git: "git@github.com:boltopspro/vpc-peer-one.git"
```

## Configure

Use the [lono seed](https://lono.cloud/reference/lono-seed/) command to generate a starter config params files.

    LONO_ENV=development lono seed vpc-peer-one
    LONO_ENV=production  lono seed vpc-peer-one

The files in `config/vpc-peer-one` folder will look something like this:

    configs/vpc-peer-one/
    ├── params
    │   ├── development.txt
    │   └── production.txt
    └── variables
        ├── development.rb
        └── production.rb

Configure the `configs/vpc-peer-one/params` and `configs/vpc-peer-one/variables` files.  All the parameters are required.

configs/vpc-peer-one/params/development.txt:

    AccepterRouteOutCidr=10.21.0.0/16 # route to development VPC CIDR
    AccepterVpc=vpc-111 # management VPC
    RequesterRouteOutCdir=10.20.0.0/16 # route to management VPC CIDR
    RequesterVpc=vpc-222 # development VPC

configs/vpc-peer-one/variables/development.rb:

```ruby
# Comma-separated lists.
@requester_route_tables="rtb-111,rtb-222,rtb-333" # IE: development
@accepter_route_tables="rtb-111,rtb-222,rtb-333"  # IE: management
```

A quick way to get the config values is from the CloudFormation template output.

![](https://img.boltops.com/boltopspro/blueprints/vpc/vpc-outputs.png)

The useful outputs are `AllRoutesTables`, `Vpc`, and `VpcCidr`.

## Deploy Peering Connections

Use the [lono cfn deploy](http://lono.cloud/reference/lono-cfn-deploy/) command to deploy.

    LONO_ENV=development lono cfn deploy vpc-peer-one-development --blueprint vpc-peer-one --sure --no-wait
    LONO_ENV=production  lono cfn deploy vpc-peer-one-production  --blueprint vpc-peer-one --sure --no-wait

## IAM Permissions

The IAM permissions required for this stack are described below.

Service | Description
--- | ---
cloudformation | To launch the CloudFormation stack.
ec2 | VPC Peering Connections
s3 | Lono managed s3 bucket

## Back to Reference Architecture

That's it. Go back to the main [boltopspro/reference-architecture](https://github.com/boltopspro-docs/reference-architecture/blob/master/README.md)
