---
title: Recommandations de continuité d’activité et reprise d’activité (BCDR) pour Azure-SSIS Integration Runtime | Microsoft Docs
description: Cet article expose des recommandations de continuité d’activité et reprise d’activité pour Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 37347df2d543116085f52fed76c692b60fac2ad6
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295227"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-recommendations-for-azure-ssis-integration-runtime"></a>Recommandations de continuité d’activité et reprise d’activité (BCDR) pour Azure-SSIS Integration Runtime

Pour les besoins de la récupération d’urgence, il est possible d’arrêter le runtime d’intégration Azure-SSIS dans la région dans laquelle il est en cours d’exécution et de basculer vers une autre région pour le redémarrer. Nous vous recommandons pour cela d’utiliser des [régions couplées Azure](../best-practices-availability-paired-regions.md).

## <a name="prerequisites"></a>Prérequis

- Activez la récupération d’urgence sur votre serveur Azure SQL Database au cas où le serveur subirait une interruption en même temps. Pour plus d’informations, voir [Vue d’ensemble de la continuité d’activité avec Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Si vous utilisez un réseau virtuel dans la région actuelle, utilisez un autre réseau virtuel dans la nouvelle région pour connecter votre runtime d’intégration Azure-SSIS. Pour plus d’information, voir [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md).

- Si vous utilisez une configuration personnalisée, vous devrez peut-être préparer un autre URI SAS pour le conteneur d’objets blob qui stocke votre script de configuration personnalisée et les fichiers associés, de sorte qu’il reste accessible en cas d’interruption. Pour plus d’informations, voir [Configuration personnalisée du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="steps"></a>Étapes

Suivez ces étapes pour arrêter votre runtime d’intégration Azure-SSIS, basculez-le dans une nouvelle région et redémarrez-le.

1. Arrêtez le runtime d’intégration dans la région d’origine.

2. Appelez la commande suivante dans PowerShell pour mettre à jour le runtime d’intégration.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Pour plus d’informations sur cette commande PowerShell, voir [Créer le runtime d’intégration Azure-SSIS dans Azure Data Factory](create-azure-ssis-integration-runtime.md).

3. Redémarrez le runtime d’intégration.

## <a name="next-steps"></a>Étapes suivantes

Il existe d’autres options de configuration pour le runtime d’intégration Azure-SSIS :

- [Configurer Azure-SSIS Integration Runtime pour obtenir de hautes performances](configure-azure-ssis-integration-runtime-performance.md)

- [Personnaliser la configuration du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Approvisionner Enterprise Edition pour Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
