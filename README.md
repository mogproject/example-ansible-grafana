Example for Provisioning Grafana with Ansible
====

## Setup Instructions

### Step 1: Install provisioning tools

- Install Vagrant => [Download](http://www.vagrantup.com/downloads.html)
- Install Vagrant plugins

    ```
vagrant plugin install vagrant-aws
vagrant plugin install dotenv
    ```
- Install Ansible => [Instruction Guide](http://docs.ansible.com/intro_installation.html#latest-releases-via-homebrew-mac-osx)


### Step 2: Management with Amazon Web Services

- Create AWS account
- Sign into [AWS Management Console](https://console.aws.amazon.com/console/home?region=us-east-1#)
- Create IAM user and group
    - Services -> Deployment & Management -> IAM
    - Create ```vagrant``` user with Power User Access
        - Note access key id as **AWS_VAGRANT_ACCESS_KEY_ID**
        - Note its secret as **AWS_VAGRANT_SECRET_ACCESS_KEY**
    - Create ```cloudwatch``` user with CloudWatch Full Access
        - Note access key id as **AWS_CLOUDWATCH_ACCESS_KEY_ID**
        - Note its secret as **AWS_CLOUDWATCH_SECRET_ACCESS_KEY**
- Create security group for EC2 in *North Virginia Region*
    - Services -> Compute & Networking -> EC2 -> NETWORK & SECURITY -> Security Groups
    - Create security group ```grafana```
        - VPC: default VPC (not 'No VPC')
        - Inbound:

            | Type | Protocol | Port Range | Source |
            |:-----|:---------|:-----------|:-------|
            | SSH | TCP | 22 | Anywhere |
            | HTTPS | TCP | 443 | Anywhere |

        - Outbound: Allow all traffic
        - Note group id as **AWS_SECURITY_GROUP**
- Create key pair for EC2 in *North Virginia Region*
    - Services -> Compute & Networking -> EC2 -> NETWORK & SECURITY -> Key Pairs
    - Create key pair ```example-ansible-grafana``` and save ```example-ansible-grafana.pem``` in ```~/.ssh``` directory.
    - Be sure the permission on the file is set to ```0600```.
- Check out your subnet id
    - Services -> Compute & Networking -> VPC -> Subnets
    - Note subnet id of the default VPC in ```us-east-1b``` as **AWS_SUBNET_ID**


### Step 3: Clone this repository

```
$ git clone https://github.com/mogproject/example-ansible-grafana
$ cd example-vagrant-grafana
```


### Step 4: Create dotenv file

Create  ```.env``` in the ```example-vagrant-grafana``` directory with the following content.

Set your variables noted before, and define user/password for Grafana.

```
# default provider
VAGRANT_DEFAULT_PROVIDER="aws"

# credentials
AWS_VAGRANT_ACCESS_KEY_ID="XXXXXXXXXXXXXXXXXXXX"
AWS_VAGRANT_SECRET_ACCESS_KEY="XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
AWS_CLOUDWATCH_ACCESS_KEY_ID="XXXXXXXXXXXXXXXXXXXX"
AWS_CLOUDWATCH_SECRET_ACCESS_KEY="XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"

# EC2 settings
AWS_AVAILABILITY_ZONE="us-east-1b"
AWS_SUBNET_ID="subnet-XXXXXXXX"
AWS_SECURITY_GROUP="sg-XXXXXXXX"

AWS_SSH_USERNAME="ec2-user"
AWS_KEYPAIR_NAME="example-ansible-grafana"
AWS_SSH_KEY="~/.ssh/${AWS_KEYPAIR_NAME}.pem"

# Nginx basic auth for Grafana
GRAFANA_USER="XXXXXX"
GRAFANA_PASS="XXXXXX"
```

### Step 5: Provision it

- ```vagrant up```


It can take 5~10 minutes or more.  
Public IP address will be automatically assigned.


## Touch with Grafana

- Wait for an hour
     - Because cloudwatch-dump will run on ```*:05``` for each hour.

- Get the public IP address

    ```
$ vagrant ssh-config
Host example-ansible-grafana
    HostName YOUR_PUBLIC_IP
    User ec2-user
    Port 22
    UserKnownHostsFile /dev/null
    StrictHostKeyChecking no
    PasswordAuthentication no
    IdentityFile /path/to/example-ansible-grafana.pem
    IdentitiesOnly yes
    LogLevel FATAL
    ```

- Access to Grafana
    - Launch web browser
    - Access to url: ```https://YOUR_PUBLIC_IP/```
    - Accept untrusted certification
    - Input username/password defined at Step 3


## Operations

|operation|command|
|:--------|:------|
| SSH login | ```$ vagrant ssh``` |
| Rerun provisioning | ```$ vagrant provision``` |
| Stop EC2 instance | ```$ vagrant halt``` |
| Cleanup EC2 instance: | ```$ vagrant destroy``` |


## Payment

Watch out for the cost of AWS.

- EC2 instance: We use t2.micro instance.
- CloudWatch
    - Custom metrics
    - API calls
