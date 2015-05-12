# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.
  
  # Generally we use the same box for all machines
  # Feel free to change the sub-scopes should you so wish
  config.vm.box = "ubuntu/trusty64"


  # Here we share the same folder to /srv/openstreetmap-carto/ so that
  # we can have the sources available for kosmtik to render.
  config.vm.synced_folder "../openstreetmap-carto/", "/srv/openstreetmap-carto"
  # Since we want to run Salt masterless, we've got to setup the Salt root
  # as well, resulting in us being able to get the dev. environment up.
  config.vm.synced_folder "salt/", "/srv/salt/"
  
  # Private networking for the virtual machines
  config.vm.network "private_network", type: "dhcp"

  # Set up the Salt provisioner, without much special configuration.
  config.vm.provision :salt do |salt|
    salt.minion_config = "salt/minion"
    salt.run_highstate = true
  
    # IF you'd like to use any plugins for kosmtik, you can provide them 
    # underneath here by uncommenting the block. Check out the kosmtik
    # project on github for a list over plugins, or execute
    # $ node index.js plugins --available 
    # while in /usr/local/kosmtik
    
    salt.pillar({
#	"postgis" => {
#		"user" => "gisuser",
#		"password" => "gispassword"
#	}
    # "kosmtik" => {
    #   "plugins" => [
    #     "kosmtik-map-compare",
    #     "kosmtik-place-search"
    #   ]
    # }
    })
  end

  # We'd like two machines, thus we split!
  config.vm.define "gis" do |gis|
    # Uncomment to change box
    #gis.vm.box = "ubuntu/trusty64"
    # The hostname needs to correspond with salt
    gis.vm.hostname = "postgis"

    gis.vm.provider "virtualbox" do |vb|
      # We're dealing with memory intensive business: minimum should be 2048MB.
      # Default here is 4096MB, but you can decrease or increase it as you need.
      vb.customize ["modifyvm", :id, "--memory", "4096"]
      
      # The following are recommended values for the VM for relatively high 
      # degrees of responsiveness. Note that you need virtualization extensions
      # enabled on your computer to use the last option.
      # The extra CPU cores for the VM are highly recommended for importing
      # larger areas, especially when dealing with complete countries.
      # vb.customize ["modifyvm", :id, "--cpus", "4"]
      # vb.customize ["modifyvm", :id, "--hwvirtex", "on"]
    end
  
  end

  
  # Now, let's configure the kosmtik machine -- here we just render!
  config.vm.define "render" do |render|
    # Uncomment to change box
    #render.vm.box = "ubuntu/trusty64"
    # The hostname needs to correspond with salt
    render.vm.hostname = "render"

    # Setting up port-forwarding for kosmtik, so you can access it at
    # http://127.0.0.1:6789
    render.vm.network "forwarded_port", guest: 6789, host: 6789
    

  end


end
