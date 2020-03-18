---
title: Déplacer votre ressource de service d'une région vers une autre
titleSuffix: Azure Cognitive Search
description: Cet article explique comment déplacer vos ressources Recherche cognitive Azure d'une région vers une autre dans le cloud Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 03/06/2020
ms.openlocfilehash: 183a937a232dbd28962bb7d6ef42b0d78b8a81fd
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78850684"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Déplacer votre service Recherche cognitive Azure vers une autre région Azure

Parfois, les clients se renseignent sur le déplacement d’un service de recherche existant vers une autre région. Il n’existe aucun mécanisme ou outil intégré pour vous aider dans cette tâche. Elle demeure un processus manuel, présenté ci-dessous dans cet article.

> [!NOTE]
> Dans le portail Azure, tous les services ont une commande **Exporter le modèle**. Dans le cas de la Recherche cognitive Azure, cette commande produit une définition de base d’un service (nom, localisation, niveau, réplica et nombre de partitions), mais ne reconnaît pas le contenu de votre service, pas plus que les clés, les rôles ou les journaux. Bien que la commande existe, nous vous déconseillons de l’utiliser pour déplacer un service de recherche.

## <a name="steps-for-moving-a-service"></a>Étapes pour déplacer un service

Si vous avez besoin de déplacer un service de recherche vers une autre région, votre approche devrait ressembler aux étapes ci-après :

1. Identifiez les services associés pour comprendre l’impact complet du déplacement d’un service. Vous utilisez peut-être le Stockage Azure pour la journalisation, la base de connaissances ou une source de données externe. Vous utilisez peut-être Cognitive Services pour l’enrichissement par IA. L’accès aux services dans d’autres régions est courant, mais il est fourni avec des frais de bande passante supplémentaires. Cognitive Services et la Recherche cognitive Azure doivent se trouver dans la même région si vous utilisez l’enrichissement par IA.

1. Inventoriez votre service existant pour obtenir une liste complète des objets sur le service. Si vous avez activé la journalisation, créez et archivez les rapports dont vous pouvez avoir besoin pour un enregistrement historique.

1. Vérifiez les tarifs et la disponibilité dans la nouvelle région pour garantir la disponibilité de la Recherche cognitive Azure ainsi que des services associés que vous pouvez souhaiter créer dans la même région. Vérifiez la parité des fonctionnalités. Certaines fonctionnalités d’évaluation ont une disponibilité limitée.

1. Créez un service dans la nouvelle région et republiez à partir du code source les index, indexeurs, sources de données, ensembles de compétences, bases de connaissances et mappages de synonymes existants. Les noms de service doivent être uniques afin que vous ne puissiez pas réutiliser le nom existant.

1. Rechargez les index et les bases de connaissances, le cas échéant. Vous allez utiliser du code d’application pour envoyer (push) des données JSON à un index ou réexécuter des indexeurs pour tirer (pull) des documents de sources externes. 

1. Activez la journalisation et, si vous les utilisez, recréez les rôles de sécurité.

1. Mettez à jour les applications clientes et les suites de tests pour utiliser le nouveau nom de service et les clés API, puis testez toutes les applications.

1. Supprimez l’ancien service une fois que le nouveau service est entièrement testé et opérationnel.

## <a name="next-steps"></a>Étapes suivantes

+ [Choisir un niveau](search-sku-tier.md)
+ [Créer un service de recherche](search-create-service-portal.md)
+ [Charger les documents de recherche](search-what-is-data-import.md)
+ [Activer la journalisation](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [incremental enrichment](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual), and [private endpoint](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Create a skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Create a knowledge store](https://docs.microsoft.com/azure/search/knowledge-store-create-portal) -->