---
title: Configuration d’un point de terminaison privé (préversion)
titleSuffix: Azure Machine Learning
description: Utilisez Azure Private Link pour accéder en toute sécurité à votre espace de travail Azure Machine Learning à partir d’un réseau virtuel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/28/2020
ms.openlocfilehash: 28fa3fdfdb8915d528d0765bd1fded8128621208
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166516"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Configuration d’Azure Private Link pour un espace de travail Azure Machine Learning (préversion)

Dans ce document, vous allez apprendre à utiliser Azure Private Link avec votre espace de travail Azure Machine Learning. 

> [!IMPORTANT]
> L’utilisation d’Azure Private Link avec un espace de travail Azure Machine Learning est actuellement en préversion publique. Cette fonctionnalité n’est disponible que dans les régions **USA Est** et **USA Ouest 2**. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Private Link vous permet de vous connecter à votre espace de travail à l’aide d’un point de terminaison privé. Le point de terminaison privé est un ensemble d’adresses IP privées au sein de votre réseau virtuel. Vous pouvez alors limiter l’accès à votre espace de travail pour qu’il ne se fasse que sur les adresses IP privées. Azure Private Link permet de réduire le risque d’exfiltration des données. Pour plus d’informations sur les points de terminaison privés, consultez l’article [Azure Private Link](/azure/private-link/private-link-overview).

> [!IMPORTANT]
> Azure Private Link n’a pas d’effet sur le plan de contrôle Azure (opérations de gestion), comme la suppression de l’espace de travail ou la gestion des ressources de calcul. Par exemple, la création, la mise à jour ou la suppression d’une cible de calcul. Ces opérations sont effectuées sur l’Internet public comme d’habitude.
>
> La préversion des instances de calcul Azure Machine Learning n’est pas prise en charge dans un espace de travail où le service Liaison privée est activé.
>
> Il se peut que vous rencontriez des problèmes en tentant d’accéder au point de terminaison privé de votre espace de travail si vous utilisez Mozilla Firefox. Un de ces problème peut être lié à DNS sur HTTPS dans Mozilla. Nous vous recommandons d’utiliser Microsoft Edge ou Google Chrome comme solution de contournement.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Créer un espace de travail qui utilise un point de terminaison privé

> [!IMPORTANT]
> Actuellement, nous prenons uniquement en charge l’activation d’un point de terminaison privé lors de la création d’un espace de travail Azure Machine Learning.

Vous pouvez utiliser le [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) pour créer un espace de travail avec un point de terminaison privé.

Pour plus d’informations sur l’utilisation de ce modèle, y compris les points de terminaison privés, consultez [Utiliser un modèle Azure Resource Manager pour créer un espace de travail pour Azure Machine Learning](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Utilisation d’un espace de travail sur un point de terminaison privé

Étant donné que les communications avec l’espace de travail sont autorisées uniquement à partir du réseau virtuel, tous les environnements de développement qui utilisent l’espace de travail doivent être faire partie du réseau virtuel. Par exemple, une machine virtuelle sur le réseau virtuel.

> [!IMPORTANT]
> Pour éviter toute interruption temporaire de la connectivité, Microsoft recommande de vider le cache DNS sur les ordinateurs qui se connectent à l’espace de travail après avoir activé Azure Private Link. 

Pour plus d’informations sur Machines virtuelles Microsoft Azure, consultez la [documentation relative à Machines Virtuelles](/azure/virtual-machines/).


## <a name="using-azure-storage"></a>Utilisation de Stockage Azure

Pour sécuriser le compte Stockage Azure utilisé par votre espace de travail, placez-le dans le réseau virtuel.

Pour plus d’informations sur la façon de placer le compte de stockage dans le réseau virtuel, consultez [Utiliser un compte de stockage pour votre espace de travail](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace).

> [!WARNING]
> Azure Machine Learning ne prend pas en charge l’utilisation d’un compte Stockage Azure pour lequel une liaison privée est activée.

## <a name="using-azure-key-vault"></a>Utilisation d’Azure Key Vault

Pour sécuriser l’instance Azure Key Vault utilisée par votre espace de travail, vous pouvez la placer dans le réseau virtuel ou activer Azure Private Link pour elle.

Pour plus d’informations sur la façon de placer le coffre de clés dans le réseau virtuel, consultez [Utiliser un coffre de clés pour votre espace de travail](how-to-enable-virtual-network.md#key-vault-instance).

Pour plus d’informations sur l’activation d’Azure Private Link pour le coffre de clés, consultez [Intégrer Key Vault avec Azure Private Link](/azure/key-vault/private-link-service).

## <a name="using-azure-kubernetes-services"></a>Utilisation d’Azure Kubernetes Services

Pour sécuriser les services Azure Kubernetes utilisés par votre espace de travail, placez-le dans un réseau virtuel. Pour plus d’informations, consultez [Utiliser Azure Kubernetes Services avec votre espace de travail](how-to-enable-virtual-network.md#aksvnet).

Azure Machine Learning prend maintenant en charge l’utilisation d’une instance Azure Kubernetes Service avec activation de la liaison privée.
Pour créer un cluster AKS privé, suivez [ces documents](https://docs.microsoft.com/azure/aks/private-clusters).

## <a name="azure-container-registry"></a>Azure Container Registry

Pour plus d’informations sur la sécurisation d’Azure Container Registry dans le réseau virtuel, consultez [Utilisation d’Azure Container Registry](how-to-enable-virtual-network.md#azure-container-registry).

> [!IMPORTANT]
> Si vous utilisez le service Liaison privée pour votre espace de travail Azure Machine Learning et que vous placez l'instance Azure Container Registry de votre espace de travail dans un réseau virtuel, vous devez également appliquer le modèle Azure Resource Manager suivant. Ce modèle permet à votre espace de travail de communiquer avec ACR via le service Liaison privée.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "keyVaultArmId": {
      "type": "string"
      },
      "workspaceName": {
      "type": "string"
      },
      "containerRegistryArmId": {
      "type": "string"
      },
      "applicationInsightsArmId": {
      "type": "string"
      },
      "storageAccountArmId": {
      "type": "string"
      },
      "location": {
      "type": "string"
      }
  },
  "resources": [
      {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "identity": {
          "type": "SystemAssigned"
      },
      "sku": {
          "tier": "enterprise",
          "name": "enterprise"
      },
      "properties": {
          "sharedPrivateLinkResources":
  [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
          "keyVault": "[parameters('keyVaultArmId')]",
          "containerRegistry": "[parameters('containerRegistryArmId')]",
          "applicationInsights": "[parameters('applicationInsightsArmId')]",
          "storageAccount": "[parameters('storageAccountArmId')]"
      }
      }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurisation de votre espace de travail Azure Machine Learning, consultez l’article [Sécurité des entreprises](concept-enterprise-security.md).