---
title: Configurer Azure-SSIS Integration Runtime pour le basculement SQL Database
description: Cet article explique comment configurer Azure-SSIS Integration Runtime avec la géo-réplication et le basculement Azure SQL Database pour la base de données SSISDB.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: b4b679b15092531ff9553d221f23d7f030fc1def
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592087"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Configurer Azure-SSIS Integration Runtime avec la géo-réplication et le basculement Azure SQL Database

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Cet article explique comment configurer Azure-SSIS Integration Runtime avec la géo-réplication Azure SQL Database pour la base de données SSISDB. Lorsqu’un basculement se produit, vous pouvez garantir le bon fonctionnement d’Azure-SSIS Integration Runtime avec la base de données secondaire.

Pour plus d’informations sur la géoréplication et le basculement de SQL Database, consultez [Vue d’ensemble : géoréplication active et groupes de basculement automatique](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Basculement du runtime Azure-SSIS IR avec Azure SQL Database Managed Instance

### <a name="prerequisites"></a>Prérequis

Azure SQL Database Managed Instance utilise une clé **DMK (clé principale de base de données)** pour sécuriser les données, les informations d’identification et les informations de connexion stockées dans la base de données. Pour activer le déchiffrement automatique de la clé DMK, une copie de la clé est chiffrée à l’aide de la **clé SMK (clé principale du serveur)** . Toutefois, la clé SMK n’est pas répliquée dans le groupe de basculement, vous devez donc ajouter un mot de passe supplémentaire sur les instances primaire et secondaire pour permettre le déchiffrement de la clé DMK après le basculement.

1. Exécutez la commande ci-dessous sur la SSISDB sur l’instance principale. Cette étape consiste à ajouter un nouveau mot de passe de chiffrement.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Créez un groupe de basculement sur Azure SQL Database Managed Instance.

3. Exécutez **sp_control_dbmasterkey_password** sur l’instance secondaire à l’aide du nouveau mot de passe de chiffrement.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scénario 1 - Azure-SSIS IR pointe vers le point de terminaison de l’écouteur de lecture-écriture

Si vous souhaitez qu’Azure-SSIS IR pointe vers le point de terminaison de l’écouteur en lecture et en écriture, vous devez d’abord pointer vers le point de terminaison du serveur principal. Après avoir placé SSISDB dans le groupe de basculement, vous pouvez passer au point de terminaison de l’écouteur en lecture et en écriture, et redémarrer Azure-SSIS IR.

#### <a name="solution"></a>Solution

En cas de basculement, vous devez effectuer les opérations suivantes :

1. Arrêtez le runtime Azure-SSIS IR sur la région primaire.

2. Modifiez Azure-SSIS IR avec les nouvelles informations relatives à la région, au réseau virtuel et à l’URI SAS de configuration personnalisée de l’instance secondaire. Dans la mesure où Azure-SSIS IR pointe vers l’écouteur en lecture et en écriture, et où le point de terminaison est transparent pour Azure-SSIS IR, vous n’avez pas besoin de modifier le point de terminaison.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Redémarrez le runtime Azure-SSIS IR.

### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scénario 2 - Azure-SSIS IR pointe vers le point de terminaison du serveur principal

Le scénario convient si Azure-SSIS IR pointe vers le point de terminaison du serveur principal.

#### <a name="solution"></a>Solution

En cas de basculement, vous devez effectuer les opérations suivantes :

1. Arrêtez le runtime Azure-SSIS IR sur la région primaire.

2. Modifiez le runtime Azure-SSIS IR avec de nouvelles informations sur la région, le point de terminaison et le réseau virtuel de l’instance secondaire.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Redémarrez le runtime Azure-SSIS IR.

### <a name="scenario-3---azure-ssis-ir-is-pointing-to-public-endpoint-of-azure-sql-database-managed-instance"></a>Scénario 3 - Azure-SSIS IR pointe vers le point de terminaison public d’Azure SQL Database Managed Instance

Le scénario convient si Azure-SSIS IR pointe vers le point de terminaison public d’Azure SQL Database Managed Instance, et s’il n’est pas joint au réseau virtuel. La seule différence entre le scénario 2 et ces scénarios vient du fait que vous n’avez pas besoin de modifier les informations relatives au réseau virtuel d’Azure-SSIS IR après le basculement.

#### <a name="solution"></a>Solution

En cas de basculement, vous devez effectuer les opérations suivantes :

1. Arrêtez le runtime Azure-SSIS IR sur la région primaire.

2. Modifiez Azure-SSIS IR avec les nouvelles informations relatives à la région et au point de terminaison de l’instance secondaire.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Redémarrez le runtime Azure-SSIS IR.

### <a name="scenario-4---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scénario 4 : Attachement d’un SSISDB (catalogue SSIS) existant à un nouveau runtime d’intégration Azure-SSIS IR

Ce scénario convient si vous souhaitez provisionner un nouveau runtime d’intégration Azure-SSIS IR sur une région secondaire, ou si SSISDB doit continuer à fonctionner avec un nouveau runtime d’intégration Azure-SSIS IR dans une nouvelle région quand un sinistre lié à ADF ou Azure-SSIS IR se produit dans la région actuelle.

#### <a name="solution"></a>Solution

En cas de basculement, vous devez effectuer les opérations suivantes :

> [!NOTE]
> L’étape 4 (création du runtime d’intégration) doit être effectuée via PowerShell. Le portail Azure signalera une erreur indiquant que SSISDB existe déjà.

1. Arrêtez le runtime Azure-SSIS IR sur la région primaire.

2. Exécutez la procédure stockée pour mettre à jour les métadonnées dans SSISDB afin d’accepter les connexions à partir de **\<new_data_factory_name\>** et **\<new_integration_runtime_name\>** .
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Créez une fabrique de données nommée **\<new_data_factory_name\>** dans la nouvelle région. Pour plus d’informations, consultez Créer une fabrique de données.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    Pour plus d’informations sur cette commande PowerShell, consultez [Créer une fabrique de données Azure à l’aide de PowerShell](quickstart-create-data-factory-powershell.md)

4. Créez un nouveau runtime d’intégration Azure-SSIS nommé **\<new_integration_runtime_name\>** dans la nouvelle région à l’aide d’Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Pour plus d’informations sur cette commande PowerShell, voir [Créer le runtime d’intégration Azure-SSIS dans Azure Data Factory](create-azure-ssis-integration-runtime.md).



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Basculement du runtime Azure-SSIS IR avec Azure SQL Database

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scénario 1 - Azure-SSIS IR pointe vers le point de terminaison de l’écouteur de lecture-écriture

Ce scénario convient si Azure-SSIS IR pointe vers le point de terminaison de l’écouteur en lecture et en écriture du groupe de basculement, et si le serveur SQL Database n’est *pas* configuré avec la règle de point de terminaison de service de réseau virtuel. Si vous souhaitez qu’Azure-SSIS IR pointe vers le point de terminaison de l’écouteur en lecture et en écriture, vous devez d’abord pointer vers le point de terminaison du serveur principal. Après avoir placé SSISDB dans le groupe de basculement, vous pouvez passer au point de terminaison de l’écouteur en lecture et en écriture, et redémarrer Azure-SSIS IR.

#### <a name="solution"></a>Solution

En cas de basculement, il est transparent pour Azure-SSIS Integration Runtime. Azure-SSIS Integration Runtime se connecte automatiquement au nouveau point de terminaison principal du groupe de basculement. Si vous souhaitez mettre à jour la région ou d’autres informations dans Azure-SSIS IR, vous pouvez l’arrêter, le modifier et le redémarrer.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scénario 2 - Azure-SSIS IR pointe vers le point de terminaison du serveur principal

Le scénario convient si Azure-SSIS IR pointe vers le point de terminaison du serveur principal.

#### <a name="solution"></a>Solution

En cas de basculement, vous devez effectuer les opérations suivantes :

1. Arrêtez le runtime Azure-SSIS IR sur la région primaire.

2. Modifiez le runtime Azure-SSIS IR avec de nouvelles informations sur la région, le point de terminaison et le réseau virtuel de l’instance secondaire.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Redémarrez le runtime Azure-SSIS IR.

### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scénario 3 : Attachement d’un SSISDB (catalogue SSIS) existant à un nouveau runtime d’intégration Azure-SSIS

Ce scénario convient si vous souhaitez provisionner un nouveau runtime d’intégration Azure-SSIS IR sur une région secondaire, ou si SSISDB doit continuer à fonctionner avec un nouveau runtime d’intégration Azure-SSIS IR dans une nouvelle région quand un sinistre lié à ADF ou Azure-SSIS IR se produit dans la région actuelle.

#### <a name="solution"></a>Solution

> [!NOTE]
> L’étape 4 (création de l’IR) doit être effectuée via PowerShell. Le portail Azure signalera une erreur indiquant que SSISDB existe déjà.

1. Arrêtez le runtime Azure-SSIS IR sur la région primaire.

2. Exécutez la procédure stockée pour mettre à jour les métadonnées dans SSISDB afin d’accepter les connexions à partir de **\<new_data_factory_name\>** et **\<new_integration_runtime_name\>** .
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Créez une fabrique de données nommée **\<new_data_factory_name\>** dans la nouvelle région. Pour plus d’informations, consultez Créer une fabrique de données.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    Pour plus d’informations sur cette commande PowerShell, consultez [Créer une fabrique de données Azure à l’aide de PowerShell](quickstart-create-data-factory-powershell.md)

4. Créez un nouveau runtime d’intégration Azure-SSIS nommé **\<new_integration_runtime_name\>** dans la nouvelle région à l’aide d’Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Pour plus d’informations sur cette commande PowerShell, voir [Créer le runtime d’intégration Azure-SSIS dans Azure Data Factory](create-azure-ssis-integration-runtime.md).


## <a name="next-steps"></a>Étapes suivantes

Il existe d’autres options de configuration pour le runtime d’intégration Azure-SSIS :

- [Configurer Azure-SSIS Integration Runtime pour obtenir de hautes performances](configure-azure-ssis-integration-runtime-performance.md)

- [Personnaliser la configuration du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Approvisionner Enterprise Edition pour Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
