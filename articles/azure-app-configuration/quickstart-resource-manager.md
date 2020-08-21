---
title: 'Démarrage rapide : Déploiement automatique d’une machine virtuelle avec Azure App Configuration'
description: Ce guide de démarrage rapide vous montre comment utiliser le module Azure PowerShell et les modèles Azure Resource Manager pour déployer un magasin Azure App Configuration. Il vous montre ensuite comment déployer une machine virtuelle en utilisant les valeurs stockées dans le magasin.
author: lisaguthrie
ms.author: lcozzens
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 9b609d4571d6240f428a0210aa5108ff19dc753b
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235177"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template-arm-template"></a>Démarrage rapide : Déploiement automatique d'une machine virtuelle avec App Configuration et un modèle Resource Manager (modèle ARM)

Apprenez à utiliser les modèles Azure Resource Manager et Azure PowerShell pour déployer un magasin Azure App Configuration, à ajouter des paires clé-valeur dans le magasin et à utiliser les paires clé-valeur du magasin pour déployer une ressource Azure, comme une machine virtuelle Azure dans cet exemple.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="review-the-templates"></a>Passer les modèles en revue

Les modèles utilisés dans ce guide de démarrage rapide sont extraits de [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/). Le [premier modèle](https://azure.microsoft.comresources/templates/101-app-configuration-store/) crée un magasin App Configuration :

:::code language="json" source="~/quickstart-templates/101-app-configuration-store/azuredeploy.json" range="1-37" highlight="27-35":::

Une seule ressource Azure est définie dans le modèle :

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2019-10-01/configurationstores) : crée un magasin App Configuration.

Le [deuxième modèle](https://azure.microsoft.com/resources/templates/101-app-configuration/) crée une machine virtuelle à l'aide des paires clé-valeur du magasin. Avant cette étape, vous devez ajouter des paires clé-valeur à l'aide du portail ou d'Azure CLI.

:::code language="json" source="~/quickstart-templates/101-app-configuration/azuredeploy.json" range="1-217" highlight="77, 181,189":::

## <a name="deploy-the-templates"></a>Déployer les modèles

### <a name="create-an-app-configuration-store"></a>Créer un magasin App Configuration

1. Cliquez sur l’image ci-après pour vous connecter à Azure et ouvrir un modèle. Le modèle crée un magasin App Configuration.

    [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

1. Sélectionnez ou entrez les valeurs suivantes.

    - **Abonnement** : sélectionnez l'abonnement Azure utilisé pour créer le magasin App Configuration.
    - **Groupe de ressources** : sélectionnez **Créer** pour créer un groupe de ressources, sauf si vous souhaitez utiliser un groupe de ressources existant.
    - **Région** : sélectionnez un emplacement pour le groupe de ressources.  Par exemple, **USA Est**.
    - **Nom du magasin de configuration** : entrez un nouveau nom de magasin App Configuration.
    - **Emplacement** : spécifiez l'emplacement du magasin App Configuration.  Utilisez la valeur par défaut.
    - **Nom de la référence SKU** : spécifiez le nom de la référence SKU du magasin App Configuration. Utilisez la valeur par défaut.

1. Sélectionnez **Revoir + créer**.
1. Vérifiez que la page affiche **Contrôle réussi**, puis sélectionnez **Créer**.

Notez le nom du groupe de ressources et le nom du magasin App Configuration.  Vous aurez besoin de ces valeurs lors du déploiement de la machine virtuelle.
### <a name="add-vm-configuration-key-values"></a>Ajouter les paires clé-valeur sur la machine virtuelle

Après avoir créé un magasin App Configuration, vous pouvez utiliser le portail Azure ou Azure CLI pour ajouter des paires clé-valeur dans le magasin.

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez au nouveau magasin App Configuration.
1. Sélectionnez **Explorateur de configurations** dans le menu de gauche.
1. Sélectionnez **Créer** pour ajouter les paires clé-valeur suivantes :

   |Clé|Valeur|Étiquette|
   |-|-|-|
   |windowsOsVersion|2019-Datacenter|template|
   |diskSizeGB|1023|template|

   Laissez **Type de contenu** vide.

Pour utiliser Azure CLI, consultez [Utiliser des paires clé-valeur dans un magasin Azure App Configuration](./scripts/cli-work-with-keys.md).

### <a name="deploy-vm-using-stored-key-values"></a>Déployer une machine virtuelle en utilisant les paires clé-valeur

Maintenant que vous avez ajouté les paires clé-valeur au magasin, vous êtes prêt à déployer une machine virtuelle à l’aide d’un modèle Azure Resource Manager. Le modèle référence les clés **windowsOsVersion** et **diskSizeGB** que vous avez créées.

> [!WARNING]
> Les modèles ARM ne peuvent pas faire référence à des clés dans un magasin App Configuration pour lequel Private Link est activé.

1. Cliquez sur l’image ci-après pour vous connecter à Azure et ouvrir un modèle. Le modèle crée une machine virtuelle à l'aide des paires clé-valeur stockées dans le magasin App Configuration.

    [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration%2Fazuredeploy.json)

1. Sélectionnez ou entrez les valeurs suivantes.

    - **Abonnement** : sélectionnez l'abonnement Azure utilisé pour créer la machine virtuelle.
    - **Groupe de ressources** : spécifiez le même groupe de ressources que pour le magasin App Configuration ou sélectionnez **Créer** pour créer un nouveau groupe de ressources.
    - **Région** : sélectionnez un emplacement pour le groupe de ressources.  Par exemple, **USA Est**.
    - **Emplacement** : spécifiez l'emplacement de la machine virtuelle. Utilisez la valeur par défaut.
    - **Nom d'utilisateur administrateur** : spécifiez un nom d'utilisateur administrateur pour la machine virtuelle.
    - **Mot de passe d'administrateur** : spécifiez un mot de passe d'administrateur pour la machine virtuelle.
    - **Étiquette de nom de domaine** : spécifiez un nom de domaine unique.
    - **Nom du compte de stockage** : spécifiez un nom unique pour un compte de stockage associé à la machine virtuelle.
    - **Groupe de ressources du magasin App Configuration** : spécifiez le groupe de ressources qui contient votre magasin App Configuration.
    - **Nom du magasin App Configuration** : spécifiez le nom de votre magasin Azure App Configuration.
    - **Clé de la référence de la machine virtuelle** : spécifiez **windowsOsVersion**.  Il s'agit du nom de la paire clé-valeur que vous avez ajoutée au magasin.
    - **Clé de la taille du disque** : spécifiez **diskSizeGB**. Il s'agit du nom de la paire clé-valeur que vous avez ajoutée au magasin.

1. Sélectionnez **Revoir + créer**.
1. Vérifiez que la page affiche **Contrôle réussi**, puis sélectionnez **Créer**.

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis accédez à la nouvelle machine virtuelle.
1. Sélectionnez **Vue d'ensemble** dans le menu de gauche et vérifiez que la **référence SKU** est **2019-Datacenter**.
1. Sélectionnez **Disques** dans le menu de gauche et vérifiez que la taille du disque de données est **2013**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, le magasin App Configuration, la machine virtuelle et toutes les ressources associées. Si vous envisagez de réutiliser le magasin App Configuration ou la machine virtuelle plus tard, ne les supprimez pas. Si vous n’avez plus besoin de ce travail, supprimez toutes les ressources créées dans le cadre de ce guide de démarrage rapide en exécutant l’applet de commande suivante :

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle en utilisant un modèle Azure Resource Manager et des paires clé-valeur du magasin Azure App Configuration.

Pour découvrir comment créer d’autres applications avec Azure App Configuration, passez à l’article suivant :

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer une application ASP.NET Core avec Azure App Configuration](quickstart-aspnet-core-app.md)
