---
author: trngo
description: Découvrez comment supprimer des conteneurs individuels à l’aide d’az-cli
title: Supprimer des conteneurs Fluid
ms.author: trngo
ms.date: 09/28/2021
ms.service: azure-fluid
ms.topic: reference
ms.openlocfilehash: 8a94c06bd80b6997c5bd80cbb147bf68ccf91a4b
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728662"
---
# <a name="delete-fluid-containers-in-microsoft-azure-fluid-relay-server"></a>Supprimer des conteneurs Fluid dans le serveur Relais Azure Fluid Microsoft

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

Dans ce scénario, nous allons supprimer un conteneur Fluid existant. Une fois qu’un conteneur est supprimé, les applications qui font référence au conteneur ne sont plus en mesure d’accéder au conteneur ni à ses données. 

## <a name="requirements-to-delete-a-fluid-container"></a>Conditions requises pour supprimer un conteneur Fluid
- Pour commencer, vous devez installer [Azure CLI](/cli/azure/install-azure-cli). Si Azure CLI est déjà installé, assurez-vous que vous disposez de la version 2.0.67 ou d’une version ultérieure en exécutant `az version`.
- Pour pouvoir supprimer un conteneur Fluid, vous devez vous assurer que votre application et ses clients ne sont plus connectés au conteneur.

## <a name="list-the-containers-within-a-fluid-relay-resource"></a>Répertorier les conteneurs dans une ressource Relais Fluid
Pour afficher tous les conteneurs appartenant à votre ressource Relais Fluid, vous pouvez exécuter la commande suivante :
```
az rest --method get --uri https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.FluidRelay/FluidRelayServers/<frsResourceName>/FluidRelayContainers?api-version=<apiVersion>
```
**subscriptionId** : ID de l’abonnement Azure auquel appartient votre ressource Relais Fluid.

**resourceGroupName** : Nom du groupe de ressources dans lequel se trouve votre ressource Relais Fluid.

**frsResourceName** : Nom de votre ressource Relais Fluid. Notez que c’est différent du tenantId de la ressource Relais Fluid.

**apiVersion** : Version de l’API du fournisseur de ressources. La version minimale prise en charge est **2021-08-30-preview**.  


## <a name="sample-output"></a>Exemple de sortie
La sortie contiendra une liste des conteneurs appartenant à votre ressource Relais Fluid et leurs propriétés.
```json
{
  "value": [
    {
      "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.FluidRelay/fluidRelayServers/<frsResourcename>/fluidRelayContainers/<containerId>",
      "name": "<containerId>",
      "properties": {
        "frsContainerId": "<containerId>",
        "frsTenantId": "<frsResourceTenantId>"
      },
      "resourceGroup": "<resourceGroupname>",
      "type": "Microsoft.FluidRelay/fluidRelayServers/fluidRelayContainers"
    },
    ...
  ]
}
```


## <a name="delete-an-existing-container"></a>Supprimer un conteneur existant
Pour supprimer un conteneur, vous devez identifier le **containerId** du conteneur à partir de la sortie ci-dessus et exécuter la commande suivante : 
```
az rest --method delete --uri https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.FluidRelay/FluidRelayServers/<frsResourceName>/FluidRelayContainers/<frsContainerId>?api-version=<api-version>
```
  **frsContainerId** : ID du conteneur Fluid à supprimer. 
