# SAP System publically available (most likely a demo scenario)

Unlike the other architectures described here, this is your Quick-Start architecture. The goal is to quickly show you how to get started and see the Copilot Studio and SAP integration in action. For production ready scenarios, please look at the other architectures as well.  

## Why would you use this scenario?
The assumption is that you have already an SAP OData Service, that is accessible from the internet. In most cases, this is probably not the case or you have other means of protecting your endpoints already in place. 

In the following steps, we will use the public SAP Gateway Development system. This is a public hosted system from SAP, that exposes the Enterprise Procurement Model (EPM) via the GWSAMPLE_BASIC OData service. [See also](https://developers.sap.com/tutorials/odata-02-exploration-epm..html) If you do not have access yet, you can register for the [SAP Gateway Demo System here](https://developers.sap.com/tutorials/gateway-demo-signup.html)

> [!Note]
> Since the SAP Gateway Development system is being [de-commissioned](https://community.sap.com/t5/technology-blog-posts-by-sap/sap-gateway-demo-system-will-be-de-commissioned/ba-p/13353480) the recommendation for now is just to use any other public OData service (e.g. https://services.odata.org/v4/TripPinServiceRW). An alternative is to use the SAP Business Accelerator Hub, register and use an OData service for Read-only scenarios. 

## Setup & Configuration
Make sure to register for the SAP Gateway Demo System. You should be able to access the data with your P- or S-User in a browser, e.g. [https://sapes5.sapdevcenter.com/sap/opu/odata/iwbep/GWSAMPLE_BASIC/](https://sapes5.sapdevcenter.com/sap/opu/odata/iwbep/GWSAMPLE_BASIC/). A list of five Products can be retrieved via, https://sapes5.sapdevcenter.com/sap/opu/odata/iwbep/GWSAMPLE_BASIC/ProductSet?$top=5

### Agent & Copilot Development 
The easiest way to get started is with Copilot Studio. You can start a free trial at [Microsoft Copilot Studio](https://www.microsoft.com/en-us/microsoft-365-copilot/microsoft-copilot-studio). Then you can use the SAP OData Connector to connect to the public endpoint of the GWSAMPLE_BASIC Service. 

The [Microsoft 365 Agents Toolkit](https://learn.microsoft.com/en-us/microsoft-365/developer/overview-m365-agents-toolkit) also enables you to access external APIs, like the GWSAMPLE_BASIC OData service, via [API Plugins](https://learn.microsoft.com/en-us/microsoft-365-copilot/extensibility/overview-api-plugins). Since there is no native OData support, you might need to convert the OData specification into an OpenAPI specification. You can follow the instructions of the online tool [here](https://convert.odata-openapi.net/) to do that. Then you can use the instruction [here](https://learn.microsoft.com/en-us/microsoft-365-copilot/extensibility/build-api-plugins-existing-api) using the OpenAPI specification. 

### Integration & Connectivity Infrastructure
In this scenario the OData services is accessible directly from the internet. Because of this no additional integration layer is required. In a productive environment you would probably leverage an API Management solution (like [SAP API Management](./Architecture-BTP+APIM.md) or [Azure API Management](./Architecture-APIM+vnet.md) solution)

### Backend Systems & Data Sources
In our case the SAP Gateway Demo system is an SAP ECC System. However, you can also access SAP S/4HANA Systems, for example the Sandbox systems provided via the [SAP Business Accelerator Hub](https://hub.sap.com/) 
