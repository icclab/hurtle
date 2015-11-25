## Cloud Controller SDK

### Function
The SDK is a library containing many helper functions for service orchestrators to create and manage their services. A main function of the SDK in the current implementation is to deploy Heat templates on an underlying infrastructure, but it also offers many more methods.

### Helper methods
Below is a short description of the main functions in each component of the CC SDK. Details on each method provided by these components are provided within the inline documentation.
#### sdk.services
This class provides service discovery methods to retrieve service endpoint for a given service type within the service registry.
#### sdk.mcn.security
This class provides authentication methods to be used by other classes of the SDK. The current implementation is restricted to Keystone authentication.
#### sdk.mcn.runtime
The runtime library offers functions to be used by service orchestrators to create and manage alarms and callback notifications for runtime management of their service. More information can be found in the runtime_usage.md document.
#### sdk.mcn.deployment
This file contains an interface for generic deployers with one implementation for deployment via Heat. It provides methods to CRUD Heat templates through a user provided Heat endpoint.
#### sdk.mcn.util
Util provides static methods to retrieve instances of a deployer object, of the auth object, as well as method to create or dispose automatically instances of some atomic services. Note that using these methods circumvent the normal usage of the service graph and the resolver which are normally tasked to deploy all services comprising a composed application.

### Usage Examples
Examples of using the SDK are provided within the sample_so repository.