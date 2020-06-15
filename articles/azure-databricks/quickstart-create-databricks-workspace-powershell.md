---
title: Démarrage rapide – Créer un espace de travail Azure Databricks à l’aide de PowerShell
description: Ce démarrage rapide montre comment utiliser PowerShell pour créer un espace de travail Azure Databricks.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 06/02/2020
ms.custom: mvc
ms.openlocfilehash: 445832119a113f92dc1da51eb5b5e77dff176fa3
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485687"
---
# <a name="quickstart-create-an-azure-databricks-workspace-using-powershell"></a>Démarrage rapide : Créer un espace de travail Azure Databricks à l’aide de PowerShell

Ce démarrage rapide montre comment utiliser PowerShell pour créer un espace de travail Azure Databricks. Vous pouvez utiliser PowerShell pour créer et gérer des ressources Azure de façon interactive ou dans des scripts.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

Si vous choisissez d’utiliser PowerShell localement, cet article vous demande d’installer le module Az PowerShell et de vous connecter à votre compte Azure avec l’applet de commande [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount). Pour en savoir plus sur l’installation du module Az PowerShell, consultez [Installer Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Tant que le module PowerShell Az.Databricks est en préversion, vous devez l’installer séparément du module Az PowerShell à l’aide de la commande suivante : `Install-Module -Name Az.Databricks -AllowPrerelease`.
> Une fois le module PowerShell Az.Databricks en disponibilité générale, il devient partie intégrante des versions futures du module Az PowerShell et disponible en mode natif dans Azure Cloud Shell.

Si c’est la première fois que vous utilisez le service Azure Databricks, vous devez inscrire le fournisseur de ressources **Microsoft.Databricks**.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Databricks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Si vous avez plusieurs abonnements Azure, sélectionnez l’abonnement approprié dans lequel les ressources doivent être facturées. Sélectionnez un ID d’abonnement spécifique avec l’applet de commande [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) avec l’applet de commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe.

L’exemple suivant crée un groupe de ressources nommé **myresourcegroup** dans la région **USA Ouest 2**.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus2
```

## <a name="create-an-azure-databricks-workspace"></a>Créer un espace de travail Azure Databricks

Dans cette section, vous allez créer un espace de travail Azure Databricks à l’aide de PowerShell.

```azurepowershell-interactive
New-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup -Location westus2 -ManagedResourceGroupName databricks-group -Sku standard
```

Renseignez les valeurs suivantes :

|       **Propriété**       |                                                                                **Description**                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Nom                     | Renseignez un nom pour votre espace de travail Databricks.                                                                                                                                   |
| ResourceGroupName        | Spécifier un nom de groupe de ressources existant                                                                                                                                        |
| Emplacement                 | Sélectionnez **USA Ouest 2**. Pour les autres régions disponibles, consultez [Disponibilité des services Azure par région](https://azure.microsoft.com/regions/services/).                                     |
| ManagedResourceGroupName | Indiquez si vous souhaitez créer un groupe de ressources managé Azure ou utiliser un groupe existant.                                                                                        |
| Sku                      | Choisissez **Standard**, **Premium** ou **Essai**. Pour plus d’informations sur ces niveaux, consultez la [tarification Databricks](https://azure.microsoft.com/pricing/details/databricks/). |

La création de l’espace de travail dure quelques minutes. À l’issue de ce processus, votre compte d’utilisateur est automatiquement ajouté comme utilisateur administrateur dans l’espace de travail.

Lorsque le déploiement d’un espace de travail échoue, l’espace de travail est malgré tout créé en état d’échec. Supprimez l’espace de travail défaillant et créez un espace de travail qui résout les erreurs de déploiement. Lorsque vous supprimez l’espace de travail défaillant, le groupe de ressources managé et toutes les ressources déployées correctement sont également supprimés.

## <a name="determine-the-provisioning-state-of-a-databricks-workspace"></a>Déterminer l’état d’approvisionnement d’un espace de travail Databricks

Pour déterminer si un espace de travail Databricks a été approvisionné avec succès, vous pouvez utiliser la cmdlet `Get-AzDatabricksWorkspace`.

```azurepowershell-interactive
Get-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup |
  Select-Object -Property Name, SkuName, Location, ProvisioningState
```

```Output
Name            SkuName   Location  ProvisioningState
----            -------   --------  -----------------
mydatabricksws  standard  westus2   Succeeded
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez pas besoin des ressources que vous avez créées dans ce démarrage rapide pour un autre démarrage rapide ou un tutoriel, vous pouvez les supprimer en exécutant l’exemple suivant.

> [!CAUTION]
> L’exemple suivant supprime le groupe de ressources spécifié et toutes les ressources qu’il contient.
> Si des ressources en dehors du cadre de ce démarrage rapide existent dans le groupe de ressources spécifié, elles seront également supprimées.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Pour supprimer uniquement le serveur créé dans ce démarrage rapide sans supprimer le groupe de ressources, utilisez l’applet de commande `Remove-AzDatabricksWorkspace`.

```azurepowershell-interactive
Remove-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un cluster Spark dans Databricks](quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)
