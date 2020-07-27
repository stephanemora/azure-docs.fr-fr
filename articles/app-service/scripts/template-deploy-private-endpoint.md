---
title: Avec ce modèle Azure Resource Manager, vous serez en mesure de déployer un point de terminaison privé pour une application web.
description: Découvrez comment utiliser un modèle ARM pour déployer un point de terminaison privé pour votre application web.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ff2d59ed569037f34e24a69ffafa0df237a3de34
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524826"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-resource-manager-template"></a>Créer une application App Service et déployer un point de terminaison privé à l’aide d’un modèle Azure Resource Manager

Dans ce guide de démarrage rapide, vous allez utiliser un modèle Azure Resource Manager pour créer une application web et l’exposer avec un point de terminaison privé.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Configuration requise

Vous devez avoir un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>Créer un Private Endpoint

Ce modèle crée un point de terminaison privé pour une application web Azure.

### <a name="review-the-template"></a>Vérifier le modèle

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Déployer le modèle

Voici comment déployer le modèle Azure Resource Manager sur Azure :

1. Pour vous connecter à Azure et ouvrir le modèle, sélectionnez **Déployer sur Azure**. Le modèle crée le réseau virtuel, l’application web, le point de terminaison privé et la zone DNS privée.

   [Déployer dans Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json)

2. Sélectionnez ou créez votre groupe de ressources.
3. Spécifiez le nom de votre application web, le plan App Service et le point de terminaison privé.
5. Lisez les clauses des conditions générales. Si vous acceptez, sélectionnez J’accepte les conditions générales mentionnées ci-dessus > Acheter. Le déploiement peut prendre plusieurs minutes.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin des ressources que vous avez créées avec le point de terminaison privé, supprimez le groupe de ressources. Cette action supprime le point de terminaison privé et toutes les ressources qui y sont associées.

Pour supprimer le groupe de ressources, appelez l’applet de commande `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Étapes suivantes

- Vous trouverez d’autres modèles Azure Resource Manager pour Azure App Service Web Apps dans les [exemples de modèle ARM](../samples-resource-manager-templates.md).
