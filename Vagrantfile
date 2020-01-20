required_plugins = %w( vagrant-hostsupdater vagrant-berkshelf )
required_plugins.each do |plugin|
exec "vagrant plugin install #{plugin};vagrant #{ARGV.join(" ")}" unless Vagrant.has_plugin? plugin || ARGV[0] == 'plugin'
end

Vagrant.configure("2") do |config|

config.omnibus.chef_version = '14.12.9'
config.vm.box = "ubuntu/xenial64"
config.vm.synced_folder "app", "/home/ubuntu/app"
config.vm.provision "chef_zero" do |chef|
  chef.cookbooks_path = 'cookbook'
  chef.add_recipe 'Python_cookbook'
  chef.nodes_path = 'cookbook/Python_cookbook/nodes'
  end
end
