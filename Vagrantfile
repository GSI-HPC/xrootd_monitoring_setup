boxes={
  "server":{networks:['172.17.0.10'], forwarded:[[9090,9099]]},
  "grafana":{networks:["172.18.0.50"],forwarded:[[ 3000, 3000],[9090,9000]]}
}

provider= "libvirt"

Vagrant.configure("2") do |config|
  boxes.each do |name,box|
    config.vm.synced_folder ".", "/vagrant", type: "rsync",
      rsync__exclude: ".git/"
    config.vm.define "#{name}" do |vm|
      vm.vm.hostname="#{name}"
      vm.vm.graceful_halt_timeout = 300
      vm.vm.provider provider do |v|
        if provider=="libvirt" then
          v.cpu_mode = "host-model"
        end
        v.memory = 1024
        v.cpus = 2
      end
      vm.vm.box = "generic/centos8"
      box[:networks].each do |ipi|
        #generate all networks
        vm.vm.network "private_network", ip: ipi
        box[:forwarded].each do| guest,host|
          vm.vm.network "forwarded_port", guest: guest , host: host
        end
      end
      if box[:ib]
        #install ib if needed
        end
      vm.vm.provision "ansible" do |ansible|
#        ansible.verbose='v'
        ansible.playbook="playbook.yml"
      end

      vm.vm.provision "shell", inline: "mkdir /etc/apparmor.d/disable -p"


    end
  end
end
