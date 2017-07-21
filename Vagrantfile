# -*- mode: ruby -*-

Vagrant.configure(2) do |config|
  config.vm.box = "aws"
  config.ssh.username = "ubuntu"
  config.vm.synced_folder ".", disabled: true
  
  N = 1
    (1..N).each do |machine_id|

      config.vm.define "machine#{machine_id}" do |machine|
        machine.vm.provider :aws do |aws, override|
           aws.access_key_id = ENV['AWS_ACCESS_KEY']
           aws.secret_access_key = ENV['AWS_SECRET_KEY']
           aws.region = "us-west-2"
           aws.availability_zone = "us-west-2c"
           aws.subnet_id = "subnet-3bdd1e60"
           aws.associate_public_ip = true
           aws.ami =  "ami-835b4efa"
           aws.keypair_name = ENV['AWS_PRIVATE_KEY_NAME']
           aws.instance_type = "t2.micro"
           aws.security_groups = ["sg-05c97a7f"]
           aws.block_device_mapping = [
              {
                'DeviceName' => "/dev/sda1",
                'Ebs.DeleteOnTermination' => true
              }
           ]

          override.ssh.private_key_path = ENV['AWS_PRIVATE_KEY']
        end

        machine.vm.hostname = "machine#{machine_id}"
        if machine_id == N
          machine.vm.provision :ansible do |ansible|
            ansible.limit = "all,localhost"
            ansible.playbook = "site.yml"
            ansible.groups = {
              "hosts"       => ["machine[1:#{N}]"],
              "all:children" => ["hosts"],
            }
            ansible.sudo = true
          end
        end
      end
  end
end
