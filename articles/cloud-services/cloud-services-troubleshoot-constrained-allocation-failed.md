---
title: Résoudre les problèmes liés à ConstrainedAllocationFailed lors du déploiement d’un service cloud sur Azure | Microsoft Docs
description: Cet article explique comment résoudre une exception ConstrainedAllocationFailed lors du déploiement d’un service cloud sur Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: de344bbcd89158676bacf2a8aa1743d282700b9d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520750"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-to-azure"></a>Résoudre les problèmes liés à ConstrainedAllocationFailed lors du déploiement d’un service cloud sur Azure

Dans cet article, vous allez résoudre les échecs d’allocation empêchant le déploiement d’Azure Cloud Services en raison de contraintes.

Microsoft Azure procède à une allocation quand vous effectuez les opérations suivantes :

- Mise à niveau d’instances de services cloud

- Ajout de nouvelles instances de rôle web ou de rôle de travail

- Déploiement d’instances sur un service cloud

Vous pouvez parfois recevoir des erreurs durant ces opérations avant même d’avoir atteint la limite de votre abonnement Azure.

> [!TIP]
> Les informations qu’il contient peuvent également vous être utiles dans le cadre de la planification du déploiement de vos services.

## <a name="symptom"></a>Symptôme

Dans le portail Azure, accédez à votre service cloud puis, dans la barre latérale, sélectionnez *Journaux des opérations (classique)* pour afficher les journaux.

Quand vous inspectez les journaux de votre service cloud, vous constatez l’exception suivante :

|Type d’exception  |Message d’erreur  |
|---------|---------|
|ConstrainedAllocationFailed     |L’opération Azure « `{Operation ID}` » a échoué. Code : Compute.ConstrainedAllocationFailed. Détails : L’allocation a échoué. Impossible de satisfaire aux contraintes spécifiées dans la demande. Le nouveau déploiement de service demandé est lié à un groupe d’affinités ou cible un réseau virtuel, ou un déploiement existant se trouve sous ce service hébergé. Toutes ces conditions réduisent le nouveau déploiement à certaines ressources Azure spécifiques. Réessayez ultérieurement, ou bien essayez de réduire la taille de la machine virtuelle ou le nombre d’instances de rôle. Vous pouvez aussi éventuellement supprimer les contraintes mentionnées précédemment ou essayer de déployer dans une autre région.|

## <a name="cause"></a>Cause

Il y a un problème de capacité avec la région ou le cluster sur lequel vous effectuez le déploiement. Cela se produit quand la référence SKU de ressource que vous avez sélectionnée n’est pas disponible pour la localisation spécifiée.

> [!NOTE]
> Quand le premier nœud d’un service cloud est déployé, il est *épinglé* à un pool de ressources. Un pool de ressources peut être un cluster unique ou un groupe de clusters.
>
> Au fil du temps, les ressources de ce pool de ressources peuvent devenir entièrement utilisées. Si un service cloud effectue une demande d’allocation pour des ressources supplémentaires alors que les ressources disponibles dans le pool de ressources épinglé sont insuffisantes, la demande entraîne un [échec d’allocation](cloud-services-allocation-failures.md).

## <a name="solution"></a>Solution

Dans ce scénario, vous devez sélectionner une autre région ou référence SKU sur laquelle déployer votre service cloud. Avant de déployer ou de mettre à niveau votre service cloud, vous pouvez déterminer quelles références SKU sont disponibles dans une région ou une zone de disponibilité. Suivez les processus d’[Azure CLI](#list-skus-in-region-using-azure-cli), de [PowerShell](#list-skus-in-region-using-powershell) ou de l’[API REST](#list-skus-in-region-using-rest-api) ci-dessous.

### <a name="list-skus-in-region-using-azure-cli"></a>Lister les références SKU dans la région à l’aide d’Azure CLI

Vous pouvez utiliser la commande [az vm list-skus](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus).

- Utilisez le paramètre `--location` pour filtrer la sortie sur la localisation que vous utilisez.
- Utilisez le paramètre `--size` pour rechercher par un nom partiel.
- Pour plus d’informations, consultez le guide [Résoudre les erreurs de référence SKU non disponible](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli).

    **Par exemple :**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Résultats de l’exemple :** ![Sortie Azure CLI de l’exécution de la commande « az vm list-skus --location southcentralus --size Standard_F --output table », qui affiche les références SKU disponibles.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Lister les références SKU dans la région à l’aide de PowerShell

Vous pouvez utiliser la commande [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku).

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

Vous pouvez utiliser l’opération [Resource Skus - List](https://docs.microsoft.com/rest/api/compute/resourceskus/list). Elle renvoie les références et les régions disponibles dans le format suivant :

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
    <Rest_of_your_file_is_located_here...>
  ]
}
    
```

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres solutions aux échecs d’allocation et mieux comprendre comment elles sont générées :

> [!div class="nextstepaction"]
> [Échecs d’allocation (services cloud)](cloud-services-allocation-failures.md)

Si le problème lié à Azure n’est pas traité dans cet article, parcourez les forums Azure sur [MSDN et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez publier votre problème sur ces forums ou [@AzureSupport sur Twitter](https://twitter.com/AzureSupport). Vous pouvez également envoyer une demande de support Azure. Pour envoyer une demande de support sur la page [Prise en charge Azure](https://azure.microsoft.com/support/options/), sélectionnez *Obtenir de l’aide*.
