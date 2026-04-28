# Via Azure Data Factory / Fabric Pipelines

This architecture uses Azure Data Factory (ADF) or Fabric Data Pipelines — which share the same engine — to extract data from SAP systems and land it in Microsoft Fabric's OneLake.

## Why Would You Use This Scenario?

- You already have **Azure Data Factory pipelines** extracting SAP data and want to migrate or extend to Fabric
- You want to leverage ADF's mature **SAP connectors and monitoring** capabilities
- You need **complex orchestration** (e.g. extract from SAP, join with other Azure sources, then load into Fabric)
- Your data engineering team is already experienced with ADF / Data Factory concepts
- You want to use a **single pipeline framework** for SAP and non-SAP data sources

## Setup & Configuration

### Option A: Fabric Data Pipelines (recommended for new projects)

Fabric Data Pipelines use the same engine as Azure Data Factory and support the same SAP connectors:

1. Create a Data Pipeline in your Fabric workspace
2. Add Copy Data activities with SAP connectors (SAP CDC, SAP Table, SAP ODP, SAP HANA, OData)
3. Configure the SAP source connection
4. Set the destination to a Lakehouse or Warehouse in OneLake
5. Add any transformation steps (Data Flows, Notebooks, or Stored Procedures)
6. Schedule or trigger the pipeline

### Option B: Azure Data Factory → Fabric

If you already have ADF pipelines or prefer to manage pipelines in Azure:

1. Use your existing ADF SAP pipelines (or create new ones)
2. Change or add a **sink** that writes to:
   - **Azure Data Lake Storage Gen2** — then use a Fabric Shortcut to reference the data in OneLake
   - **OneLake directly** — ADF supports OneLake as a destination via the Lakehouse connector
3. In Fabric, the data is available for downstream analytics (Power BI, Notebooks, SQL Analytics)

### Data Flow

```
┌──────────────┐       ┌──────────────────────┐       ┌──────────────────┐
│ SAP Systems  │       │  Azure / Fabric       │       │ Microsoft Fabric │
│              │       │  Pipelines            │       │                  │
│ S/4HANA      │──────►│                       │──────►│ OneLake          │
│ ECC          │  CDC  │  Copy Data            │       │   Lakehouse      │
│ BW           │  ODP  │  Data Flows           │       │   Warehouse      │
│ HANA         │  SQL  │  Notebooks            │       │   Power BI       │
│              │       │                       │       │                  │
└──────────────┘       │  + SHIR / Gateway     │       └──────────────────┘
                       │  (for on-prem access) │
                       └──────────────────────┘
```

### Self-Hosted Integration Runtime (SHIR)

For SAP systems behind a firewall, ADF and Fabric Pipelines use the **Self-Hosted Integration Runtime** (SHIR):

1. Install the SHIR on a machine that can access the SAP system
2. For SAP Table/CDC/ODP: install the SAP .NET Connector (NCo) on the SHIR machine
3. Register the SHIR in Azure Data Factory or Fabric
4. Configure your pipeline's linked service to use the SHIR

> [!Note]
> The SHIR in ADF/Fabric serves a similar purpose as the on-premises data gateway (OPDG) in Power Platform, but they are different components. Fabric Pipelines can use either SHIR (for pipeline connectors) or OPDG (for Dataflow Gen2 / Power Query connectors).

## Authentication

| Method | Description | Connector Support |
| --- | --- | --- |
| **Basic (SAP user/password)** | Service account credentials | All SAP connectors |
| **SNC (Secure Network Communications)** | Encrypted communication for RFC-based connectors | SAP Table, SAP CDC, SAP ODP |
| **OAuth 2.0** | Token-based auth for cloud SAP systems | OData (SuccessFactors, S/4HANA Cloud) |
| **Windows Authentication** | For SHIR running under a domain account | SAP HANA (via SHIR) |

## ADF vs. Fabric Pipelines — Key Differences

| Feature | Azure Data Factory | Fabric Data Pipelines |
| --- | --- | --- |
| **SAP Connectors** | Full set (CDC, Table, ODP, HANA, BW) | Same connectors (shared engine) |
| **Destination** | ADLS, SQL, Cosmos, OneLake, ... | OneLake (Lakehouse / Warehouse) natively |
| **Management** | Azure Portal | Fabric workspace |
| **Monitoring** | ADF Monitor, Azure Monitor | Fabric Monitor |
| **Cost Model** | Azure consumption-based | Fabric capacity-based |
| **Migration** | — | ADF pipelines can be migrated to Fabric |

For new Fabric projects, using Fabric Data Pipelines directly is recommended. For existing ADF investments, writing to ADLS + Fabric Shortcuts provides a smooth bridge.

## Links & Resources

* [Data Pipelines in Fabric](https://learn.microsoft.com/en-us/fabric/data-factory/data-factory-overview)
* [SAP CDC Connector](https://learn.microsoft.com/en-us/fabric/data-factory/connector-sap-change-data-capture-overview)
* [Self-Hosted Integration Runtime](https://learn.microsoft.com/en-us/azure/data-factory/create-self-hosted-integration-runtime)
* [Migrate ADF to Fabric](https://learn.microsoft.com/en-us/fabric/data-factory/compare-fabric-data-factory-and-azure-data-factory)
