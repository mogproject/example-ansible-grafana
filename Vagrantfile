# -*- mode: ruby -*-
# vi: set ft=ruby :

# load .env file
Dotenv.load

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box       = "dummy"
  config.vm.box_url   = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"

  config.vm.define "example-ansible-grafana" do |node|
    configure_node(node) do |aws, override|
      aws.ami                    = "ami-76817c1e"  # Amazon Linux AMI 2014.03.2 (HVM)
      aws.instance_type          = "t2.micro"
      aws.tags = {
        "Name" => "example-ansible-grafana",
        "Description" => "Example server for Grafana",
      }
    end

    node.vm.provision "ansible" do |ansible|
      ansible.playbook = "playbooks/site.yml"
      ansible.extra_vars = {
        aws_cloudwatch_access_key_id: ENV['AWS_CLOUDWATCH_ACCESS_KEY_ID'],
        aws_cloudwatch_secret_access_key: ENV['AWS_CLOUDWATCH_SECRET_ACCESS_KEY'],
        grafana_user: ENV['GRAFANA_USER'],
        grafana_pass: ENV['GRAFANA_PASS']
      }
      ansible.host_key_checking = false
      ansible.verbose = "v"
    end
  end

  def configure_node(node, &block)
    node.vm.provider :aws do |aws, override|
      # credentials
      aws.access_key_id          = ENV['AWS_VAGRANT_ACCESS_KEY_ID']
      aws.secret_access_key      = ENV['AWS_VAGRANT_SECRET_ACCESS_KEY']
      aws.keypair_name           = ENV['AWS_KEYPAIR_NAME']

      # EC2 instance settings
      aws.region                 = "us-east-1"
      aws.availability_zone      = ENV['AWS_AVAILABILITY_ZONE']
      aws.subnet_id              = ENV['AWS_SUBNET_ID']
      aws.instance_ready_timeout = 120
      aws.terminate_on_shutdown  = false
      aws.elastic_ip             = true

      aws.security_groups = [ ENV['AWS_SECURITY_GROUP'] ]

      # ssh login settings
      override.ssh.username = ENV['AWS_SSH_USERNAME']
      override.ssh.private_key_path = ENV['AWS_SSH_KEY']

      # suppress errors
      override.vm.synced_folder ".", "/vagrant", disabled: true

      block.call(aws, override)
    end
  end 
end
