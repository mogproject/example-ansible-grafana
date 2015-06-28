Example for Provisioning Grafana with Ansible
====

## Setup Instructions

### Step 1: Clone This Repository

```
$ git clone https://github.com/mogproject/example-ansible-grafana
$ cd example-vagrant-grafana
```

### Step 2: Create EC2 Instance

- Create AWS account
- Sign into [AWS Management Console](https://console.aws.amazon.com/console/home?region=us-east-1#)
- Create EC2 instance with SSH key
- Open inbound ports, SSH (TCP/22) and HTTPS (TCP/443)

### Step 3: Connection Settings

- Write `hosts` file with replacing...
    - `xxx.xxx.xxx.xxx` = Public IP Address of your EC2 instance
    - `path/to/pem` = Path to the private key

```
example-grafana ansible_ssh_host=xxx.xxx.xxx.xxx ansible_ssh_user=ec2-user ansible_ssh_private_key_file=path/to/pem
```

- Test connection

Output should be like the following.

```
$ ansible -i hosts -m ping all
The authenticity of host 'xxx.xxx.xxx.xxx (xxx.xxx.xxx.xxx)' can't be established.
RSA key fingerprint is yy:yy:yy:yy:yy:yy:yy:yy:yy:yy:yy:yy:yy:yy:yy:yy.
Are you sure you want to continue connecting (yes/no)? yes
example-grafana | success >> {
    "changed": false,
    "ping": "pong"
}
```

### Step 4: Provision It

```
$ ansible-playbook -i hosts ./playbooks/site.yml -v
```

### Step 5: Play Grafana

- Access to Grafana
    - Launch a web browser
    - Access to url: ```https://xxx.xxx.xxx.xxx/``` (Your public IP address)
    - Accept untrusted certification
    - Log in with username=`admin`, password=`admin`
- Click the top-left menu icon
- Data Sources -> Add new
    - Name: `Graphite`
    - Default: On
    - Type: Graphite
    - Url: `http://localhost:9001`
    - Access: proxy
    - Basic Auth: Enable: Off
- Dashboards -> Home -> New
- Row menu (gree button) -> Add Panel -> Graph -> Click on the title -> edit
- Select the metrics (e.g. `carbon.agents.ip-xxx-xxx-xxx-xxx-a.memUsage`)


