# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

# ---- Define number of nodes to spin up ----
N = 4

# ---- Define any custom memory/cpu requirement ----
# if custom requirements are desired...ensure to set
# custom_cpu_mem == true otherwise set to false
# By default if custom requirements are defined and set below
# any node not defined will be configured as the default...
# which is 1vCPU/512mb...So if setting custom requirements
# only define any node which requires more than the defaults.
nodes = [
  {
    :node => "node0",
    :box => "mrlesmithjr/trusty64",
    :cpu => 1,
    :mem => 1024
  }
]

# ---- Define variables below ----
#Define if additional disks should be added (true|false)
additional_disks = false
additional_disks_controller = "SATA Controller"
#Define the number of additional disks to add
additional_disks_num = 1
#Define disk size in GB
additional_disks_size = 10
#Define if additional network adapters should be created (true|false)
additional_nics = true
# Define specific network assignments for nodes
additional_nic_assignments = [
  {
    :auto_config => true,
    :ip => "192.168.250.10",
    :method => "static",
    # :network_name => "management",
    :node => "node0"
  },
  {
    :auto_config => true,
    :ip => "192.168.1.10",
    :method => "static",
    :network_name => "net-01",
    :node => "node0"
  },
  {
    :auto_config => true,
    :ip => "192.168.2.10",
    :method => "static",
    :network_name => "net-02",
    :node => "node0"
  },
  {
    :auto_config => true,
    :ip => "192.168.3.10",
    :method => "static",
    :network_name => "net-03",
    :node => "node0"
  },
  {
    :auto_config => true,
    :ip => "192.168.250.11",
    :method => "static",
    # :network_name => "management",
    :node => "node1"
  },
  {
    :auto_config => true,
    :ip => "192.168.1.11",
    :method => "static",
    :network_name => "net-01",
    :node => "node1"
  },
  {
    :auto_config => true,
    :ip => "192.168.250.12",
    :method => "static",
    # :network_name => "management",
    :node => "node2"
  },
  {
    :auto_config => true,
    :ip => "192.168.2.12",
    :method => "static",
    :network_name => "net-02",
    :node => "node2"
  },
  {
    :auto_config => true,
    :ip => "192.168.250.13",
    :method => "static",
    # :network_name => "management",
    :node => "node3"
  },
  {
    :auto_config => true,
    :ip => "192.168.3.13",
    :method => "static",
    :network_name => "net-03",
    :node => "node3"
  }
]
#Define if add'l network adapters are auto configured addresses (true|false)
additional_nics_auto_config = true
#Define if additional network adapters should be DHCP assigned (true|false)
additional_nics_dhcp = false
#Define the number of additional nics to add
additional_nics_num = 4
ansible_groups = {
  "quagga-routers" => ["node[0:#{N-1}]"]
}
#Define Vagrant box to load
box = "mrlesmithjr/xenial64"
#Define if custom cpu and memory requirements are needed (true|false)
  #defined within nodes variable above
custom_cpu_mem = false
#Define if running desktop OS (true|false)
desktop = false
enable_additional_nic_assignments = true
#Define if custom boxes should be used...defined in nodes var..
enable_custom_boxes = false
#Define if port forwards should be enabled (true|false)
enable_port_forwards = false
#Defines if nodes should be linked from master VM (true|false)
linked_clones = false
port_forwards = [
  {
    :node => "node0",
    :guest => 3306,
    :host => 3306
  },
  {
    :node => "node0",
    :guest => 80,
    :host => 8080
  },
  {
    :node => "node0",
    :guest => 8000,
    :host => 8000
  }
]
#Define if provisioners should run (true|false)
provision_nodes = true
#Define if IP's are random assigned if not DHCP (true|false)
random_ips = false
#Define number of CPU cores
  #will be ignored if custom_cpu_mem == true
server_cpus = 1
#Define amount of memory to assign to node(s)
  #will be ignored if custom_cpu_mem == true
server_memory = 512
#Define subnet for private_network (If not using DHCP)
subnet = "192.168.202."
#Define starting last octet of the subnet range to begin addresses for node(s)
subnet_ip_start = 200

Vagrant.configure(2) do |config|
  config.vm.synced_folder ".", "/vagrant", disabled: true
  #Iterate over nodes
  (1..N).each do |node_id|
    nid = (node_id - 1)

    config.vm.define "node#{nid}" do |node|
      if enable_custom_boxes
        #Initially no so it can be set to yes if found in custom box defined
        box_set = "no"
        nodes.each do |cust_box|
          if cust_box[:node] == "node#{nid}"
            node.vm.box = cust_box[:box]
            box_set = "yes"
          end
        end
        if box_set == "no"
          node.vm.box = box
        end
      end
      if not enable_custom_boxes
        node.vm.box = box
      end
      node.vm.provider "virtualbox" do |vb|
        if linked_clones
          vb.linked_clone = true
        end
        if not custom_cpu_mem
          vb.customize ["modifyvm", :id, "--cpus", server_cpus]
          vb.customize ["modifyvm", :id, "--memory", server_memory]
        end
        if custom_cpu_mem
          nodes.each do |cust_node|
            if cust_node[:node] == "node#{nid}"
              vb.customize ["modifyvm", :id, "--cpus", cust_node[:cpu]]
              vb.customize ["modifyvm", :id, "--memory", cust_node[:mem]]
            end
          end
        end

        # Setup desktop environment
        if desktop
          vb.gui = true
          vb.customize ["modifyvm", :id, "--graphicscontroller", "vboxvga"]
          vb.customize ["modifyvm", :id, "--accelerate3d", "on"]
          vb.customize ["modifyvm", :id, "--ioapic", "on"]
          vb.customize ["modifyvm", :id, "--vram", "128"]
          vb.customize ["modifyvm", :id, "--hwvirtex", "on"]
        end

        # Add additional disks
        if additional_disks
          (1..additional_disks_num).each do |disk_num|
            dnum = (disk_num + 1)
            ddev = ("node#{nid}_Disk#{dnum}.vdi")
            unless File.exist?("#{ddev}")
              vb.customize ['createhd', '--filename', ("#{ddev}"), \
                '--variant', 'Fixed', '--size', additional_disks_size * 1024]
            end
            vb.customize ['storageattach', :id,  '--storagectl', \
              "#{additional_disks_controller}", '--port', dnum, '--device', 0, \
              '--type', 'hdd', '--medium', "node#{nid}_Disk#{dnum}.vdi"]
          end
        end
      end
      node.vm.hostname = "node#{nid}"

      # Define additional network adapters below
      if additional_nics
        if not enable_additional_nic_assignments
          if not additional_nics_dhcp
            (1..additional_nics_num).each do |nic_num|
              if random_ips
                nnum = Random.rand(0..50)
                if additional_nics_auto_config
                  node.vm.network :private_network, \
                  ip: subnet+"#{subnet_ip_start + nid + nnum}"
                end
                if not additional_nics_auto_config
                  node.vm.network :private_network, \
                  ip: subnet+"#{subnet_ip_start + nid + nnum}",
                    auto_config: false
                end
              end
              if not random_ips
                if additional_nics_auto_config
                  node.vm.network :private_network, \
                  ip: subnet+"#{subnet_ip_start + nid + nic_num}"
                end
                if not additional_nics_auto_config
                  node.vm.network :private_network, \
                  ip: subnet+"#{subnet_ip_start + nid}",
                    auto_config: false
                end
              end
            end
          end
          if additional_nics_dhcp
            (1..additional_nics_num).each do |nic_num|
              node.vm.network :private_network, type: "dhcp"
            end
          end
        end
        if enable_additional_nic_assignments
          additional_nic_assignments.each do |an|
            if an[:node] == "node#{nid}"
              if an[:network_name] == "None"
                node.vm.network :private_network, \
                ip: an[:ip], \
                auto_config: an[:auto_config]
              end
              if an[:network_name] != "None"
                node.vm.network :private_network, \
                virtualbox__intnet: an[:network_name], \
                ip: an[:ip], \
                auto_config: an[:auto_config]
              end
            end
          end
        end
      end

      # Define port forwards below
      if enable_port_forwards
        port_forwards.each do |pf|
          if pf[:node] == "node#{nid}"
            node.vm.network :forwarded_port, guest: pf[:guest], \
            host: pf[:host]
          end
        end
      end

      # Provisioners
      if provision_nodes
        if node_id == N
          node.vm.provision "ansible" do |ansible|
            ansible.verbose = "vv"
			ansible.limit = "all"
            #runs bootstrap Ansible playbook
            ansible.playbook = "bootstrap.yml"
          end
          node.vm.provision "ansible" do |ansible|
            ansible.limit = "all"
            #runs Ansible playbook for installing roles/executing tasks
            ansible.playbook = "playbook.yml"
            ansible.groups = ansible_groups
          end
        end
      end

    end
  end
  if provision_nodes
    #runs initial shell script
    config.vm.provision :shell, path: "bootstrap.sh", keep_color: "true"
  end
end
