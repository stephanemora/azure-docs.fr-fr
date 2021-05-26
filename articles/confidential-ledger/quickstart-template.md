---
title: Créer un Registre confidentiel Microsoft Azure à l’aide d’un modèle Azure Resource Manager
description: Découvrez comment créer un Registre confidentiel Microsoft Azure à l’aide d’un modèle Azure Resource Manager.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.author: mbaldwin
ms.date: 04/15/2021
ms.openlocfilehash: 1110d1441c63f3af1f7c31b9ac090c9693b36be1
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385992"
---
# <a name="quickstart-create-an-microsoft-azure-confidential-ledger-with-an-arm-template"></a>Démarrage rapide : Créer un Registre confidentiel Microsoft Azure avec un modèle ARM

Le [Registre confidentiel Microsoft Azure](overview.md) est un nouveau service, hautement sécurisé, pour la gestion des enregistrements de données sensibles. Ce guide de démarrage rapide se concentre sur le processus de déploiement d’un modèle Azure Resource Manager (modèle ARM ) pour créer un Registre.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-confidential-ledger-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates).

Ressources Azure définies dans le modèle :

- Microsoft.ConfidentialLedger/ledgers

## <a name="deploy-the-template"></a>Déployer le modèle

1. Sélectionnez l’image ci-après pour vous connecter à Azure et ouvrir le modèle.

    [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-confidential-ledger-create%2Fazuredeploy.json)

1. Sélectionnez ou entrez les valeurs suivantes.

    Sauf si la valeur est spécifiée, utilisez la valeur par défaut pour créer le Registre confidentiel.

    - **Nom du registre** : sélectionnez un nom pour votre registre. Un nom de registre doit être globalement unique.
    - **Emplacement** : Sélectionnez un emplacement. Par exemple, **USA Est**.

1. Sélectionnez **Achat**. Une fois la ressource de Registre confidentiel déployée, vous recevez une notification.

Le portail Azure est utilisé pour déployer le modèle. Outre le portail Azure, vous pouvez également utiliser Azure PowerShell, l’interface Azure CLI et l’API REST. Pour découvrir d’autres méthodes de déploiement, consultez [Déployer des modèles](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Vous pouvez utiliser le portail Azure pour vérifier la ressource du registre.

## <a name="clean-up-resources"></a>Nettoyer les ressources

D’autres articles relatifs au Registre confidentiel Microsoft Azure peuvent se baser sur ce démarrage rapide. Si vous prévoyez d’utiliser d’autres démarrages rapides et didacticiels, il peut être utile de conserver ces ressources.

Si vous n’en avez plus besoin, supprimez le groupe de ressources. Cela a pour effet de supprimer la ressource de registre. Pour supprimer le groupe de ressources avec Azure CLI ou Azure PowerShell :

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé une ressource de Registre confidentiel à l’aide d’un modèle ARM, et validé le déploiement. Pour en savoir plus sur le service, consultez [Vue d’ensemble du Registre confidentiel Microsoft Azure](overview.md).


