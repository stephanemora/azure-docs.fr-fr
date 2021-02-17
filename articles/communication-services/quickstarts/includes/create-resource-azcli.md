---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 1/28/2021
ms.author: mikben
ms.openlocfilehash: d70d53da48f41f0a6a37da6154d29696d3824325
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819999"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Créer une ressource Azure Communication

Pour créer une ressource Azure Communication Services, [connectez-vous à Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli), puis exécutez la commande suivante :

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Vous pouvez configurer votre ressource Communication Services avec les options suivantes :

* Groupe de ressources
* Nom de la ressource Communication Services
* Zone géographique à laquelle associer la ressource

À l’étape suivante, vous pouvez attribuer des étiquettes à la ressource. Les étiquettes peuvent être utiles pour organiser vos ressources Azure. Pour plus d’informations sur les étiquettes, consultez la [documentation sur l’étiquetage des ressources](../../../azure-resource-manager/management/tag-resources.md).

## <a name="manage-your-communication-services-resource"></a>Gérer la ressource Communication Services

Pour ajouter des balises à votre ressource Communication Services, exécutez les commandes suivantes :

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"
```

Pour plus d’informations sur des commandes supplémentaires, consultez [az communication](https://docs.microsoft.com/cli/azure/ext/communication/communication).