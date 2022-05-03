# Baseline Dynamics 365 SCM Sensor Data Intelligence Azure Resource Deployment

[![Deploy To Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fgist.githubusercontent.com%2FAndreasHassing%2F0b31eea37b5fd27bd191a205d06e95f7%2Fraw%2Fazuredeploy.json%2F/createUIDefinitionUri/https%3A%2F%2Fgist.githubusercontent.com%2FAndreasHassing%2F0b31eea37b5fd27bd191a205d06e95f7%2Fraw%2FcreateUiDefinition.json%2F)

[![Deploy To Azure US Gov](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazuregov.svg?sanitize=true)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fgist.githubusercontent.com%2FAndreasHassing%2F0b31eea37b5fd27bd191a205d06e95f7%2Fraw%2Fazuredeploy.json%2F/createUIDefinitionUri/https%3A%2F%2Fgist.githubusercontent.com%2FAndreasHassing%2F0b31eea37b5fd27bd191a205d06e95f7%2Fraw%2FcreateUiDefinition.json%2F)

This template deploys a set of baseline Azure resources for use in Dynamics 365 SCM Sensor Data Intelligence. Sensor Data Intelligence consumes output from an insights layer (Stream Analytics) to notify and affect business processes in Dynamics 365.

The template can reuse an existing IoT Hub from a previous [Connected Field Service](https://docs.microsoft.com/en-us/dynamics365/field-service/connected-field-service) Azure resources deployment.

## Overview and deployed resources

The following resources are deployed as part of the solution:

- Azure IoT Hub: sink for IoT signals
- Azure Stream Analytics job: for transforming IoT signals into insight signals
- Azure Cache for Redis: for real-time sensor metric visualizations in Dynamics
- Azure Function: for updating the Redis cache with sensor metrics
  - With an App Service plan
- Azure Storage Account: for storing reference data from Dynamics and `AzureWebJobsStorage` target for the Azure Function
- Azure Service Bus: for storing insight signals received from Stream Analytics to be sent to Dynamics
- Azure Logic Apps: for updating reference data in blobs from-, and forwarding insight signals from Service Bus to, Dynamics
- User assigned managed identity: for securely communicating with Dynamics from Logic apps

## Prerequisites

It is expected that the entity deploying this already has some IoT systems emitting telemetry to be captured. If not, IoT simulators can be used to generate data for testing and validation.

## Deployment steps

You can click the "Deploy to Azure" button at the beginning of this document.

## Usage

It is expected that this solution will be used from within Dynamics 365, for Sensor Data Intelligence.

### Connect

After deployment, you must allowlist the deployed user assigned managed identity's client ID in Dynamics.

### Customize

After deployment, you will want to make changes to the Azure Stream Analytics job query (transform) to fit your IoT sensor telemetry into an expected shape.

To compile the Bicep file to ARM, you need to install [AZ CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Invoke [`scripts/Build-ARMTemplate.ps1`](scripts/Build-ARMTemplate.ps1) to compile the template.

## Notes

It is not recommended to reuse the Stream Analytics job between Connected Field Service and Dynamics SCM Sensor Data Intelligence, as they will evolve independently and can clash if breaking changes are applied in one or the other.

This template is a baseline and is purposefully made simple. This means that; before going into production, you should go over the individually deployed resources and make sure that they are configured securely to the specifications of your organization.

To get around some issues with fetching keys for a function app while it is deploying, we are deploying a Deployment Script which just adds a wait of 30 seconds after deploying the Azure Function. We hope to remove this in the future.

We have a [`createUiDefinition.json`](./createUiDefinition.json) file in this folder which lets us use a Resource Selector for the "Reuse existing IoT Hub" parameter.

`Tags: Dynamics 365, Sensor Data Intelligence, IoT`
