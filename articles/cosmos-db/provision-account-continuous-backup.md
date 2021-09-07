---
title: Provisionner un compte avec la sauvegarde continue et la restauration à un instant dans le passé dans Azure Cosmos DB
description: Apprenez à provisionner un compte avec la sauvegarde continue et la restauration à un instant dans le passé en utilisant le portail Azure, PowerShell, CLI et les modèles Resource Manager.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 78996e58111d380778e8d02673f518720250e6fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563226"
---
# <a name="provision-an-azure-cosmos-db-account-with-continuous-backup-and-point-in-time-restore"></a>Provisionner un compte Azure Cosmos DB avec la sauvegarde continue et la restauration à un instant dans le passé 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

La fonctionnalité de restauration à un instant dans le passé d’Azure Cosmos DB vous permet de récupérer vos données après une modification accidentelle au sein d’un conteneur, de restaurer un compte, une base de données ou un conteneur supprimé, ou de restaurer dans une région (où des sauvegardes existent). Le mode de sauvegarde continue vous permet d’effectuer une restauration à n’importe quel instant dans le passé au cours des 30 derniers jours.

Cet article explique comment provisionner un compte avec la sauvegarde continue et la restauration à un instant dans le passé en utilisant le [portail Azure](#provision-portal), [PowerShell](#provision-powershell), [CLI](#provision-cli) et des [modèles Resource Manager](#provision-arm-template).

> [!NOTE]
> Vous pouvez provisionner le compte en mode de sauvegarde continue uniquement si les conditions suivantes sont remplies :
>
> * Si le compte est de type API SQL ou API pour MongoDB.
> * Si le compte a une seule région d’écriture.
> * Si le compte n’est pas activé avec des clés gérées par le client (CMK).

## <a name="provision-using-azure-portal"></a><a id="provision-portal"></a>Provisionner avec le portail Azure

Lorsque vous créez un compte Azure Cosmos DB, sous l’onglet **Stratégie de sauvegarde**, choisissez le mode **continu** pour activer la fonctionnalité de restauration à un instant dans le passé pour le nouveau compte. Avec la restauration à un instant dans le passé, les données sont restaurées sur un nouveau compte. Actuellement, vous ne pouvez pas effectuer de restauration sur un compte existant.

:::image type="content" source="./media/provision-account-continuous-backup/configure-continuous-backup-portal.png" alt-text="Approvisionner un compte Azure Cosmos DB avec une configuration de sauvegarde continue." border="true" lightbox="./media/provision-account-continuous-backup/configure-continuous-backup-portal.png":::

## <a name="provision-using-azure-powershell"></a><a id="provision-powershell"></a>Provisionner avec Azure PowerShell

Avant de provisionner le compte, installez la [dernière version d’Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) ou une version supérieure à 6.2.0. Connectez-vous ensuite à votre compte Azure et sélectionnez l’abonnement requis avec les commandes suivantes :

1. Connectez-vous à Azure en utilisant la commande suivante :

   ```azurepowershell
   Connect-AzAccount
   ```

1. Sélectionnez un abonnement spécifique avec la commande suivante :

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

#### <a name="sql-api-account"></a><a id="provision-powershell-sql-api"></a>Compte API SQL

Pour approvisionner un compte avec sauvegarde continue, ajoutez un argument `-BackupPolicyType Continuous` à la commande d’approvisionnement standard.

L’applet de commande suivante est un exemple de compte d’écriture monorégion appelé *Pitracct* avec une stratégie de sauvegarde continue créée dans la région *USA Ouest* sous le groupe de ressources *MyRG* :

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "MyRG" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct" `
  -ApiKind "Sql"
      
```

#### <a name="api-for-mongodb"></a><a id="provision-powershell-mongodb-api"></a>API pour MongoDB

L’applet de commande suivante est un exemple de compte de sauvegarde continue *Pitracct* créé dans la région *USA Ouest* sous le groupe de ressources *MyRG* :

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "MyRG" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "Pitracct" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="provision-using-azure-cli"></a><a id="provision-cli"></a>Provisionner avec Azure CLI

Avant de provisionner le compte, installez Azure CLI en suivant ces étapes :

1. Installez la dernière version d’Azure CLI.

   * Installez la dernière version d’[Azure CLI](/cli/azure/install-azure-cli) ou une version supérieure à 2.26.0
   * Si vous avez déjà installé l’interface CLI, exécutez la commande `az upgrade` pour effectuer la mise à jour vers la dernière version. Cette commande ne fonctionnera qu’avec une version de CLI supérieure à la version 2.11. Si vous disposez d’une version antérieure, utilisez le lien ci-dessus pour installer la dernière version.

1. Se connecter et sélectionner votre abonnement

   * Connectez-vous à votre compte Azure à l’aide de la commande `az login`.
   * Sélectionnez l’abonnement requis à l’aide de la commande `az account set -s <subscriptionguid>`.

### <a name="sql-api-account"></a><a id="provision-cli-sql-api"></a>Compte API SQL

Pour approvisionner un compte d’API SQL avec sauvegarde continue, un argument supplémentaire `--backup-policy-type Continuous` doit être transmis avec la commande d’approvisionnement standard. La commande suivante est un exemple de compte d’écriture monorégion appelé *Pitracct* avec une stratégie de sauvegarde continue créée dans la région *USA Ouest* sous le groupe de ressources *MyRG* :

```azurecli-interactive

az cosmosdb create \
  --name Pitracct \
  --resource-group MyRG \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

### <a name="api-for-mongodb"></a><a id="provision-cli-mongo-api"></a>API pour MongoDB

La commande suivante montre un exemple de compte d’écriture monorégion appelé *Pitracct* avec une stratégie de sauvegarde continue créée dans la région *USA Ouest* sous le groupe de ressources *MyRG* :

```azurecli-interactive

az cosmosdb create \
  --name Pitracct \
  --kind MongoDB \
  --resource-group MyRG \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-using-resource-manager-template"></a><a id="provision-arm-template"></a>Provisionner avec un modèle Resource Manager

Vous pouvez utiliser des modèles Azure Resource Manager pour déployer un compte Azure Cosmos DB en mode continu. Quand vous définissez le modèle pour provisionner un compte, incluez le paramètre `backupPolicy`, comme indiqué dans l’exemple suivant :

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

## <a name="next-steps"></a>Étapes suivantes

* [Restaurer un compte Azure Cosmos DB actif ou supprimé](restore-account-continuous-backup.md)
* [Guide pratique pour migrer vers un compte à partir d’une sauvegarde périodique vers une sauvegarde continue](migrate-continuous-backup.md).
* [Modèle de ressource du mode de sauvegarde continue.](continuous-backup-restore-resource-model.md)
* [Gérez les autorisations](continuous-backup-restore-permissions.md) requises pour restaurer les données en mode de sauvegarde continue.