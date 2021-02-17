---
title: Utiliser un modèle ARM pour configurer la sauvegarde continue et la restauration à un instant dans le passé dans Azure Cosmos DB
description: Découvrez comment approvisionner un compte avec sauvegarde continue et restauration des données à l’aide de modèles Azure Resource Manager.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 642c61414d882b9cfe83f585fda8ff5404e8834a
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538474"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-resource-manager-templates"></a>Configurer et gérer la sauvegarde en continu et la restauration à un instant dans le passé (préversion) – Modèles Azure Resource Manager
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> La fonctionnalité de restauration à un instant dans le passé (mode de sauvegarde continue) pour Azure Cosmos DB est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La fonctionnalité de restauration à un instant dans le passé d’Azure Cosmos DB (préversion) vous permet de récupérer vos données après une modification accidentelle au sein d’un conteneur, de restaurer un compte, une base de données ou un conteneur supprimé ou de restaurer dans n’importe quelle région (où des sauvegardes existent). Le mode de sauvegarde continue vous permet d’effectuer une restauration à n’importe quel instant dans le passé au cours des 30 derniers jours.

Cet article explique comment approvisionner un compte avec sauvegarde continue et restauration des données à l’aide de modèles Azure Resource Manager.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Approvisionner un compte avec sauvegarde continue

Vous pouvez utiliser des modèles Azure Resource Manager pour déployer un compte Azure Cosmos DB en mode continu. Lorsque vous définissez le modèle pour approvisionner un compte, incluez le paramètre « backupPolicy », comme indiqué dans l’exemple suivant :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

Ensuite, déployez le modèle à l’aide d’Azure PowerShell ou de l’interface CLI. L’exemple suivant montre comment déployer le modèle avec une commande CLI :

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>Restaurer à l’aide du modèle Resource Manager

Vous pouvez également restaurer un compte à l’aide du modèle Resource Manager. Lorsque vous définissez le modèle, incluez les paramètres suivants :

* Définissez le paramètre « createMode » sur « Restore ».
* Définissez le paramètre « restoreParameters », et notez que la valeur de « restoreSource » est extraite de la sortie de la commande `az cosmosdb restorable-database-account list` pour votre compte source. L’attribut ID d’instance de votre nom de compte est utilisé pour effectuer la restauration.
* Définissez le paramètre « restoreMode » sur « PointInTime » et configurez la valeur « restoreTimestampInUtc ».

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

Ensuite, déployez le modèle à l’aide d’Azure PowerShell ou de l’interface CLI. L’exemple suivant montre comment déployer le modèle avec une commande CLI :  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>Étapes suivantes

* Configurez et gérez la sauvegarde continue en utilisant [Azure CLI](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-command-line.md) ou [Portail Azure](continuous-backup-restore-portal.md).
* [Modèle de ressource du mode de sauvegarde continue](continuous-backup-restore-resource-model.md).
* [Gérez les autorisations](continuous-backup-restore-permissions.md) requises pour restaurer les données en mode de sauvegarde continue.