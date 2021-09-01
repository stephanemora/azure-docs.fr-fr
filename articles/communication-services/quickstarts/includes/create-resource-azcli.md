---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: rifox
ms.openlocfilehash: 06e37015bb95c3bad54f7eb46b7dd8991b6b05bb
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966451"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/dotnet/).
- Installer [l’interface de ligne de commande Azure](/cli/azure/install-azure-cli-windows?tabs=azure-cli) 

## <a name="create-azure-communication-services-resource"></a>Créer une ressource Azure Communication Services

Pour créer une ressource Azure Communication Services, [connectez-vous à Azure CLI](/cli/azure/authenticate-azure-cli). Pour ce faire, vous pouvez utiliser le terminal et la commande ```az login```, puis fournir vos informations d’identification. Exécutez la commande suivante pour créer la ressource :

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Si vous souhaitez sélectionner un abonnement particulier, vous pouvez également spécifier l’indicateur ```--subscription``` et fournir l’ID d’abonnement.
```
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup> --subscription "<subscriptionID>"
```

Vous pouvez configurer votre ressource Communication Services avec les options suivantes :

* Groupe de ressources
* Nom de la ressource Communication Services
* Zone géographique à laquelle associer la ressource

À l’étape suivante, vous pouvez attribuer des étiquettes à la ressource. Les étiquettes peuvent être utiles pour organiser vos ressources Azure. Pour plus d’informations sur les étiquettes, consultez la [documentation sur l’étiquetage des ressources](../../../azure-resource-manager/management/tag-resources.md).

## <a name="manage-your-communication-services-resource"></a>Gérer la ressource Communication Services

Pour ajouter des balises à votre ressource Communication Services, exécutez les commandes suivantes : Vous pouvez également cibler un abonnement spécifique.

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal1" --resource-group "<resourceGroup>"

az communication update --name "<communicationName>" --tags newTag="newVal2" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"
```

Pour plus d’informations sur des commandes supplémentaires, consultez [az communication](/cli/azure/communication).