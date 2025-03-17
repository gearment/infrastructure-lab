# DESCRIBE INFRASTRUCTURE:
  ![image info](./infras.drawio.svg)

# CONFIG INFRAS:

## folder structure
```
.
│   
└───key (store ssh keys if we create key for ec2)
│       
│
└───tools (store infras tools)
│   │   tools.sh (main tools - can run help for more information)
│   │
│   └───function
│       │   tf-tool.sh (tools to run terragrunt)
│       │   setup.sh (download binary tools if needed)
│   
└───cloud
    └───aws (provider)
    |   └───apps
    |   |   └───<app_name>
    |   |       | terragrunt.hcl
    |   |       | variables.yaml
    |   |
    |   └───infras 
    |       └───<core_infras>
    |           | terragrunt.hcl
    |           | variables.yaml
    |                 
    |   project.yaml (1)
    |   terragrunt.hcl (2)
```

## config variables 
`project.yaml (1)`:
```
  project:
    company: company name
    env: environment of infrastructure
    key_directory: where to store keys
  terraform_config:
    profile: aws credentials profile name 
    zone: aws region apply this infras
    bucket: s3 state bucket name
    dynamodb_table: terraform lock table
```

`terragrunt.hcl (2)`:
  use to config terragrunt like provider, terraform command, local variables, remote backend state, root enviroment. Note: the terragrunt.hcl will merge with terragrunt.hcl in the parent's folder file before run apply terragrunt.

`apps/variables.yaml`:
  use to config policy will be created
```
  policies:
    <policy_name>: (this will be apply prefix [company]-[env]-[policy_name])
      policy: (this will use aws standard config for policy)
```
`infras/variables.yaml`
  use to config core infras
```
vpc: (vpc config)
  cidr:                 cidr of vpc                     string
  zones:                list of availability zones      list[string]
  public_subnets:       list cidr of public subnets     list[string]
  private_subnets:      list cidr of private subnets    list[string]
  database_subnets:     list cidr of database subnets   list[string]
  single_nat_gateway:   vpc use single nat gateway      bool

ec2: (ec2|autoscaling group config)
  web-server (name anything you want for ec2 instance): 
    instance_type:            instance type                                                                   string
    ami:                      ami id for instance                                                             string
    init_script:              init script for instance (will use in terraform module)                         string
    create_key:               ssh key will be create and store in folder config in project.yaml or not        bool
    zone:                     placement of ec2 or autoscaling group                                           string
    vpc_security_group_ids:   list of security groups id                                                      list(string)
    iam_role_policies:        map of policy will attach to the roles                                          map(string:string)
    lb:                       list of load balancer                                                           list(string)
    autoscaling: (config autoscaling )
      min_size:               min size                                                                        number
      desired_size:           desired capacity                                                                number
      max_size:               max size                                                                        number
      health_check_type:      health check type                                                               string(EC2|ELB)
      health_check_grace_period: health check grace period                                                    number
      block_device_mappings: (volume config)
        - device_name:        device name                                                                     string
          ebs:
            volume_size:      volume size                                                                     number
            volume_type:      volume type                                                                     string(gp3,gp2,...)
            delete_on_termination: true
      scaling_policies: (scaling policy config)
        avg-cpu-policy-greater-than-50 (name of policy): 
          policy_type:                          policy type                                                   string
          estimated_instance_warmup:            startup time for instace                                      number
          target_tracking_configuration: 
            predefined_metric_specification:
              predefined_metric_type:           metric type for autoscaling policy                            string
            target_value:                       target value for metric                                       number

sg:
  http-access (name anything you want for sg):
    description: description string
    ingress_with_cidr_blocks: (list of rule)
      - from_port  :          from port                                                                       number
        to_port    :          to port                                                                         number
        protocol   :          protocol                                                                        string
        description:          description for rule                                                            string
        cidr_blocks:          cidr blocks will allow                                                          string

alb:
  web-server-alb (name anything you want for alb):
    security_groups:          list of security groups id                                                      list(string)
    deletion_protection:      enable deletion protection                                                      bool

s3:
  backup-web-server (name anything you want for bucket):
    acl:                      use what acl private or public                                                  string
    versioning:               enable versioning                                                               bool
```

# TO RUN THIS PROJECT:

## create
```
  s3: gearment-example-default-terraform-state
  dynamo: default-terraform-lock-table
```
## command
* Note always use action plan before action apply to make sure every is right

```
  - bash tools/tools.sh cloud --action plan --directory aws --part apps --items web-server
  - bash tools/tools.sh cloud --action apply --directory aws --part apps --items web-server
  - bash tools/tools.sh cloud --action plan --directory aws --part infras --items core
  - bash tools/tools.sh cloud --action apply --directory aws --part infras --items core
```
## manual
  backup cronjob for sync data to s3 with attached role, could add to init script to auto do this tasks: 
```
  0 0 * * * /usr/bin/aws s3 sync ./file s3://example-defaul-backup-web-server/
```
## test web server
  go to aws console get alb domain name and access in browser for curl command (curl [alb_domain_name])

## clean up process 
* Note: execute in order:
```
    - bash tools/tools.sh cloud --action destroy --directory aws --part infras --items core
    - bash tools/tools.sh cloud --action destroy --directory aws --part apps --items web-server
```
