require 'yaml'

cfg = YAML.load_file('config.yml')

nodes = cfg['esxi_guests']

Vagrant.configure('2') do |config|
  nodes.each do |node|
    config.vm.define node['name'] do |machine|
      machine.vm.box = node['box']
      machine.vm.hostname = node['name']

      # disable synced folders
      machine.vm.synced_folder('.', '/vagrant', type: 'nfs', disabled: true)

      # create vms with vmware esxi provider
      machine.vm.provider :vmware_esxi do |v|
        # Host Options
        v.esxi_hostname        = cfg['esxi_hostname']
        v.esxi_username        = cfg['esxi_username']
        v.esxi_password        = cfg['esxi_password']
        v.esxi_virtual_network = cfg['esxi_virtual_network']
        v.esxi_disk_store      = cfg['esxi_data_store']
        # v.esxi_resource_pool   = '/Vagrant'

        # Guest Options
        v.guest_name     = node['name']
        v.guest_numvcpus = node['numvcpus']
        v.guest_memsize  = node['memsize']
        v.guest_storage  = 20
        v.guest_username = 'vagrant'
        
        # OPTIONAL. Specify a disk type: 'thin', 'thick', or 'eagerzeroedthick'
        v.guest_disk_type = 'thin'

        # OPTIONAL. Machine Version. ESXi 6.7 supports these versions. 4,7,8,9,10,11,12,13 & 14.
        v.guest_virtualhw_version = '9'

        # OPTIONAL. Guest Autostart
        # v.guest_autostart = 'false'
      end

      # create vms with vmware desktop provider
      machine.vm.provider :vmware_desktop do |v|
        # Host Options
        v.gui                              = false
        v.allowlist_verified               = true
        v.vmx["RemoteDisplay.vnc.enabled"] = "false"
        v.vmx["RemoteDisplay.vnc.port"]    = "5900"

        # Guest Options
        v.vmx["displayName"]          = node['name']
        v.vmx["numvcpus"]             = node['numvcpus']
        v.vmx["memsize"]              = node['memsize']
        # v.vmx["cpuid.coresPerSocket"] = "1"
        # v.vmx["ethernet0.virtualDev"] = "vmxnet3"
        # v.vmx["scsi0.virtualDev"]     = "lsisas1068"
      end

      # run ansible playbook
      machine.vm.provision :ansible do |ansible|
        ansible.compatibility_mode = "2.0"
        ansible.playbook = "provisioning/playbook.yml"
        ansible.become = true
      end
    end
  end
end
