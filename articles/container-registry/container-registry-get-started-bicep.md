---
title: Démarrage rapide – Créer un registre – Bicep
description: Découvrez comment créer un registre de conteneurs Azure à l’aide d’un fichier Bicep.
services: azure-resource-manager
author: mumian
ms.author: jgao
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: azure-resource-manager
ms.openlocfilehash: 689048afd913cf081f993a5238c42f0914119acb
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129095141"
---
# <a name="quickstart-create-a-container-registry-by-using-a-bicep-file"></a>Démarrage rapide : Créer un registre de conteneurs à l’aide d’un fichier Bicep

Ce guide de démarrage rapide montre comment créer une instance Azure Container Registry à l’aide d’un fichier Bicep.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-bicep-introduction.md)]

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="review-the-bicep-file"></a>Examiner le fichier Bicep

Utilisez Visual Studio Code ou votre éditeur favori pour créer un fichier avec le contenu suivant, puis nommez-le **main.bicep** :

```bicep
@minLength(5)
@maxLength(50)
@description('Provide a globally unique name of your Azure Container Registry')
param acrName string = 'acr${uniqueString(resourceGroup().id)}'

@description('Provide a location for the registry.')
param location string = resourceGroup().location

@description('Provide a tier of your Azure Container Registry.')
param acrSku string = 'Basic'

resource acrResource 'Microsoft.ContainerRegistry/registries@2021-06-01-preview' = {
  name: acrName
  location: location
  sku: {
    name: acrSku
  }
  properties: {
    adminUserEnabled: false
  }
}
```

La ressource suivante est définie dans le fichier Bicep :

* **[Microsoft.ContainerRegistry/registries](/azure/templates/microsoft.containerregistry/registries)**  : création d’un registre de conteneurs Azure

Vous trouverez d’autres exemples de modèles Azure Container Registry dans la [galerie de modèles de démarrage rapide](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

## <a name="deploy-the-bicep-file"></a>Déployer le fichier Bicep

Pour déployer le fichier que vous avez créé, ouvrez PowerShell ou Azure CLI. Si vous souhaitez utiliser le terminal Visual Studio Code intégré, sélectionnez la combinaison de touches `ctrl` + ```` ` ````. Remplacez le répertoire actif par le répertoire où se trouve le fichier Bicep.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli
az group create --name myContainerRegRG --location centralus

az deployment group create --resource-group myContainerRegRG --template-file main.bicep --parameters acrName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name myContainerRegRG -Location centralus

New-AzResourceGroupDeployment -ResourceGroupName myContainerRegRG -TemplateFile ./main.bicep -acrName "{your-unique-name}"
```

---

> [!NOTE]
> Remplacez **{your-unique-name}** , y compris les accolades, par un nom de registre de conteneurs unique.

Une fois le déploiement terminé, un message doit s’afficher pour indiquer que le déploiement a réussi.

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Utilisez le portail Azure ou un outil tel que l’interface de ligne de commande Azure pour passer en revue les propriétés du registre de conteneurs.

1. Dans le portail, recherchez **Registres de conteneurs**, puis sélectionnez le registre de conteneurs que vous avez créé.

1. Sur la page **Vue d’ensemble**, notez le **serveur de connexion** du registre. Utilisez cet URI quand vous utilisez Docker pour étiqueter et envoyer (push) des images à votre registre. Pour plus d’informations,consultez [Transmettre votre première image à l’aide de l’interface de ligne de commande (CLI) Docker](container-registry-get-started-docker-cli.md).

    :::image type="content" source="media/container-registry-get-started-bicep/registry-overview.png" alt-text="Vue d’ensemble du registre":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin de la ressource, supprimez le groupe de ressources et le registre. Pour ce faire, sélectionnez le groupe de ressources qui contient le registre dans le portail Azure, puis sélectionnez **Supprimer le groupe de ressources**.

Supprimer le groupe de ressources

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une instance Azure Container Registry avec un fichier Bicep. Passez à présent au tutoriel sur Azure Container Registry (ACR) pour approfondir vos connaissances.

> [!div class="nextstepaction"]
> [Tutoriels sur Azure Container Registry](container-registry-tutorial-prepare-registry.md)

Pour obtenir un tutoriel pas à pas qui vous guide tout au long du processus de création d’un fichier Bicep, consultez :

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code](../azure-resource-manager/bicep/quickstart-create-bicep-use-visual-studio-code.md)
