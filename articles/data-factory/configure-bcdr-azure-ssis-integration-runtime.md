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
ms.date: 08/14/2018
ms.openlocfilehash: 92f7d25a9c19409b220b6a71fba87da91e51a415
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928495"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Configurer Azure-SSIS Integration Runtime avec la géo-réplication et le basculement Azure SQL Database

Cet article explique comment configurer Azure-SSIS Integration Runtime avec la géo-réplication Azure SQL Database pour la base de données SSISDB. Lorsqu’un basculement se produit, vous pouvez garantir le bon fonctionnement d’Azure-SSIS Integration Runtime avec la base de données secondaire.

Pour plus d’informations sur la géoréplication et le basculement de SQL Database, consultez [Vue d’ensemble : géoréplication active et groupes de basculement automatique](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scénario 1 - Azure-SSIS IR pointe vers le point de terminaison de l’écouteur de lecture-écriture

### <a name="conditions"></a>Conditions

Cette section s’applique lorsque les conditions suivantes sont remplies :

- Azure-SSIS Integration Runtime pointe vers le point de terminaison de l’écouteur de lecture-écriture du groupe de basculement.

  AND

- Le serveur SQL Database n’est *pas* configuré avec la règle de point de terminaison de service de réseau virtuel.

### <a name="solution"></a>Solution

En cas de basculement, il est transparent pour Azure-SSIS Integration Runtime. Azure-SSIS Integration Runtime se connecte automatiquement au nouveau point de terminaison principal du groupe de basculement.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scénario 2 - Azure-SSIS IR pointe vers le point de terminaison du serveur principal

### <a name="conditions"></a>Conditions

Cette section s’applique lorsque l’une des conditions suivantes est remplie :

- Azure-SSIS Integration Runtime pointe vers le point de terminaison du serveur principal du groupe de basculement. Ce point de terminaison change lors du basculement.

  Ou

- Le serveur Azure SQL Database est configuré avec la règle de point de terminaison de service de réseau virtuel.

  Ou

- Le serveur de base de données est une instance SQL Database Managed Instance configurée avec un réseau virtuel.

### <a name="solution"></a>Solution

En cas de basculement, vous devez effectuer les opérations suivantes :

1. Arrêtez le runtime d’intégration Azure-SSIS.

2. Reconfigurez le runtime d’intégration pour qu’il pointe vers le nouveau point de terminaison principal et un réseau virtuel dans la nouvelle région.

3. Redémarrez le runtime d'intégration.

Les sections suivantes décrivent ces étapes plus en détail.

### <a name="prerequisites"></a>Prérequis

- Activez la récupération d’urgence sur votre serveur Azure SQL Database au cas où le serveur subirait une interruption en même temps. Pour plus d’informations, voir [Vue d’ensemble de la continuité d’activité avec Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Si vous utilisez un réseau virtuel dans la région actuelle, utilisez un autre réseau virtuel dans la nouvelle région pour connecter votre runtime d’intégration Azure-SSIS. Pour plus d’information, voir [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md).

- Si vous utilisez une configuration personnalisée, vous devrez peut-être préparer un autre URI SAS pour le conteneur d’objets blob qui stocke votre script de configuration personnalisée et les fichiers associés, de sorte qu’il reste accessible en cas d’interruption. Pour plus d’informations, voir [Configuration personnalisée du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Étapes

Suivez ces étapes pour arrêter votre runtime d’intégration Azure-SSIS, basculez-le dans une nouvelle région et redémarrez-le.

1. Arrêtez le runtime d’intégration dans la région d’origine.

2. Appelez la commande suivante dans PowerShell pour mettre à jour le runtime d’intégration avec les nouveaux paramètres.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Pour plus d’informations sur cette commande PowerShell, voir [Créer le runtime d’intégration Azure-SSIS dans Azure Data Factory](create-azure-ssis-integration-runtime.md).

3. Redémarrez le runtime d’intégration.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scénario 3 : Attachement d’un SSISDB (catalogue SSIS) existant à un nouveau runtime d’intégration Azure-SSIS

Dans le cas d’un incident ADF ou Azure-SSIS IR dans la région en cours, vous pouvez faire en sorte que votre SSISDB continue à fonctionner avec un nouveau runtime d’intégration Azure-SSIS dans une nouvelle région.

### <a name="prerequisites"></a>Prérequis

- Si vous utilisez un réseau virtuel dans la région actuelle, utilisez un autre réseau virtuel dans la nouvelle région pour connecter votre runtime d’intégration Azure-SSIS. Pour plus d’information, voir [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md).

- Si vous utilisez une configuration personnalisée, vous devrez peut-être préparer un autre URI SAS pour le conteneur d’objets blob qui stocke votre script de configuration personnalisée et les fichiers associés, de sorte qu’il reste accessible en cas d’interruption. Pour plus d’informations, voir [Configuration personnalisée du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Étapes

Suivez ces étapes pour arrêter votre runtime d’intégration Azure-SSIS, basculez-le dans une nouvelle région et redémarrez-le.

1. Exécutez une procédure stockée pour attacher SSISDB à **\<new_data_factory_name\>** ou **\<new_integration_runtime_name\>** .
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Créez une fabrique de données nommée **\<new_data_factory_name\>** dans la nouvelle région. Pour plus d’informations, consultez Créer une fabrique de données.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Pour plus d’informations sur cette commande PowerShell, consultez [Créer une fabrique de données Azure à l’aide de PowerShell](quickstart-create-data-factory-powershell.md)

3. Créez un nouveau runtime d’intégration Azure-SSIS nommé **\<new_integration_runtime_name\>** dans la nouvelle région à l’aide d’Azure PowerShell.

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

4. Redémarrez le runtime d’intégration.

## <a name="next-steps"></a>Étapes suivantes

Il existe d’autres options de configuration pour le runtime d’intégration Azure-SSIS :

- [Configurer Azure-SSIS Integration Runtime pour obtenir de hautes performances](configure-azure-ssis-integration-runtime-performance.md)

- [Personnaliser la configuration du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Approvisionner Enterprise Edition pour Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
