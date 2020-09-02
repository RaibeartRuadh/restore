# -*- mode: ruby -*-
# vi: set ft=ruby :
home = ENV['HOME']

MACHINES = {
	:RaibeartRuadh_restore => {
		:box_name => "centos/7",
                :box_version => "1804.02",
		:ip_addr => '127.0.0.1',
		:disks => {  
			:sata1 => {
			:dfile => home + '/disks/sata1.vdi',
			:size => 100, #Mb
			:port => 1
		},
			:sata2 => {
			:dfile => home + '/disks/sata2.vdi',
			:size => 100, #Mb
			:port => 2
		},
			:sata3 => {
			:dfile => home + '/disks/sata3.vdi',
			:size => 100, #Mb
			:port => 3
		}
	  }
     }
}
Vagrant.configure("2") do |config|
        if Vagrant.has_plugin?("vagrant-vbguest")
                config.vbguest.auto_update = false
        end
		
MACHINES.each do |boxname, boxconfig|
config.vm.define boxname do |box|
        box.vm.box = boxconfig[:box_name]
        box.vm.host_name = "restorecontrol"
        box.vm.network "private_network", ip: boxconfig[:ip_addr]
        box.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: ['.git*', '*.vdi'], rsync__verbose: "true"
        box.vm.provider :virtualbox do |vb|
                vb.customize ["modifyvm", :id, "--memory", "2024", "--cpus", "2", "--cpuexecutioncap", "70"]
                needsController = false
                boxconfig[:disks].each do |dname, dconf|
                        unless File.exist?(dconf[:dfile])
                        vb.customize ['createhd', '--filename', dconf[:dfile], '--variant', 'Fixed', '--size', dconf[:size]]
                        needsController =  true
		   end
		end
                if needsController == true
                vb.customize ["storagectl", :id, "--name", "SATA", "--add", "sata" ]
                boxconfig[:disks].each do |dname, dconf|
                        vb.customize ['storageattach', :id,  '--storagectl', 'SATA', '--port', dconf[:port], '--device', 0, '--type', 'hdd', '--medium', dconf[:dfile]]
		end
	      end
           end
        config.vm.provision :shell, :path => "scripts/bootstrap.sh"

	end
    end
end

