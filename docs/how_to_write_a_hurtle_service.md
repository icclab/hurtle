# How to write a Hurtle-compatible service?

We will use examples written in Python here but keep in mind that Service Orchestrators can use any language supported by OpenShift as long as they implement the necessary calls.

Writing a service requires a developer to write the following components:

* a Service Definition, which will be the *Service Manager* entrypoint for the new service.
* a Service Bundle, containing all necessary details for the deployment and management of a service.

## Service Definition

Each service type is given an identity and has a set of parameters defined. Below an example in :

	from occi.core_model import Kind
	from occi.core_model import Resource
	from sm.service import Service
	from sm.service import MCNApplication
	
	if __name__ == '__main__':
	
	    # defines the service to offer - the service owner defines this
	    my_svc_type = Kind(
	        'http://schemas.hurtle.it/occi/sm#',
	        'my_service',
	        title='This is an example service type',
	        attributes={
	            'endpoint.my_service.mgt': 'immutable',
	            'endpoint.other_service.mgt': 'mutable'
	        },
	        related=[Resource.kind],
	        actions=[]
	    )
	
	    # Create a service
	    srv = Service(MCNApplication(), my_svc_type)
	
	    # Run the service manager
	    srv.run()

A service is an OCCI Kind declaration, with a schema address, a service name and a description (title), and a dictionary of attributes of the service. 

Attributes can be either *mutable* (read/write access) or *immutable* (read-only). 

In the former, they refer to parameters which can be set by other services during the provisioning phase (e.g. in the case of a service composition, credentials and endpoints access to service B are fed to the main service A so it can access it). 

In the latter, they describe parameters which will be filled when the service is ready for use (e.g., the endpoint at which the service can be accessed).

## Service Bundle

The service bundle contains all the necessary files to completely manage a service: A Service Orchestrator, a Service Template Graph, an Infrastructure Template Graph. 

Upon receipt of a service creation request, the SM creates an application container in OpenShift, and when the container is ready, pushes the service bundle into the container and deploys it. Once the SO is ready, the various lifecycle phases of the service are activated depending on how the SO logic was written.

### Service Orchestrator logic

This is the application logic of the service and contains the main methods which are called at each step of the lifecycle of the service. In the initial example, it deploys a heat template in the *deploy* method, and disposes it in the *dispose* method, but can also be more complex to incorporate the other sub-services needed to implement the new service.

Such a service looks like the example below (note that some methods have been cut to make the example shorter, full version to be found in the repo **TODO:Link**):

	from sdk.mcn import util
	class ExampleSOExecution(object):
	    """
	    Interface to the CC methods. No decision is taken here on the service
	    """
	
	    def __init__(self, token, tenant):
	        super(Execution, self).__init__()
	        self.token = token
	        self.tenant_name = tenant
	        with open('example.yaml') as f:
	        	self.template = f.read()

        	self.stack_id = None
        	self.deployer = util.get_deployer(self.token,
                                          url_type='public',
                                          tenant_name=self.tenant_name)
		
	    def deploy(self):
	        if self.stack_id is None:
            	self.stack_id = self.deployer.deploy(self.template, self.token)
			
	    def dispose(self):
	        if self.stack_id is not None:
            	self.deployer.dispose(self.stack_id, self.token)
            	self.stack_id = None
            		
	    def state(self):
	        if self.stack_id is not None:
	            tmp = self.deployer.details(self.stack_id, self.token)
	            output = ''
	            try:
	                output = tmp['output']
	            except KeyError:
	                pass
	            return tmp['state'], self.stack_id, output
	        else:
	            return 'Unknown', 'N/A', ''
	            
### Upcoming: Service Template Graph: JSON Manifest

This new easy to use feature will be available in the next release. An important feature of Hurtle is to be able to compose services, thus describing which services are required by another service is critical to a non-atomic service. This is realized by a Service Manifest as a JSON file in our default implementation. Example below:

	{
	    "service_type": "http://schemas.mobile-cloud-networking.eu/occi/sm#e2e",
	    "service_description": "Example composed service",
	    "service_attributes": {
	        "mcn.cdn.id": "immutable",
	        "mcn.cdn.password": "immutable",
	        "mcn.dss.mgt": "immutable",
	        "mcn.endpoint.api": "immutable",
	        "mcn.endpoint.forwarder": "immutable",
	        "mcn.endpoint.maas": "immutable",
	        "mcn.endpoint.rcb.mgt": "immutable",
	        "mcn.endpoints.cdn.mgt": "immutable",
	        "mcn.endpoints.cdn.origin": "immutable"
	    },
	    "service_endpoint": "http://e2e.cloudcomplab.ch:8888/e2e/",
	    "depends_on": [
	      { "http://schemas.mobile-cloud-networking.eu/occi/sm#cdn": { "inputs": [] } },
	      { "http://schemas.mobile-cloud-networking.eu/occi/sm#maas": { "inputs": [] } },
	      { "http://schemas.mobile-cloud-networking.eu/occi/sm#rcb": { "inputs": [] } },
	      { "http://schemas.mobile-cloud-networking.eu/occi/sm#dnsaas": {
	          "inputs": [
	            "http://schemas.mobile-cloud-networking.eu/occi/sm#maas#mcn.endpoint.maas"
	          ] }
	      },
	      { "http://schemas.mobile-cloud-networking.eu/occi/sm#dss": {
	        "inputs": [
	          "http://schemas.mobile-cloud-networking.eu/occi/sm#maas#mcn.endpoint.maas",
	          "http://schemas.mobile-cloud-networking.eu/occi/sm#dnsaas#mcn.endpoint.api",
	          "http://schemas.mobile-cloud-networking.eu/occi/sm#cdn#mcn.endpoints.cdn.mgt",
	          "http://schemas.mobile-cloud-networking.eu/occi/sm#cdn#mcn.endpoints.cdn.origin",
	          "http://schemas.mobile-cloud-networking.eu/occi/sm#cdn#mcn.cdn.password",
	          "http://schemas.mobile-cloud-networking.eu/occi/sm#cdn#mcn.cdn.id"
	        ] }
	      }
	    ],
	    "resources": [
	        {
	            "http://schemas.mobile-cloud-networking.eu/occi/service#cc": {
	                "uri": "file:///root/my/bundle/data/itg.yaml"
	                "location": "RegionOne"
	            }
	        }
	    ]
	}

Of note is the section named “depends_on” (Line 22). In this section the service dependencies are listed. A service dependency is noted by the service type identifier and the set of the required attributes (identified by name) a service request needs. The resolution of these service types and the required parameters is carried out by the SO’s Resolver component.The initial example described above does not use any external services and does not need a service manifest in the current implementation.

### Infrastructure Template Graph: Heat Template

Finally the resources necessary to create the new service are described in a Heat template. This represents a set of virtual resources such as virtual servers, virtual networks, configuration parameters at boot time, etc.

All information on how to write a Heat template can be found in the [Heat Documentation](http://docs.openstack.org/developer/heat/).

<!--## Configuration File

By default a configuration file called sm.cfg can be found under etc/ with explanations of each values. To quickly start one, many can be left as default, with only mandatory updates to:

* bundle_path
* -->

## Run it!

The easiest is to have a directory structure similar to what is written in the examples with the service definition python file along with the service bundle directory in the same folder.

Running your new service requires you to launch your service manager (through the service definition) with a command similar to:

	python ./example_service_manager.py -c etc/sm.cfg

By default it runs on port 8888 and accept all the curl commands described here.**TODO: Link**

### Create a Service Instance
### Retrieve a Service Instance
### Delete a Service Instance


# Examples

Examples can be found under the examples directory of the repository.