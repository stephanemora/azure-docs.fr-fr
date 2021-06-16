---
title: Résolution des exceptions LocationNotFoundForRoleSize lors du déploiement d’un service cloud (classique) sur Azure | Microsoft Docs
description: Cet article explique comment résoudre une exception LocationNotFoundForRoleSize lors du déploiement d’un service cloud (classique) sur Azure.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7938dcb3e66bc618832171668e392e4fff0cf5a9
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110706165"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Résolution des exceptions LocationNotFoundForRoleSize lors du déploiement d’un service cloud (classique) sur Azure

Dans cet article, vous allez résoudre les échecs d’allocation selon lesquels une taille de machine virtuelle n’est pas disponible lors du déploiement d’un service Cloud Azure (classique).

Lorsque vous déployez des instances sur un service cloud (classique) ou ajoutez de nouvelles instances de rôle web ou de rôle de travail, Microsoft Azure alloue des ressources de calcul.

Vous pouvez parfois recevoir des erreurs durant ces opérations avant même d’avoir atteint la limite de votre abonnement Azure.

> [!TIP]
> Les informations qu’il contient peuvent également vous être utiles dans le cadre de la planification du déploiement de vos services.

## <a name="symptom"></a>Symptôme

Dans le portail Azure, accédez à votre service cloud (classique), puis, dans la barre latérale, sélectionnez *Journal des opérations (classique)* pour afficher les journaux.

![Image affichant le panneau Journal des opérations (classique).](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

Quand vous inspectez les journaux de votre service cloud (classique), vous constatez l’exception suivante :

|Type d’exception  |Message d’erreur  |
|---------|---------|
|LocationNotFoundForRoleSize     |L’opération « `{Operation ID}` » a échoué : « Le niveau de machine virtuelle demandé n’est pas disponible actuellement dans la région (`{Region ID}`) pour cet abonnement. Essayez un autre niveau ou effectuez le déploiement à un autre emplacement. »|

## <a name="cause"></a>Cause

Il y a un problème de capacité avec la région ou le cluster sur lequel vous effectuez le déploiement. L’exception *LocationNotFoundForRoleSize* se produit lorsque la référence SKU de ressource sélectionnée (taille de machine virtuelle) n’est pas disponible pour la région spécifiée.

## <a name="solution"></a>Solution

Dans ce scénario, vous devez sélectionner une autre région ou référence SKU pour déployer votre service cloud (classique). Avant de déployer ou de mettre à niveau votre service cloud (classique), vous pouvez déterminer quelles références SKU sont disponibles dans une région ou une zone de disponibilité. Suivez les processus d’[Azure CLI](#list-skus-in-region-using-azure-cli), de [PowerShell](#list-skus-in-region-using-powershell) ou de l’[API REST](#list-skus-in-region-using-rest-api) ci-dessous.

### <a name="list-skus-in-region-using-azure-cli"></a>Lister les références SKU dans la région à l’aide d’Azure CLI

Vous pouvez utiliser la commande [az vm list-skus](/cli/azure/vm?view=azure-cli-latest
#<a name="az_vm_list_skus-command"></a>az_vm_list_skus).

- Utilisez le paramètre `--location` pour filtrer la sortie sur la localisation que vous utilisez.
- Utilisez le paramètre `--size` pour rechercher par un nom partiel.
- Pour plus d’informations, consultez le guide [Résoudre les erreurs de référence SKU non disponible](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli).

    **Par exemple :**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Résultats de l’exemple :** ![Sortie Azure CLI de l’exécution de la commande « az vm list-skus --location southcentralus --size Standard_F --output table », qui affiche les références SKU disponibles.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Lister les références SKU dans la région à l’aide de PowerShell

Vous pouvez utiliser la commande [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku).

- Filtrez les résultats par emplacement.
- Pour cette commande, vous devez disposer de la version la plus récente de PowerShell.
- Pour plus d’informations, consultez le guide [Résoudre les erreurs de référence SKU non disponible](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell).

**Par exemple :**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Autres commandes utiles :**

Filtrer les localisations qui contiennent la taille (Standard_DS14_v2) :

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Filtrer toutes les localisations qui contiennent la taille (v3) :

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>Lister les références SKU dans la région à l’aide de l’API REST

Vous pouvez utiliser l’opération [Resource Skus - List](/rest/api/compute/resourceskus/list). Elle renvoie les références et les régions disponibles dans le format suivant :

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
      <<The rest of your file is located here>>
  ]
}
    
```

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres solutions aux échecs d’allocation et mieux comprendre comment elles sont générées :

> [!div class="nextstepaction"]
> [Échecs d’allocation – Services cloud (classique)](cloud-services-allocation-failures.md)

Si le problème lié à Azure n’est pas traité dans cet article, parcourez les forums Azure sur [MSDN et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez publier votre problème sur ces forums ou [@AzureSupport sur Twitter](https://twitter.com/AzureSupport). Vous pouvez également envoyer une demande de support Azure. Pour envoyer une demande de support sur la page [Prise en charge Azure](https://azure.microsoft.com/support/options/), sélectionnez *Obtenir de l’aide*.
