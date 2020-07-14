---
title: Créer un pool SQL à l’aide d’un modèle Azure Resource Manager
description: Découvrez comment créer un pool SQL Azure Synapse Analytics à l’aide d’un modèle Azure Resource Manager.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 3f84cd92a4c4f96831d39cac783df6710c5ea5f4
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2020
ms.locfileid: "85513421"
---
# <a name="quickstart-create-an-azure-synapse-analytics-sql-pool-by-using-an-arm-template"></a>Démarrage rapide : Créer un pool SQL Azure Synapse Analytics à l’aide d’un modèle ARM

Ce modèle Azure Resource Manager (modèle ARM) crée un pool SQL Azure Synapse Analytics avec Transparent Data Encryption activé. Le pool Synapse SQL fait référence aux fonctionnalités d’entreposage de données d’entreprise qui sont généralement disponibles dans Azure Synapse.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/).

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json" highlight="57-97":::

Le modèle définit une ressource :

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>Déployer le modèle

1. Sélectionnez l’image ci-après pour vous connecter à Azure et ouvrir le modèle. Ce modèle crée un pool Synapse SQL.
   
   [![Déployer sur Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Entrez ou mettez à jour les valeurs suivantes :

   * **Abonnement**: Sélectionnez un abonnement Azure.
   * **Groupe de ressources** : Sélectionnez **Créer**, entrez un nom unique pour le groupe de ressources et sélectionnez **OK**. La création d’un nouveau groupe de ressources facilitera le nettoyage des ressources.
   * **Région** : Sélectionnez une région.  Par exemple, **USA Centre**.
   * **Nom du serveur SQL** : acceptez le nom par défaut ou entrez un nom pour le serveur SQL.
   * **Connexion de l’administrateur SQL** : entrez le nom d’administrateur pour le serveur SQL.
   * **Mot de passe de l’administrateur SQL** : entrez le mot de passe d’administrateur pour le serveur SQL.
   * **Nom de l’entrepôt de données** : entrez un nom de pool SQL.
   * **Chiffrement transparent des données** : conservez la valeur par défaut, Activé. 
   * **Objectif de niveau de service** : acceptez la valeur par défaut, DW400c.
   * **Emplacement** : acceptez l’emplacement par défaut du groupe de ressources.
   * **Examiner et créer** : cochez la case.
   * **Créer** : cochez la case.

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Vous pouvez utiliser le portail Azure pour vérifier les ressources déployées, ou Azure CLI ou un script Azure PowerShell pour lister les ressources déployées.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your Synapse SQL pool exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your SQL pool account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, supprimez le groupe de ressources avec Azure CLI ou Azure PowerShell :

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

Dans le cadre de ce guide de démarrage rapide, vous avez créé un pool SQL Azure Synapse Analytics en utilisant un modèle Resource Manager, puis vous avez vérifié le déploiement. Pour plus d’informations sur Azure Synapse Analytics et Azure Resource Manager, passez aux articles ci-dessous.

- Consulter la [Vue d’ensemble d’Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)
- En savoir plus sur [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Créer et déployer votre premier modèle Resource Manager](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
