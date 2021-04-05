---
title: Créez et activez un plan Azure DDoS Protection avec un modèle Azure Resource Manager (modèle ARM).
description: Découvrez comment créer et activer un plan Azure DDoS Protection avec un modèle Azure Resource Manager (modèle ARM).
services: ddos-protection
documentationcenter: na
author: mumian
ms.service: ddos-protection
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.custom: subject-armqs
ms.author: jgao
ms.date: 01/14/2021
ms.openlocfilehash: 75d6c484a0f1d6325aaa7894d8902ff78cadbd74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99092498"
---
# <a name="quickstart-create-an-azure-ddos-protection-standard-using-arm-template"></a>Démarrage rapide : Créer un plan Azure DDoS Protection Standard avec un modèle ARM

Ce guide de démarrage rapide explique comment utiliser un modèle ARM (Azure Resource Manager) pour créer un plan de protection contre les attaques par déni de service distribué (DDoS) et un réseau virtuel, puis active le plan de protection pour le réseau virtuel. Un plan Azure DDoS Protection Standard définit un ensemble de réseaux virtuels pour lesquels la protection DDoS est activée entre différents abonnements. Vous pouvez configurer un plan de protection DDoS pour votre organisation et lier des réseaux virtuels à partir de plusieurs abonnements au même plan.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-create-and-enable-ddos-protection-plans).

:::code language="json" source="~/quickstart-templates/101-create-and-enable-ddos-protection-plans/azuredeploy.json":::

Le modèle définit deux ressources :

- [Microsoft.Network/ddosProtectionPlans](/azure/templates/microsoft.network/ddosprotectionplans)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)

## <a name="deploy-the-template"></a>Déployer le modèle

Dans cet exemple, le modèle crée un groupe de ressources, un plan de protection DDoS et un réseau virtuel.

1. Pour vous connecter à Azure et ouvrir le modèle, sélectionnez le bouton **Déployer sur Azure**.

    [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

1. Entrez les valeurs pour créer un groupe de ressources, un plan de protection DDoS et un nom de réseau virtuel.

    :::image type="content" source="media/manage-ddos-protection-template/ddos-template.png" alt-text="Modèle de démarrage rapide DDoS.":::

    - **Abonnement**: Nom de l’abonnement Azure sur lequel les ressources seront déployées.
    - **Groupe de ressources** : Sélectionnez ou créez un groupe de ressources.
    - **Région** : Région sur laquelle le groupe de ressources est déployé, par exemple USA Est.
    - **Nom du plan DDoS Protection** : Nom du nouveau plan de protection DDoS.
    - **Nom du réseau virtuel** : Crée un nom pour le nouveau réseau virtuel.
    - **Emplacement** : Fonction qui utilise la même région que le groupe de ressources pour le déploiement des ressources.
    - **Préfixe d’adresse de réseau virtuel** : Utilisez la valeur par défaut ou entrez votre adresse de réseau virtuel.
    - **Préfixe de sous-réseau** : Utilisez la valeur par défaut ou entrez votre sous-réseau de réseau virtuel.
    - **Plan DDoS Protection activé** : La valeur par défaut est `true` pour activer le plan de protection DDoS.

1. Sélectionnez **Revoir + créer**.
1. Vérifiez que la validation du modèle a réussi, puis sélectionnez **Créer** pour commencer le déploiement.

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Pour copier la commande Azure CLI ou Azure PowerShell, sélectionnez le bouton **Copier**. Le bouton **Essayer** a pour effet d’ouvrir Azure Cloud Shell pour exécuter la commande.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli-interactive
az network ddos-protection show \
  --resource-group MyResourceGroup \
  --name MyDdosProtectionPlan
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName 'MyResourceGroup' -Name 'MyDdosProtectionPlan'
```

---

La sortie affiche les nouvelles ressources.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```Output
{
  "etag": "W/\"abcdefgh-1111-2222-bbbb-987654321098\"",
  "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan",
  "location": "eastus",
  "name": "MyDdosProtectionPlan",
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "resourceGuid": null,
  "tags": null,
  "type": "Microsoft.Network/ddosProtectionPlans",
  "virtualNetworks": [
    {
      "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : MyDdosProtectionPlan
Id                : /subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan
Etag              : W/"abcdefgh-1111-2222-bbbb-987654321098"
ProvisioningState : Succeeded
VirtualNetworks   : [
                      {
                        "Id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet"
                      }
                    ]
```

---

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous avez terminé, vous pouvez supprimer les ressources. La commande supprime le groupe de ressources et les ressources qu’il contient.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli-interactive
az group delete --name MyResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'MyResourceGroup'
```

---

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment consulter et configurer la télémétrie pour votre plan de protection DDoS, passez aux tutoriels.

> [!div class="nextstepaction"]
> [Consulter et configurer la télémétrie de la protection DDoS](telemetry.md)
