---
title: Créer un runtime d’intégration Azure
titleSuffix: Azure Data Factory & Azure Synapse
description: Apprenez à créer un runtime d’intégration Azure dans Azure Data Factory et Azure Synapse Analytics, qui est utilisé pour copier des données et répartir des activités de transformation.
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.date: 08/24/2021
author: lrtoyou1223
ms.author: lle
ms.custom: devx-track-azurepowershell, synapse
ms.openlocfilehash: a9819af196af6df60644a5e25599c6066dc17eaa
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123312345"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Comment créer et configurer un runtime d’intégration Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Le runtime d’intégration (IR) représente l’infrastructure de calcul utilisée par les pipelines Azure Data Factory et Synapse pour fournir des capacités d’intégration de données entre différents environnements réseau. Pour plus d’informations sur le runtime d’intégration, voir [Runtime d’intégration](concepts-integration-runtime.md).

Un runtime d’intégration Azure fournit un calcul entièrement géré pour effectuer en mode natif un déplacement de données et distribuer des activités de transformation de données à des services de calcul tels que HDInsight. Il est hébergé dans l’environnement Azure et prend en charge la connexion à des ressources dans un environnement de réseau public avec des points de terminaison accessibles au public.

Ce document décrit comment créer et configurer un runtime d’intégration Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Runtime d’intégration Azure par défaut
Par défaut, chaque fabrique de données ou espace de travail Synapse a un runtime d’intégration Azure dans le service principal, qui prend en charge les opérations sur les banques de données et les services de calcul en ligne dans un réseau public. L’emplacement de ce runtime d’intégration Azure est résolu automatiquement. Si la propriété **connectVia** n’est pas spécifiée dans la définition du service lié, le runtime d’intégration Azure par défaut est utilisé. Vous devez uniquement créer explicitement un runtime d’intégration Azure lorsque vous souhaitez définir explicitement l’emplacement du runtime d’intégration, ou si vous souhaitez grouper virtuellement les exécutions d’activité sur différents runtimes d’intégration à des fins de gestion. 

## <a name="create-azure-ir"></a>Créer un runtime d’intégration Azure

Pour créer et configurer un Azure IR, vous pouvez suivre les procédures suivantes.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Créer un Azure IR via Azure PowerShell
Vous pouvez créer un runtime d’intégration à l’aide de la cmdlet PowerShell **Set-AzDataFactoryV2IntegrationRuntime**. Pour créer un Azure IR, vous spécifiez le nom, l’emplacement et le type de la commande. Voici un exemple de commande pour créer un runtime d’intégration Azure avec l’emplacement défini sur « West Europe » (Europe Ouest) :

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Pour un runtime d’intégration Azure, le type doit être défini sur **Géré**. Vous n’avez pas besoin spécifier les détails du calcul, car il est entièrement géré de façon élastique dans le cloud. Spécifiez des détails de calcul tels que la taille de nœud et le nombre de nœuds lorsque vous souhaitez créer un runtime d’intégration Azure-SSIS. Pour plus d’informations, voir [Créer et configurer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md).

Vous pouvez configurer un runtime d’intégration Azure existant pour modifier son emplacement à l’aide de la cmdlet PowerShell Set-AzDataFactoryV2IntegrationRuntime. Pour plus d’informations sur l’emplacement d’un runtime d’intégration Azure, voir [Présentation du runtime d’intégration](concepts-integration-runtime.md).

### <a name="create-an-azure-ir-via-ui"></a>Créer un Azure IR via l’interface utilisateur
Procédez comme suit pour créer un Azure IR via l’interface utilisateur.

1. Sur la page d’accueil du service, sélectionnez l’onglet [Gérer](./author-management-hub.md) dans le volet le plus à gauche.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).
    
    :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="Bouton Gérer de la page d’accueil":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/get-started-page-manage-button-synapse.png" alt-text="Bouton Gérer de la page d’accueil":::

2. Sélectionnez **Runtimes d’intégration** dans le volet gauche, puis **+ Nouveau**.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

    :::image type="content" source="media/doc-common-process/manage-new-integration-runtime.png" alt-text="Capture d’écran qui met en surbrillance les runtimes d’intégration dans le volet de gauche et le bouton + Nouveau.":::
   
    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/manage-new-integration-runtime-synapse.png" alt-text="Capture d’écran qui met en surbrillance les runtimes d’intégration dans le volet de gauche et le bouton + Nouveau.":::

3. Sur la page **Configuration du runtime d’intégration**, sélectionnez **Azure, auto-hébergé**, puis **Continuer**. 

1. Sur la page suivante, sélectionnez **Azure** pour créer un Azure IR, puis choisissez **Continuer**.
   ![Créer un runtime d’intégration](media/create-azure-integration-runtime/new-azure-integration-runtime.png)

1. Entrez un nom pour votre Azure IR, puis sélectionnez **Créer**.
   ![Créer un Azure IR](media/create-azure-integration-runtime/create-azure-integration-runtime.png)

1. Une notification contextuelle s’affiche lorsque la création est terminée. Dans la page **Runtimes d’intégration**, assurez-vous que le runtime d’intégration nouvellement créé est répertorié.

## <a name="use-azure-ir"></a>Utiliser un runtime d’intégration Azure

Une fois un runtime d’intégration Azure créé, vous pouvez le référencer dans votre définition de service lié. Voici un exemple de la façon dont vous pouvez référencer le runtime d’intégration Azure créé ci-dessus à partir d’un service lié de Stockage Azure :

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Étapes suivantes
Lisez les articles suivants concernant la création d’autres types de runtimes d’intégration :

- [Créer un runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md)
- [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md)
