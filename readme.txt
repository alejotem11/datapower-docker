# Create the virtual machine
docker-machine create -d virtualbox --virtualbox-memory=4096 --virtualbox-cpu-count=2 default

# Open VirtualBox and configure the shared folder to be able to mount a volume on the docker machine
# Run the container: NOTE: you should change the path of the local folders "config" and "local" accordingly:
docker run -it -v /c/LATG/virtual-machines/datapower/config:/drouter/config -v /c/LATG/virtual-machines/datapower/local:/drouter/local -e DATAPOWER_ACCEPT_LICENSE=true -e DATAPOWER_INTERACTIVE=true -e DATAPOWER_WORKER_THREADS=4 -p 9090:9090 ibmcom/datapower

# Configure datapower to allow web access through the UI
idg# configure terminal
idg(config)# web-mgmt
idg(config web-mgmt)# admin-state enabled
idg(config web-mgmt)# local-address eth0_ipv4_1 9090
idg(config web-mgmt)# idle-timeout 0
idg(config web-mgmt)# exit
idg(config)# write mem

# Make sure the "default" docker machine is up:
docker-machine ls
# In case you have to start it:
docker-machine start default

# Once configured, you can start the service as follow:
docker-compose up