---
title: Déployer des fichiers Bicep avec Cloud Shell
description: Utilisez Azure Resource Manager et Azure Cloud Shell pour déployer des ressources sur Azure. Les ressources sont définies dans le fichier Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: fa74250a290cc3e254e5c9f3757bb3e712c020da
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025875"
---
# <a name="deploy-bicep-files-from-azure-cloud-shell"></a>Déployer des modèles à partir d'Azure Cloud Shell

Vous pouvez utiliser [Azure Cloud Shell](../../cloud-shell/overview.md) pour déployer un fichier Bicep. Actuellement, vous pouvez uniquement déployer un fichier Bicep local à partir de Cloud Shell.

Vous pouvez effectuer le déploiement sur n’importe quelle étendue. Cet article présente un déploiement sur un groupe de ressources.

## <a name="deploy-local-bicep-file"></a>Déployer un fichier Bicep local

Pour déployer un fichier Bicep local, vous devez d’abord charger votre fichier Bicep dans votre session Cloud Shell.

1. Connectez-vous à [Cloud Shell](https://shell.azure.com).
1. Sélectionnez **PowerShell** ou **Bash**.

    :::image type="content" source="./media/deploy-cloud-shell/bicep-cloud-shell-bash-powershell.png" alt-text="Sélectionnez Bash ou PowerShell":::

1. Sélectionnez **Charger/Télécharger des fichiers**, puis **Charger**.

    :::image type="content" source="./media/deploy-cloud-shell/bicep-cloud-shell-upload.png" alt-text="Charger un fichier":::

1. Sélectionnez le fichier Bicep que vous souhaitez télécharger, puis sélectionnez **Ouvrir**.
1. Pour déployer le fichier Bicep, exécutez les commandes suivantes :

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.bicep \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.bicep `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les commandes de déploiement, consultez [Déployer des ressources à l’aide de Bicep et l’interface de ligne de commande Azure](deploy-cli.md) et [Déployer des ressources à l’aide de Bicep et d’Azure PowerShell](deploy-powershell.md).
- Pour afficher un aperçu des modifications avant de déployer un fichier Bicep, consultez l’opération de simulation d’un [déploiement Bicep](./deploy-what-if.md).
