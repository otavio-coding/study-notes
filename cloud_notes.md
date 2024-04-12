# Setting up a cloud

### GCP:

### 1. Starting a VM 
  #### Through UI:
  * ##### Browse to GCP Console 
    
  * ##### Create a Project

  * ##### Create a VM instance (Compute Engine > VM instances > Create)
	
	- Give it a name
	- Select the Zones
	- Select the Machine 
	- Setup the "Boot Disk"
	- Setup Scopes (Default allows ssh)
	- Turn on desired Firewalls
	- Create
	- Connect clicking on ssh
	
_______		   	
###  2. Adding scripts as services to the VM:
#### Through VM's Terminal

* Clone the scripts repo into the VM
* Setup scripts according to the .service file
* Copy the **.service** systemd file into  **/etc/systemd/system/**

		cp service_name.service /etc/systemd/system/
		
* Enable the service to run automatically by running: 
		
		systemclt enable service_name 
* Reboot the VM
* Install and Setup Puppet (Search for more info on how to do this)
___
### 3. Templating an image for automated deployment

* Create an image through the console
* Create a template with the image created as boot disk
* Through the terminal authenticate Google Cloud by running:

		gcloud init

* Create multiple identical VMs using the template by running:

		gcloud compute instances create --source-instance-template <template_name> vm1 vm2 vm3 vm4 vm5