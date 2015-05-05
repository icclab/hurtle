# Hurtle Architecture

Hurtle follows the principles of services oriented architectures and its architecture is divided into a number of services. 

Before delving in the components themselves, it is important to understand the different actors involved in the writing and usage of a hurtle-managed service as well as the lifecycle of a hurtle service.

## Actors
The Service Developer or Enterprise End User (EEU) designs the service which will be offered and writes the necessary components to integrate with the Hurtle framework.

The End User (EU) is the one actually requesting an instance of a service for his own uses.

## Service Lifecycle

The technical phase (see below diagram) includes essentially all activities from technical design all the way through to technical disposal of a service.


## Architecture components

All the components are represented in the diagram below.

<img src="./hurtle_components.png" alt="hurtle_components" width=500px>

The following three sections explain the essential parts of the diagram in more details:

### Service Manager

The SM provides an external interface to the EEU and is responsible for managing service orchestrators. It takes part in the Design, Deployment, Provisioning, Operation & Runtime Management and Disposal steps in the Technical Lifecycle of the Service.

### Service Orchestrator


### Cloud Controller