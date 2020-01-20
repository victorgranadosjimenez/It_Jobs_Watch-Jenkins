# Python Environment Project

## Development environment folder

We firstly provision our VM (which has been created through Virtual Box) using vagrant, we do "vagrant init" to make the "vagrantfile".

Vagrant automates and simplify the configuration of the VM, in this case we also use another powerful tool that provision our machine like Chef is, instead using shell scripts.
We generate our cookbook with "chef generate cookbook Python_cookbook" creating a cookbook folder with most of files we are going to need.


## Provisioning with Chef

Inside our cookbook we have default recipe (recipes/default.rb) that sustitute our old provision.sh shell script.
This one will run the download and installation of packages we need.

We provision our VM updating the package list: "sudo apt update -y"
installing the python pip module: "sudo apt install python3-pip -y"
installing through pip all package/libraries we will need that we keep in a requirements text file: "pip3 install -r /home/vagrant/app/requirements.txt"
and making a directory used by the app to save the csv file: "mkdir /home/vagrant/Downloads"


For the continuous integration and continuous deployment pipelines we want to build it is very important to run our tests to stop that deployment in case they fail.
To run an unit test to check the syntax we will run "chef exec rspec" and will edit the file "spec/unit/recipes/default_spec.rb"
To run the integration test to spin up the machine and get all tools ready wil run "kitchen verify" and we should edit the file "test/integration/default/default_test.rb"

In the integration test there was an issue I had where the *requirements.txt* file was not being found this was because the kitchen test was not syncing the files as it does with Vagrant.
After I edited the *recipe/default.rb* file to get all the packages and make the required directories I had to edit my *Vagrantfile* to use this cookbook to complete the provisioning. I tested this stage worked by running:
`vagrant up`
`vagrant ssh`
From there inside the machine I navigated to the Python Applications folder and ran the app to make sure it still worked

## Running and conecting to our machine
we do "vagrant up" to spin up the ubuntu machine and generate ".vagrant" hidden folder.
"vagrant ssh" to connect to our machine, we can go then to our folder "home/ubuntu/app" and use the app with "python3 main.py" and check after our sync folder "home/vagrant/Downloads".

## Github
we will create then 2 repositories in GutHub, One for app and another one with the cookbook, and push the local environment from development branch.
We need to generate certain useful files in our environment such berksfile or policyfile typing "berks vendor cookbook".
that Berksfile will help us to create a link from the app repo to the cookbook repo.
we will also have a ".gitignore" file to prevent to push the cookbook files.
in this step we use commands such git innit, git add, git status, git commit -m, git checkout, git remote add origin, git push origin development.

## Jenkins jobs
we will create 4 jenkins jobs, 2 for each repo, that will listen to our repositories, using the repo http and ssh and that trigger the webhook.
For the cookbook repo:the first job will make the tests (unit and integration) in the developer branch and if they pass the second one will be listening and will merge to master branch and call the packer image to create the AMI (Amazon Machine Image) in AWS.
For the python app repo:the first job will do a pytest in the developer branch and if they pass the second one will be listening and will merge to master branch and call the packer image to create the AMI (Amazon Machine Image) in AWS.

## Create AMI using Packer
So we had to create a Packer json file which contains how we want the AMI to look like. so "touch packer_file.json" and we start to edit the file.
We running after the command "packer build packer_file.json" that suppose to create a packer image inside a folder.
I got an error here (Failed to initialize build 'amazon-ebs': error initializing provisioner 'chef-zero': Unknown provisioner chef-zero) having problems because I am using chef-zero. 



