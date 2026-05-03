---
title: Architecture - Direct SAP Connectors in Fabric
description: Using Microsoft Fabric's built-in SAP connectors (ODP, HANA, Table) to extract data directly into OneLake.
author: hobruche
ms.service: sap-on-azure
ms.subservice: center-sap-solutions
ms.topic: overview
ms.custom: microsoft-ai
ms.date: 05/03/2026
ms.author: hobruche
---

# Direct SAP Connectors in Fabric

This architecture uses Microsoft Fabric's built-in SAP connectors to extract data directly from SAP systems into OneLake — without an intermediate integration layer.

## Why Would You Use This Scenario?

- You want a **straightforward, direct** connection between SAP and Fabric
- You don't have (or don't want to use) SAP BTP, SAP Datasphere, or Azure Data Factory as an intermediate layer
- Your SAP system is **accessible from the cloud** (either publicly available, via on-premises data gateway, or running on Azure)
- You want to use Fabric-native tools for the entire data pipeline

## Setup & Configuration

### Option A: Data Pipelines (enterprise-scale)

Data Pipelines in Fabric support the following SAP connectors:

| Connector | Source System | Use Case |
| --- | --- | --- |
| **SAP Table** | SAP ECC, S/4HANA | Direct table-level extraction |
| **SAP ODP** | SAP ECC, S/4HANA, BW | Extraction via Operational Data Provisioning framework |
| **SAP HANA** | SAP HANA DB | Direct SQL access to HANA |
| **SAP BW Open Hub** | SAP BW | Extraction via Open Hub Destinations |
| **OData** | Any SAP OData service | Consume OData APIs |

**Steps:**
1. Create a new Data Pipeline in your Fabric workspace
2. Add a Copy Data activity and select the appropriate SAP connector
3. Configure the connection (hostname, client, credentials)
4. Configure the source (table, ODP context, CDS view, OData endpoint, ...)
5. Configure the destination (Lakehouse table or file in OneLake)
6. Set up a schedule or trigger for recurring extraction

### Option B: Dataflows Gen2 (self-service / smaller scale)

Dataflows Gen2 use the Power Query engine and provide a visual, low-code experience:

1. Create a new Dataflow Gen2 in your Fabric workspace
2. Select the appropriate connector (SAP HANA, SAP BW, OData)
3. Connect to your SAP system and select the data
4. Apply transformations using the Power Query editor
5. Load data into a Lakehouse table or Warehouse

### On-Premises Data Gateway

If your SAP system is behind a firewall, you need the **on-premises data gateway** to bridge the connectivity:

1. Install the on-premises data gateway on a machine that can reach the SAP system
2. For SAP HANA: install the SAP HANA ODBC driver on the gateway machine
3. For SAP Table/ODP connectors: install the SAP .NET Connector (NCo) on the gateway machine
4. Register the gateway in the Fabric / Power Platform admin center
5. Configure your pipeline or dataflow to use the gateway connection

## Authentication

| Method | Description | Connector Support |
| --- | --- | --- |
| **Basic (SAP user/password)** | Simple credentials, suitable for service accounts | All connectors |
| **SSO via Kerberos** | End-user identity propagation through Kerberos constrained delegation | SAP HANA, SAP BW (via gateway) |
| **OAuth 2.0** | Token-based authentication for cloud SAP systems | OData (SuccessFactors, S/4HANA Cloud) |
| **X.509 Certificate** | Certificate-based authentication | SAP Table |

## Supported SAP Systems

| SAP System | Recommended Connector | Notes |
| --- | --- | --- |
| SAP S/4HANA Cloud (public) | OData | Use published OData services |
| SAP S/4HANA (private cloud / on-prem) | SAP Table, SAP ODP, OData | Gateway may be required |
| SAP ECC | SAP Table, SAP ODP | Gateway required |
| SAP BW/4HANA | SAP BW Open Hub, SAP ODP | |
| SAP HANA (standalone) | SAP HANA | Direct SQL access |
| SAP SuccessFactors | OData | Cloud-to-cloud, no gateway needed |

## Links & Resources

* [SAP Table Connector](https://learn.microsoft.com/en-us/azure/data-factory/connector-sap-table)
* [SAP HANA Connector](https://learn.microsoft.com/en-us/power-query/connectors/sap-hana/overview)
* [On-premises Data Gateway](https://learn.microsoft.com/en-us/data-integration/gateway/service-gateway-onprem)
