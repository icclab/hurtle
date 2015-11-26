# How to Write a Hurtle Service

Writing a service requires a developer to write the following components:

1. a Service Definition, which will be the *Service Manager* entrypoint for the new service.
2. a Service Bundle, containing all necessary details for the deployment and management of a service.

## Service Definition

The service that is to be offered out is defined in a service manifest file (```service_manifest.json```).

```
{
    "service_type": "http://schemas.mobile-cloud-networking.eu/occi/sm#demo1",
    "service_description": "Demo 1 service",
    "service_attributes": {
      "mcn.endpoint.p1":    "immutable",
      "mcn.endpoint.p2":    ""
    },
    "service_endpoint": "http://127.0.0.1:8890/demo1/",
    "depends_on": []
}
```

In this service manifest we see that there are five attributes that are defined

 * ```service_type```: this is a URL that uniquely defines the service type. 
 * ```service_description```: this is a plain text field that provides a short description of the service.
 * ```service_attributes```: this is a dictionary of attributes that the service exposes. 
 * ```service_endpoint```: this is the endpoint where the service manager takes requests. In the example the service manager will listen to local host on port 8890. All requests are to be sent to the URL fragment of '/demo1/'. 
 * ```depends_on```: this is a dictionary of services that are dependencies of the service you are defining. In this example, the service has not examples. For how to include service dependencies see the ["Service Dependencies and Composition" section]().

One this manifest is defined you can expose the service by using the ```service_manager``` command line utility

### Using the Service Manager Utility

Before going any further, ensure that the SM library is installed. [Instructions on how to do this can be found here]().

When the service manager library is installed you can execute the following to confirm that is has been installed correctly:

```
[andy:~]$ service_manager -h
Usage: service_manager options. See service_manager -h for options.

Options:
  -h, --help            show this help message and exit
  -c CONFIG_FILE_PATH, --config-file=CONFIG_FILE_PATH
                        Path to the service manager configuration file.
```

This shows how to run the service manager. To do so you need to supply a configuration file. To understand what [configuration parameters should be set in the service manager configuration file, please see here](https://github.com/icclab/hurtle_sm#configuration)

Once you have your configuration file, simple execute the service manager:

```
[andy:~/Source/demo-sm1]$ service_manager -c etc/sm.cfg
WARNING 11/25/2015 04:11:27 PM: 	No service parameters file found in config file, setting internal params to empty.
INFO 11/25/2015 04:11:27 PM: 	Using configuration file: etc/sm.cfg
INFO 11/25/2015 04:11:27 PM: 	Using http://127.0.0.1:8890/demo1/ as the service_endpoint value from service manifest
DEBUG 11/25/2015 04:11:29 PM: 	Registering the service with the keystone service...
INFO 11/25/2015 04:11:32 PM: 	Service is now registered with keystone: Region: RegionOne Public URL:http://127.0.0.1:8890 Service ID: ecf7706c7ae849bea65715ff593df8da Endpoint ID: 4b9eab03e6774f1aa7ff9d3f4e95f5c0
DEBUG 11/25/2015 04:11:32 PM: 	Using WSGI reference implementation
```

At this point you can now issue requests against the service manager's exposed interface to create instances of that service.

## Service Bundle

The service bundle contains all the necessary files to completely manage and deliver a service: 

1. A Service Orchestrator, 
2. A Service Template Graph, 
3. An Infrastructure Template Graph. 

Upon receipt of a service creation request, the SM creates an application container in OpenShift. Depending if you're using OpenShift v2 or v3, one of two things happen:

1. You're using v2: when OpenShift has created a container is ready, the service manager pushes the service bundle into the container and deploys it. 
2. You're using v3: when OpenShift has created the container from the docker image the SO is ready.

Note that as v2 builds a container on demand the time to have a SO ready is slower than that of V3.

Once the SO is ready, the [hurtle lifecycle phases]() of the service are activated depending on how the SO logic was written.

### Service Orchestrator Logic

This is the application logic of the service and contains the main methods which are called at each step of the lifecycle of the service. In the initial example, it deploys a heat template in the *deploy* method, and disposes it in the *dispose* method, but can also be more complex to incorporate the other sub-services needed to implement the new service.

Such a service orchestrator looks like the basic example below (note that some methods have been cut to make the example shorter. More [full examples of hurtle SOs can be found here]()

```
from sdk.mcn import util


class ExampleSOE(service_orchestrator.Execution):
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
```

### Infrastructure Template Graph: Heat Template

Finally the resources necessary to create the new service are described in a Heat template. This represents a set of virtual resources such as virtual servers, virtual networks, configuration parameters at boot time, etc.

All information on how to write a Heat template can be found in the [Heat Documentation](http://docs.openstack.org/developer/heat/).

Hurtle current supports a number of different types of heat backends

 * OpenStack (native): when you install OpenStack you will get this as part of the basic installation.
 * Smart Data Center: for information on using this please see the [Smart Data Center heat plugin's repository]()
 * CloudStack: for information on using this please see the [CloudStack heat plugin's repository]()

## Service Dependencies and Composition

Hurtle supports the composition of third party services. In order to compose these services as part of the overall orchestration of a delivered service instance, the service's third-party dependencies must be declared in the service manifest document. Following on from the service manifest example above, below is a set of third-party services that must be composed together.

```
"depends_on": [
  { "http://schemas.hurtle.it/occi/sm#cdn": { "inputs": [] } },
  { "http://schemas.hurtle.it/occi/sm#maas": { "inputs": [] } },
  { "http://schemas.hurtle.it/occi/sm#rcb": { "inputs": [] } },
  { "http://schemas.hurtle.it/occi/sm#dnsaas": {
      "inputs": [
        "http://schemas.hurtle.it/occi/sm#maas#mcn.endpoint.maas"
      ] }
  },
  { "http://schemas.hurtle.it/occi/sm#dss": {
    "inputs": [
      "http://schemas.hurtle.it/occi/sm#maas#mcn.endpoint.maas",
      "http://schemas.hurtle.it/occi/sm#dnsaas#mcn.endpoint.api",
      "http://schemas.hurtle.it/occi/sm#cdn#mcn.endpoints.cdn.mgt",
      "http://schemas.hurtle.it/occi/sm#cdn#mcn.endpoints.cdn.origin",
      "http://schemas.hurtle.it/occi/sm#cdn#mcn.cdn.password",
      "http://schemas.hurtle.it/occi/sm#cdn#mcn.cdn.id"
    ] }
  }
]
```

In the example above, we see that five third-party services are required as well as the input parameters each service needs.

## Use it!
### Authentication
Authentication and access to the SM is mediated by OpenStack keystone. In order to make a service instantiation request against a SM the end user needs to supply:

 * tenant name: this should be provided through the *X-Auth-Token* HTTP header
 * token: this should be provided through the *X-Tenant-Name* HTTP header.

#### Generating a Keystone Token
**IMPORTANT:** your user must be assigned *admin* permissions for the project they're part of.

For this to work you will need the keystone command line tools installed (*pip install python-keystoneclient*) and also your OpenStack credentials.

To create a keystone token issue the following commands:

    keystone token-get

### Create a Service Instance

	curl -v -X POST http://localhost:8890/demo1/ -H 'Category: example; scheme="http://schemas.hurtle.it/occi/sm#"; class="kind";' -H 'content-type: text/occi' -H 'x-tenant-name: YOUR_TENANT_NAME' -H 'x-auth-token: YOUR_KEYSTONE_TOKEN'

This request, if successful, will return a service instance ID that can be used to request further details about the service instance.

### Retrieve a Service Instance

	curl -v -X GET http://localhost:8890/demo1/59eb41f9-8cbc-4bbd-bb16-4101703d0e13 -H 'x-tenant-name: YOUR_TENANT_NAME' -H 'x-auth-token: YOUR_KEYSTONE_TOKEN'

### Delete a Service Instance
    
    curl -v -X DELETE http://localhost:8890/demo1/59eb41f9-8cbc-4bbd-bb16-4101703d0e13 -H 'x-tenant-name: YOUR_TENANT_NAME' -H 'x-auth-token: YOUR_KEYSTONE_TOKEN'


# Further Examples

Examples can be found under the examples directory of the repository.
